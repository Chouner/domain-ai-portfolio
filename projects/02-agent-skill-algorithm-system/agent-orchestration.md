# Agent Orchestration / Agent 调度

## Workflow / 工作流

User request -> Task decomposition -> Data type detection -> Skill retrieval -> Input contract check -> Parameter confirmation -> Skill execution -> Result validation -> Report generation

用户请求 -> 任务拆解 -> 数据类型识别 -> Skill 检索 -> 输入契约检查 -> 参数确认 -> Skill 执行 -> 结果验证 -> 报告生成

## Human Confirmation Triggers / 需要人工确认的触发条件

- Missing required input / 缺少必要输入
- Unknown parameter / 参数未知
- No matching Skill / 没有匹配的 Skill
- Output outside physical range / 输出超出物理范围
- Conflicting module dependency / 模块依赖冲突

