# RAG and Agent Evaluation Framework / RAG 与 Agent 评估框架

## 1. RAG Metrics / RAG 指标

| Metric / 指标 | Meaning / 含义 | How to Evaluate / 评估方法 |
|---|---|---|
| Retrieval Hit Rate / 检索命中率 | Whether relevant document is retrieved / 是否检索到相关文档 | Human-labeled QA set / 人工标注问答集 |
| TopK Accuracy / TopK 准确率 | Whether answer appears in top K chunks / 答案是否出现在 TopK 片段中 | Top1/Top3/Top5 / Top1/Top3/Top5 |
| Answer Accuracy / 答案准确率 | Whether final answer is correct / 最终答案是否正确 | Expert review / 专家评审 |
| Citation Coverage / 引用覆盖率 | Whether answer has source support / 回答是否有来源支撑 | Manual check / 人工检查 |
| Hallucination Rate / 幻觉率 | Unsupported or wrong answer rate / 无支撑或错误回答比例 | Error annotation / 错误标注 |
| Human Handoff Rate / 人工转接率 | Whether human intervention is needed / 是否需要人工介入 | Chat logs / 对话日志 |

## 2. Agent Metrics / Agent 指标

| Metric / 指标 | Meaning / 含义 |
|---|---|
| Task Success Rate / 任务成功率 | Whether agent completes full workflow / Agent 是否完成完整流程 |
| Tool Call Accuracy / 工具调用准确率 | Whether correct tool or skill is selected / 是否选择正确工具或 Skill |
| Human Intervention Rate / 人工介入率 | How often user must correct the agent / 用户需要纠正 Agent 的频率 |
| Traceability / 可追溯性 | Whether output can be traced to data or module / 输出是否可追溯到数据或模块 |
| Recovery Capability / 恢复能力 | Whether failure can be detected and handled / 是否能识别并处理失败 |

## 3. Evaluation Pipeline / 评估流程

Question set -> Retrieval result -> Answer generation -> Human scoring -> Error classification -> Iteration

问题集 -> 检索结果 -> 答案生成 -> 人工评分 -> 错误分类 -> 迭代

