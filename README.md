# Domain AI Portfolio / 领域人工智能作品集

> Bridging domain expertise with modern AI engineering: from acoustic algorithms to multimodal RAG, Agent workflows, and Skill-based systems.
>
> 连接领域经验与现代 AI 工程：从声学算法到多模态 RAG、Agent 工作流和 Skill 化系统。

---

## Focus Areas / 重点方向

| Area / 方向 | Description / 描述 |
|---|---|
| **Domain RAG & Knowledge Base**<br>领域 RAG 与知识库 | Structured knowledge extraction, semantic aggregation, multimodal retrieval, hallucination control |
| **Agent + Skill Workflow**<br>Agent + Skill 工作流 | Skill-based architecture, physical constraint binding, Agent orchestration, human-in-the-loop |
| **Acoustic Algorithm Engineering**<br>声学算法工程化 | EK80/EK60 processing, TDOA positioning, Echoview benchmarking, NASC/EDSU workflows |
| **Multimodal Sonar AI**<br>多模态声呐 AI | Sonar image understanding, AI-assisted annotation, YOLO detection + traditional CV refinement |
| **AI Product & Governance**<br>AI 产品与治理 | Problem framing, MVP design, AI dev governance, evaluation frameworks, tech radar |

---

## Selected Projects / 代表项目

| # | Project / 项目 | Core Challenge / 核心挑战 | Key Result / 关键结果 |
|---|---|---|---|
| 01 | [Acoustic QA Agent / 声学问答 Agent](projects/01-acoustic-qa-agent/README.md) | High knowledge barrier sonar domain / 高知识门槛声呐领域 | 3,789 knowledge items, 218 users, 6-month trial |
| 02 | [Agent + Skill Algorithm System / Agent + Skill 算法系统](projects/02-agent-skill-algorithm-system/README.md) | Scattered algorithm knowledge / 分散的算法知识 | Sv RMSE 0.050 dB, 29 modules, 10 Skills |
| 03 | [Sonar Data Processing Automation / 声呐数据处理自动化](projects/03-sonar-data-processing-automation/README.md) | Manual, unreproducible processing / 人工不可复现处理 | 133 files, 87M sample comparisons |
| 04 | [Multimodal RAG Fish Identification / 多模态 RAG 鱼类识别](projects/04-multimodal-rag-fish-identification/README.md) | Small-sample long-tail classification / 小样本长尾分类 | 80% accuracy (4x baseline), 94% Top-3 |
| 05 | [ARIS Jellyfish Detection / ARIS 水母检测](projects/05-aris-jellyfish-detection/README.md) | Low-texture sonar imagery / 低纹理声呐图像 | mAP 0.889, 86k frames, 26.7 FPS |
| 06 | [Ultrasonic Tag Positioning / 超声波标记定位](projects/06-ultrasonic-tag-positioning-system/README.md) | Integrated HW+SW+algorithm system / 硬件+软件+算法集成 | 25k TDOA pairs, 3.5M DB records |
| 07 | [Sturgeon Spawning Monitoring / 中华鲟产卵监测](projects/07-sturgeon-spawning-behavior-monitoring/README.md) | Field deployment under real conditions / 真实野外部署 | Field-hardened monitoring system |
| 08 | [Fisheries Resource Modules / 渔业资源评估模块](projects/08-fisheries-resource-assessment-modules/README.md) | Algorithm-to-software conversion / 算法到软件的转化 | Echoview-benchmarked modules |
| 09 | [AI Development Governance / AI 开发治理](projects/09-ai-development-governance/README.md) | AI tool unpredictability / AI 工具不可预测性 | 8 failure modes, collaboration protocol |
| 10 | [Enterprise AI Tech Radar / 企业 AI 技术雷达](projects/10-enterprise-ai-tech-radar/README.md) | Scattered research hard to use / 分散研究难以应用 | 7-dim scoring model, automated pipeline |

---

## Evaluation Framework / 评估体系

All projects use a **3-level graduated evaluation framework** (L1/L2/L3):

所有项目使用 **3 级分级评估框架** (L1/L2/L3):

