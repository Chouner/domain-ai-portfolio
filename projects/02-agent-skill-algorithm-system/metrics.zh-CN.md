# 指标与量化方案

这个文件定义 Agent + Skill 算法系统如何量化，目标是把“提升效率”这类模糊说法变成可追踪的工程指标。

## 1. 算法开发效率

| 指标 | 定义 | 计算方式 | 目标 |
|---|---|---|---:|
| 模块交付周期 | 从契约确认到验证报告完成的时间 | `report_time - contract_confirmed_time` | 常规模块 0.5-2 天 |
| 需求澄清轮次 | 编码前阻塞性问题的确认次数 | 计数 | <= 2 |
| 返工轮次 | 验证失败后的修复闭环次数 | remediation report 计数 | P0/P1 <= 2 |
| 契约完整度 | 必填契约字段填写比例 | `filled_required_fields / total_required_fields` | >= 95% |
| 复用比例 | 复用已有 Skill、契约、测试模板的比例 | `reused_components / total_components` | 持续提高 |

## 2. Skill 库质量

| 指标 | 定义 | 价值 |
|---|---|---|
| Skill manifest 覆盖率 | 模块是否具备 Purpose、Formula、Input、Output、Parameters、Evaluation、Limitations | 避免临时调用算法 |
| 依赖覆盖率 | 是否声明上下游模块依赖 | 支撑 Agent 编排 |
| 参数范围覆盖率 | 参数是否有单位、范围、默认值和告警规则 | 降低无效运行 |
| 测试用例覆盖率 | 是否覆盖 normal、boundary、noisy、failure case | 让回归测试有意义 |
| 物理约束覆盖率 | 是否包含量程、Sv 单位、TVG 假设、no-data 语义等声学约束 | 避免“看似合理但错误”的输出 |

## 3. 算法验证指标

| 模块类型 | 指标 | 示例阈值 |
|---|---|---:|
| Raw 解析 / Sv 导出 | RMSE、MAE、p95 绝对误差、最大绝对误差、有效样本数 | RMSE < 0.5 dB |
| 背景噪声/脉冲噪声/瞬态噪声 | SNR 提升、mask IoU、误删率、有效样本保留率 | IoU > 0.85 |
| 表层线/底线 | 米级 MAE、p95 深度误差、连续性断裂数 | MAE < 1 m |
| NASC / EDSU 聚合 | 相对误差、有效 cell 比例、缺失 cell 数 | 相对误差 < 5% |
| AI 回波图预审 | precision、recall、人工一致性、误报率 | 人工认可率 > 90% |
| 端到端流水线 | 阶段成功率、单 raw 运行时间、人工介入次数 | 趋势性改善 |

## 4. 技术储备指标

| 阶段 | 指标 | 计算方式 |
|---|---|---|
| 采集 | 每次有效入库条目数 | accepted source items per run |
| 去重 | 重复率 | `duplicate_items / collected_items` |
| 结构化 | 字段完整度 | `filled_required_fields / required_fields` |
| 分类 | 人工认可率 | `human_accepted_labels / reviewed_labels` |
| 检索 | Precision@K | `relevant_candidates_in_top_k / k` |
| 实验 | 实验转化率 | `experiments_started / retrieved_candidates` |
| 采纳 | 有效技术率 | `accepted_techniques / tested_techniques` |
| 恢复 | 失败到报告耗时 | `experiment_report_time - failure_detected_time` |

## 5. 当前证据

| 证据 | 数值 | 状态 |
|---|---:|---|
| 候选声学处理模块 | 29 | 模块地图完成 |
| Governance Skills | 10 | 设计完成 |
| 公开 Skill manifests | 4 | 已实现/已文档化 |
| P0 模块 | 4 | 优先级计划 |
| P1 模块 | 9 | 优先级计划 |
| 38 kHz CW 对比 raw 文件 | 133 | 已完成验证运行 |
| 匹配 ping | 1,596 | 已完成验证运行 |
| 有效样本对比 | 87,047,436 | 已完成验证运行 |
| Sv RMSE | 0.050 dB | 低于阈值 |
| Sv MAE | 0.0059 dB | 低于阈值 |
| Sv p95 绝对误差 | 0.0038 dB | 低于阈值 |

## 6. 决策规则

- Pass：L1/L2 必要检查全部通过，主领域指标达到目标。
- Conditional pass：主指标通过，但边界情况需要人工复审或补充数据。
- Explore：当前实现不达标，但技术储备中存在相关方案且预期成本可接受。
- Reject：多次失败、证据弱、数据假设不兼容或工程成本不可接受。
