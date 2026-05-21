# Skill-based Algorithm Engineering / Skill 化算法工程

## Why Skill-based Design / 为什么采用 Skill 化设计

In domain-specific algorithm projects, the biggest risk is not only code failure, but loss of physical constraints during implementation.

在专业领域算法项目中，最大的风险不只是代码失败，而是实现过程中丢失物理约束。

Skill-based design aims to bind:

Skill 化设计用于绑定：

- Domain formula / 领域公式
- Input/output contract / 输入输出契约
- Parameter constraints / 参数约束
- Upstream/downstream dependency / 上下游依赖
- Test sample / 测试样例
- Known limitation / 已知限制

## Standard Skill Structure / 标准 Skill 结构

```text
skill-name/
├── skill.md
├── references.md
├── input-contract.md
├── output-contract.md
├── pseudocode.md
├── test-cases.md
├── evaluation.md
└── known-issues.md
```

## Skill Card / Skill 卡片

| Field / 字段 | Description / 说明 |
|---|---|
| Purpose / 目标 | What problem this skill solves / 解决什么问题 |
| Input / 输入 | Required data format / 必要数据格式 |
| Output / 输出 | Expected result / 预期结果 |
| Domain Constraints / 领域约束 | Physical or algorithmic constraints / 物理或算法约束 |
| Dependencies / 依赖 | Upstream/downstream modules / 上下游模块 |
| Evaluation / 评估 | How to verify correctness / 如何验证正确性 |
| Limitations / 限制 | When not to use / 何时不应使用 |

