# 专家确认、经验入库与结构化表达

## 目标

专家经验不能只停留在聊天记录、会议纪要或口头判断里。这个文档定义专家确认如何变成结构化内容、如何入库、如何进一步转成 Skill 约束、测试指标和可复用经验。

核心链路是：

`专家判断 -> 结构化断言 -> 经验卡片 -> 指标规则 -> 测试用例 -> Skill 约束 -> 后续 Agent 调度`

## 专家确认的结构化表达

专家确认不是简单写一句“这个结果可以”。需要拆成可追踪字段：

```yaml
expert_assertion_id: EXP_2026_001
project_id: sonar_agent
module_id: M03_bottom_detection
reviewer_role: acoustic_expert
review_time: 2026-05-25
context:
  data_case: bottom_case_007
  frequency: 38kHz
  scenario: shallow_water_noisy_bottom
observation: "底线在 18-22 m 区间出现跳变，但主要鱼群层未被误切。"
decision: conditional_pass
confidence: medium
conditions:
  - "仅适用于该航段相似底质和噪声条件"
  - "需要在后续批处理报告中标记底线低置信区域"
metric_binding:
  primary_metric: bottom_depth_mae_m
  observed_value: 0.82
  threshold: 1.0
risk:
  - "强噪声区可能误判为底线"
follow_up:
  - "增加 continuity break count 指标"
  - "补充 noisy/failure 测试用例"
evidence_refs:
  - validation_report: val_M03_2026_007
  - figure: artifacts/bottom_case_007.png
status: accepted_for_experience_db
```

## 入库对象

### 1. expert_assertions

记录专家对一次结果、一次失败、一个阈值或一个边界情况的判断。

| 字段 | 含义 |
|---|---|
| assertion_id | 专家判断唯一 ID |
| module_id | 关联算法模块 |
| data_case | 关联数据 case |
| observation | 专家观察 |
| decision | pass / conditional_pass / reject / needs_more_data |
| confidence | high / medium / low |
| metric_binding | 关联指标、观测值和阈值 |
| conditions | 适用条件 |
| risk | 风险和失败模式 |
| evidence_refs | 图件、报告、数据和代码引用 |

### 2. experience_cards

将一次专家判断抽象成可复用经验。

```yaml
experience_card_id: EXP_CARD_bottom_noise_001
problem: "强噪声区底线跳变"
scenario:
  frequency: 38kHz
  environment: shallow_water
  data_pattern: noisy_bottom_with_discontinuity
recommended_action:
  skill: M03_bottom_detection
  parameter_hint:
    continuity_penalty: increase
    min_bottom_depth_m: 8
constraints:
  - "bottom line should not cross high-confidence fish layer"
  - "low confidence segments must be flagged"
metrics:
  - bottom_depth_mae_m
  - continuity_break_count
  - low_confidence_segment_ratio
failure_modes:
  - "noise ridge treated as bottom"
  - "bottom line cuts through biological layer"
source_assertions:
  - EXP_2026_001
review_status: approved
```

### 3. metric_rules

将经验转成可计算的指标规则。

```yaml
metric_rule_id: METRIC_bottom_continuity_001
metric_name: continuity_break_count
module_scope:
  - M03_bottom_detection
formula: "count(abs(diff(bottom_depth_m)) > depth_jump_threshold_m)"
unit: count
thresholds:
  pass: "<= 3 per 1000 pings"
  warn: "4-8 per 1000 pings"
  fail: "> 8 per 1000 pings"
required_inputs:
  - bottom_depth_m
  - ping_time
expert_source:
  - EXP_CARD_bottom_noise_001
```

## 从经验到 Skill 约束

| 经验内容 | 结构化结果 | 进入 Skill 的位置 |
|---|---|---|
| 哪些场景适用 | scenario / conditions | Skill description / limitations |
| 哪些参数有效 | parameter_hint | Parameters / defaults |
| 哪些结果不可信 | failure_modes / risk | Warnings / validation rules |
| 需要看哪些指标 | metric_rules | Evaluation section |
| 需要补哪些数据 | follow_up test cases | test_data manifest |

## 入库流程

1. Agent 生成处理结果、指标和可视化图件。
2. AI 预审给出初步判断和疑点。
3. 专家复审，按 expert_assertion 结构填写判断。
4. 系统将多个 expert_assertion 聚合成 experience_card。
5. 可计算的部分转成 metric_rule 和 validation_case。
6. 已确认经验回写到 Skill manifest 的 limitations、parameters、evaluation 和 test cases。
7. 后续 Agent 调度时，Skill Retriever 和 Contract Checker 会引用这些经验。

## 经验质量指标

| 指标 | 计算方式 | 说明 |
|---|---|---|
| 专家判断结构化率 | `structured_assertions / total_reviews` | 判断是否从口头经验变成可用数据 |
| 经验卡片采纳率 | `approved_experience_cards / generated_experience_cards` | 经验抽象是否准确 |
| 指标规则转化率 | `metric_rules_created / approved_experience_cards` | 经验是否变成可计算规则 |
| 回归用例转化率 | `validation_cases_created / approved_experience_cards` | 经验是否进入测试体系 |
| 后续命中率 | `retrieved_experience_cards / relevant_failures` | 经验是否能在后续失败中被用到 |
| 专家一致性 | `same_decision_reviews / multi_reviewer_cases` | 多专家判断是否稳定 |

## 决策状态

| 状态 | 含义 | 后续动作 |
|---|---|---|
| pass | 专家确认可用 | 可进入交付或模块注册 |
| conditional_pass | 指标基本通过但有适用条件 | 写入 limitations 和 report warning |
| needs_more_data | 无法判断 | 补充测试数据或参考输出 |
| reject | 不可用 | 进入 failure signature 和探索队列 |
| reserve | 当前不用但有参考价值 | 入技术储备或经验库 |
