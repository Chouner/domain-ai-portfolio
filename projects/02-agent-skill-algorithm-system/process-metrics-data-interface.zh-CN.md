# 流程指标、数据治理与接口设计

## 目标

这个文档定义声学 Agent 项目中“流程如何量化、数据如何制定、接口如何约束”。它服务三个目的：

1. 让 Agent 调度和算法模块有稳定输入输出。
2. 让测试结果可以被复现、比较和追踪。
3. 让项目效果可以用指标表达，而不是只靠主观描述。

## 流程指标体系

| 类别 | 指标 | 计算方式 | 用途 |
|---|---|---|---|
| 任务效率 | 需求到方案耗时 | `solution_ready_at - request_at` | 衡量 Agent 方案生成效率 |
| 开发效率 | 模块交付周期 | `validation_report_at - contract_confirmed_at` | 衡量 Skill 化开发效率 |
| 复用程度 | Skill 复用率 | `reused_skills / total_executed_skills` | 判断经验沉淀是否有效 |
| 自动化程度 | 人工介入次数 | 阻塞性人工确认事件计数 | 衡量流程自动化水平 |
| 稳定性 | Pipeline 成功率 | `successful_runs / total_runs` | 衡量批处理可靠性 |
| 返工成本 | 修复轮次 | validation-remediation 循环次数 | 衡量算法成熟度 |
| 知识利用 | 技术储备命中率 | `useful_retrievals / retrieval_attempts` | 衡量知识库是否能支撑开发 |
| 决策质量 | 专家采纳率 | `accepted_ai_suggestions / reviewed_suggestions` | 衡量 AI 预审质量 |

## 算法与数据指标

| 模块 | 主指标 | 辅助指标 | 推荐阈值 |
|---|---|---|---:|
| raw-to-Sv | RMSE / MAE / p95 abs error | valid sample count、max abs error | RMSE < 0.5 dB |
| 去噪 | mask IoU / SNR gain | false removal rate、valid retention | IoU > 0.85 |
| 表层线/底线 | depth MAE | p95 depth error、break count | MAE < 1 m |
| EDSU/NASC | relative error | valid cell ratio、missing cell count | < 5% |
| AI 回波图预审 | precision / recall | false alarm rate、reviewer agreement | agreement > 90% |
| 端到端流程 | artifact completeness | runtime per raw、manual intervention | 持续改善 |

## 数据分层

| 数据层 | 内容 | 是否可变 | 说明 |
|---|---|---|---|
| raw layer | EK80/EK60 raw、原始 GPS、原始参考导出 | 不可变 | 只读保存，计算 hash |
| reference layer | Echoview CSV、人工标注、专家确认线/区域 | 版本化 | 作为验证基准 |
| artifact layer | Sv、TS、Power、mask、line、NASC、图件 | 可再生成 | 由模块运行产生 |
| metric layer | metric_result、validation_report、failure_signature | 版本化 | 用于对比和决策 |
| experience layer | expert_assertion、experience_card、metric_rule | 版本化 | 用于知识回流和 Skill 约束 |
| delivery layer | 报告、可视化、飞书交付包 | 可再生成 | 面向项目交付 |

## 数据 manifest

每个数据集或测试用例都应该有 manifest，避免后续无法复现。

```yaml
case_id: M01_raw_to_sv_case_001
module_id: M01_raw_to_sv
frequency: 38kHz
source_files:
  - path: raw/xxx.raw
    sha256: "..."
reference_files:
  - path: reference/echoview_sv.csv
    sha256: "..."
input_profile:
  raw_format: EK80
  ping_count: 1596
  sample_count: 54541
  no_data_value: null
parameters:
  calibration_source: embedded_or_reference
  tvg_mode: echoview_style
expected_metrics:
  sv_rmse_db: "< 0.5"
  sv_mae_db: "record"
  p95_abs_diff_db: "record"
review_status: pending | accepted | rejected
```

## 标准接口

### 1. Skill 输入接口

```json
{
  "task_id": "run_2026_001",
  "module_id": "M01_raw_to_sv",
  "input_artifacts": [
    {"type": "raw", "path": "...", "sha256": "..."},
    {"type": "gps", "path": "...", "sha256": "..."}
  ],
  "parameters": {
    "frequency": "38kHz",
    "calibration_source": "embedded",
    "no_data_policy": "nan"
  },
  "expected_outputs": ["sv", "metadata", "validation_report"]
}
```

### 2. Skill 输出接口

```json
{
  "task_id": "run_2026_001",
  "module_id": "M01_raw_to_sv",
  "status": "success",
  "artifacts": [
    {"type": "sv", "path": "artifacts/sv.zarr", "sha256": "..."},
    {"type": "metadata", "path": "artifacts/metadata.json", "sha256": "..."}
  ],
  "metrics": {
    "valid_sample_count": 87047436,
    "sv_rmse_db": 0.050,
    "sv_mae_db": 0.0059,
    "p95_abs_diff_db": 0.0038
  },
  "warnings": [],
  "next_recommended_action": "expert_review"
}
```

### 3. 验证结果接口

```json
{
  "validation_id": "val_M01_2026_001",
  "module_id": "M01_raw_to_sv",
  "reference": "echoview_export",
  "level": "L2_numeric",
  "pass": true,
  "thresholds": {
    "sv_rmse_db": 0.5
  },
  "metrics": {
    "sv_rmse_db": 0.050,
    "sv_mae_db": 0.0059,
    "p95_abs_diff_db": 0.0038,
    "max_abs_diff_db": 4.824
  },
  "evidence_boundary": "validated 38 kHz CW slice"
}
```

## 数据制定原则

- 每个模块至少包含 normal、boundary、noisy、failure 四类测试数据。
- 每个测试数据必须有输入、参考输出、参数、版本和 hash。
- 指标必须绑定数据版本，不能脱离数据集单独宣传。
- no-data、单位、频率、校准假设必须进入 manifest。
- 探索实验和生产验证要分开标注，避免把探索结果当成最终能力。
