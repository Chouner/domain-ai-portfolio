# 声学 Agent 系统架构

## 架构定位

这个声学 Agent 不是单纯的问答系统，也不是单个算法脚本。它的定位是一个“知识库 + 数据治理 + 算法 Skill + Agent 调度 + AI 预审 + 专家复审 + 报告交付”的工程系统。

系统目标有三个：

1. 把 Echoview、论文、开源代码、内部经验和专家判断变成可复用的结构化知识。
2. 把 EK80/EK60 raw 数据处理拆成可验证、可回归、可交付的算法模块。
3. 当算法指标不达标时，自动回到技术储备和经验库中寻找改进方向，形成“失败 - 检索 - 探索 - 验证 - 专家确认 - 入库”的闭环。

## 总体架构图

```mermaid
flowchart TB
    subgraph L1["知识库层 / Knowledge Layer"]
        K1["Echoview 行为知识库"]
        K2["声学论文 / 算法文献"]
        K3["开源声学项目代码"]
        K4["内部项目经验 / 专家确认"]
        K5["Hermes / OpenClaw 定时采集"]
        K6["技术储备库: source_items / tech_cards / feasibility_reports"]
        K7["经验卡片库: expert_assertions / experience_cards / metric_rules"]
        K1 --> K6
        K2 --> K6
        K3 --> K6
        K4 --> K7
        K5 --> K6
        K7 --> K6
    end

    subgraph L2["数据层 / Data Layer"]
        D1["EK80 / EK60 raw 文件"]
        D2["Echoview CSV / 参考导出"]
        D3["GPS / 航迹 / 时间信息"]
        D4["测试数据集: normal / boundary / noisy / failure"]
        D5["统一数据产物: Sv / TS / Power / Metadata"]
        D6["验证数据 manifest / metric_result"]
        D1 --> D5
        D2 --> D5
        D3 --> D5
        D4 --> D6
        D5 --> D6
    end

    subgraph L3["算法层 / Algorithm Skill Layer"]
        A1["Raw 解析 / Sv 导出 Skill"]
        A2["背景噪声 / 脉冲噪声 / 瞬态噪声去除"]
        A3["表层线 / 底线检测"]
        A4["EDSU / NASC 聚合"]
        A5["目标检测 / DSL / 频率判别"]
        A6["质量评价 / 异常检测"]
        A7["模块契约: 输入 / 输出 / 参数 / 阈值 / 物理约束"]
        A7 --> A1
        A7 --> A2
        A7 --> A3
        A7 --> A4
        A7 --> A5
        A7 --> A6
    end

    subgraph L4["调度层 / Agent Orchestration Layer"]
        O1["用户任务 / 项目需求"]
        O2["Agent Planner: 任务拆解"]
        O3["Skill Retriever: 模块检索"]
        O4["Contract Checker: 契约检查"]
        O5["Pipeline Orchestrator: 流程编排"]
        O6["Validation Runner: 一致性验证"]
        O1 --> O2 --> O3 --> O4 --> O5 --> O6
    end

    subgraph L5["决策与分析层 / Decision & Analysis Layer"]
        Q1["AI 回波图预审"]
        Q2["物理约束检查"]
        Q3["Echoview / Reference 对比"]
        Q4["指标计算: RMSE / MAE / IoU / NASC Error"]
        Q5{"是否达标?"}
        Q6["失败特征提取"]
        Q7["技术储备检索"]
        Q8["探索测试报告"]
        Q9["专家确认 / 人工复审 / Go-NoGo"]
        Q1 --> Q2 --> Q3 --> Q4 --> Q5
        Q5 -- "通过" --> Q9
        Q5 -- "不通过" --> Q6 --> Q7 --> Q8 --> Q9
    end

    subgraph L6["可视化与交付层 / Visualization & Delivery Layer"]
        V1["回波图 / 噪声 mask / 底线图"]
        V2["指标面板 / 质量报告"]
        V3["处理结果文件 / 标准接口"]
        V4["自动报告生成"]
        V5["飞书发送 / 项目交付"]
        V1 --> V2 --> V4
        V3 --> V4 --> V5
    end

    K6 --> O3
    K7 --> A7
    D5 --> O4
    D6 --> O6
    O5 --> A1
    O5 --> A2
    O5 --> A3
    O5 --> A4
    O5 --> A5
    O5 --> A6

    A1 --> Q1
    A2 --> Q1
    A3 --> Q1
    A4 --> Q1
    A5 --> Q1
    A6 --> Q1

    Q7 --> K6
    Q9 --> K7
    Q9 --> V1
    Q9 --> V3
```

## 分层说明

| 层级 | 作用 | 关键产物 |
|---|---|---|
| 知识库层 | 汇聚 Echoview、论文、开源代码、内部经验和专家判断 | source_items、tech_cards、feasibility_reports、experience_cards |
| 数据层 | 统一 raw、CSV、GPS、测试数据和验证结果 | raw manifest、artifact manifest、validation_case、metric_result |
| 算法层 | 将声学算法封装为 Skill，并绑定契约和阈值 | Skill manifest、module contract、parameter schema |
| 调度层 | 根据任务自动拆解流程、选择 Skill、检查契约并执行流水线 | task graph、pipeline plan、execution log |
| 决策与分析层 | 做 AI 预审、参考对比、指标计算、失败分析和专家复审 | validation report、failure signature、go/no-go decision |
| 可视化与交付层 | 输出回波图、质量报告、结果文件和飞书交付物 | echogram、dashboard、report、delivery package |

## 核心闭环

1. 新任务进入后，Agent Planner 将需求拆成数据解析、预处理、算法处理、验证和报告几个阶段。
2. Skill Retriever 根据数据类型、频率、目标指标和模块依赖选择候选 Skill。
3. Contract Checker 检查输入、输出、参数、单位、no-data 语义和物理约束。
4. Pipeline Orchestrator 执行模块并生成标准产物。
5. Validation Runner 计算 RMSE、MAE、IoU、NASC 相对误差、底线误差等指标。
6. 若结果通过阈值，进入专家确认和交付；若不通过，生成 failure signature，检索技术储备并进入探索测试。
7. 专家确认后的判断被结构化为经验卡片，回写到知识库和 Skill 约束中。