```
L1: Format & Contract  →  L2: Numerical Accuracy  →  L3: Domain Plausibility
L1: 格式与契约验证      →  L2: 数值精度验证         →  L3: 领域合理性验证
```

| Level | Focus | Executor | Example / 示例 |
|---|---|---|---|
| L1 | Schema, types, units, completeness | Automated pre-commit | Field presence, type check, unit consistency |
| L2 | RMSE, MAE, IoU, mAP vs reference | CI/CD pipeline | Sv RMSE 0.050 dB vs Echoview |
| L3 | Physical plausibility, operational usability | Human + auto review | Bottom continuity, trajectory smoothness |

See: [Evaluation Framework](methodology/00-enhanced-evaluation-framework.md) | [Validation Metrics](methodology/07-validation-metrics-methodology.md)

---

## Methodological Highlights / 方法论亮点

| Document / 文档 | Content / 内容 |
|---|---|
| [Skill Design Standard / Skill 设计标准](methodology/06-skill-design-standard-enhanced.md) | Standard structure, contract templates, pseudocode examples, development workflow |
| [L1/L2/L3 Evaluation Framework / 分级评估框架](methodology/00-enhanced-evaluation-framework.md) | Per-level definitions, metrics, pseudocode, cross-project reference |
| [Validation Metrics / 验证指标体系](methodology/07-validation-metrics-methodology.md) | Full metric taxonomy, computation pseudocode, dashboard template |
| [Multimodal Sonar AI / 多模态声呐 AI](methodology/04-multimodal-sonar-ai-methodology.md) | Why sonar images are difficult, general pipeline |
| [Role-based Storytelling / 岗位化表达](methodology/05-role-based-project-storytelling.md) | How to describe projects for different roles |

---

## Repository Map / 仓库导航

```
domain-ai-portfolio/
├── README.md                     ← you are here
├── portfolio-overview.md         ← capability map, project matrix, metrics table
├── confidentiality.md            ← what IS and IS NOT included
├── projects/                     ← 10 project directories with full STAR documentation
│   ├── 01-acoustic-qa-agent/
│   ├── 02-agent-skill-algorithm-system/
│   ├── 03-sonar-data-processing-automation/
│   ├── 04-multimodal-rag-fish-identification/
│   ├── 05-aris-jellyfish-detection/
│   ├── 06-ultrasonic-tag-positioning-system/
│   ├── 07-sturgeon-spawning-behavior-monitoring/
│   ├── 08-fisheries-resource-assessment-modules/
│   ├── 09-ai-development-governance/
│   └── 10-enterprise-ai-tech-radar/
├── methodology/                  ← Evaluation framework, Skill standards, metrics, best practices
├── templates/                    ← Reusable templates for project documentation
├── role-mapping/                 ← Role-specific project interpretations
├── evidence/                     ← Anonymized metrics index, demo outputs, diagrams
└── assets/                       ← Architecture diagrams (Mermaid), sample data contracts
```

---

## Key Quantitative Evidence / 关键量化证据

| # | Metric / 指标 | Value / 数值 | Project |
|---|---|---|---|
| 1 | Knowledge descriptions in KB | 3,789 | 01 |
| 2 | Acoustic algorithm modules | 29 | 02 |
| 3 | Raw-to-Sv validation samples | 87,047,436 | 02/03 |
| 4 | Sv RMSE vs Echoview | 0.050 dB | 02/03 |
| 5 | CLIP Top-3 fish ID accuracy | 94.38% | 04 |
| 6 | ARIS detection mAP | 0.889 | 05 |
| 7 | ARIS inference speed | 26.7 FPS | 05 |
| 8 | TDOA positioning pairs | 25,064 | 06 |
| 9 | Database records processed | 3,544,034 | 06 |
| 10 | AI failure modes catalogued | 8 | 09 |

---

## Confidentiality / 保密声明

This repository is a **sanitized portfolio** — it demonstrates system design, evaluation methodology, and project execution capability without exposing:

本仓库是**脱敏作品集** — 展示系统设计、评估方法和项目执行能力，不暴露：

- Proprietary source code / 公司源码
- Internal algorithms / 内部算法
- Confidential datasets / 保密数据
- Customer information / 客户信息

See [confidentiality.md](confidentiality.md) for the full boundary statement.
