# Portfolio Overview / 作品集总览

## 1. Core Positioning / 核心定位

I focus on converting domain-specific knowledge, expert rules, and complex data-processing workflows into AI systems that are reusable, traceable, and evaluable. My work spans the full lifecycle: from problem framing through evidence-based evaluation.

我专注于将专业领域的知识、专家规则和复杂数据处理流程转化为可复用、可追溯、可评估的 AI 系统。我的工作贯穿全生命周期：从问题定义到证据化评估。

## 2. Capability Map / 能力地图

### 2.1 Domain AI System Design / 领域 AI 系统设计

- RAG and structured knowledge base design / RAG 与结构化知识库设计
- Agent workflow and human-in-the-loop review / Agent 工作流与人机协同
- Skill-based architecture with physical constraint binding / 带物理约束的 Skill 化架构
- L1/L2/L3 graduated evaluation framework / L1/L2/L3 分级评估框架
- Technical reserve and research-intelligence workflow / 技术储备与情报跟踪

### 2.2 Acoustic Algorithm Engineering / 声学算法工程化

- EK80/EK60 raw data parsing and Sv validation / EK80/EK60 原始数据解析与 Sv 验证
- Echoview-style reference comparison (cell/ping/region level) / Echoview 风格参考对比（单元/脉冲/区域级）
- Noise removal, bottom/surface line, NASC and EDSU workflows / 去噪、底/表面线、NASC 和 EDSU 工作流
- Acoustic physical constraints and module contracts / 声学物理约束与模块契约
- Algorithm regression and validation reporting / 算法回归与验证报告
- Ultrasonic tag positioning (TDOA, clock sync, depth decoding) / 超声波标记定位（TDOA、校时、深度解算）

### 2.3 Multimodal AI / 多模态 AI

- Sonar image understanding (ARIS, scientific echosounders) / 声呐图像理解（ARIS、科研探鱼仪）
- AI-assisted annotation with human review / AI 辅助标注与人审
- YOLO-based detection with traditional CV refinement / YOLO 检测 + 传统 CV 后处理
- Multimodal RAG (vision-language + knowledge retrieval) / 多模态 RAG（视觉-语言 + 知识检索）
- Echo-image pre-review for acoustic workflows / 声学工作流中的回波图像预审

### 2.4 AI Product & Engineering Governance / AI 产品与工程治理

- Problem framing and MVP design / 问题定义与 MVP 设计
- AI development governance (context management, requirement freeze, change control) / AI 开发治理（上下文管理、需求冻结、变更控制）
- Cross-functional role mapping and storytelling / 跨职能岗位映射与表达
- Technology scoring and decision framework / 技术评分与决策框架

## 3. Project Matrix / 项目矩阵

| # | Project / 项目 | Domain / 领域 | Core Method / 核心方法 | Key Metric / 关键指标 |
|---|---|---|---|---|
| 01 | Acoustic QA Agent / 声学问答 Agent | NLP, Knowledge Base | RAG, Teacher-Student LM | 3,789 knowledge items, 218 users |
| 02 | Agent + Skill Algorithm System | Algorithm Engineering | Skill architecture, Agent workflow | Sv RMSE 0.050 dB, 29 modules |
| 03 | Sonar Data Processing Automation | Acoustic Processing | Modular pipeline, AI pre-review | 133 files, 87M comparisons |
| 04 | Multimodal RAG Fish Identification | Marine Biology, CV | Multimodal RAG, CLIP+Qwen-VL | 83.15% Top-1, 4x baseline gain |
| 05 | ARIS Jellyfish Detection | Sonar Imaging, CV | YOLO + Traditional CV | mAP 0.889, 26.7 FPS |
| 06 | Ultrasonic Tag Positioning | Acoustic Positioning | TDOA, Clock Sync, DDD | 25,064 TDOA pairs, 4 tags |
| 07 | Sturgeon Spawning Monitoring | Field Deployment | Field acoustics, embedded systems | Field-hardened system |
| 08 | Fisheries Resource Modules | Acoustic Processing | Echoview benchmarking | Per-module validation |
| 09 | AI Development Governance | Engineering Process | Governance framework | 8 failure modes catalogued |
| 10 | Enterprise AI Tech Radar | Tech Research | Scored review pipeline | 7-dim scoring model |

## 4. Role-mapped Capability Scoring / 岗位能力评分

| Project / 项目 | AI PM | AI App Eng | AI Solution | Algo Eng | Multimodal |
|---|---|---|---|---|---|
| 01 Acoustic QA Agent | 5 | 3 | 5 | 2 | 1 |
| 02 Agent + Skill System | 4 | 4 | 5 | 5 | 1 |
| 03 Sonar Automation | 4 | 4 | 5 | 5 | 3 |
| 04 Multimodal RAG Fish | 3 | 5 | 3 | 3 | 5 |
| 05 ARIS Jellyfish | 2 | 5 | 3 | 4 | 5 |
| 06 Ultrasonic Positioning | 3 | 3 | 4 | 5 | 1 |
| 07 Sturgeon Monitoring | 3 | 2 | 5 | 5 | 1 |
| 08 Fisheries Modules | 2 | 2 | 4 | 5 | 1 |
| 09 AI Dev Governance | 5 | 5 | 4 | 2 | 1 |
| 10 Enterprise Tech Radar | 4 | 3 | 5 | 3 | 1 |

Score = 1-5: 1=exposure, 5=deep expertise. / 1=了解, 5=深度经验。

## 5. Representative Metrics / 代表性指标

| Area / 领域 | Metric / 指标 | Value / 数值 | Project |
|---|---|---|---|
| Knowledge base | Knowledge descriptions | 3,789 | 01 |
| Knowledge base | Users served | 218 | 01 |
| Skill system | Implemented acoustic Skills (with SKILL.md) | 18 | 02 |
| Skill system | Skill contracts (typed schemas) | 14 | 02 |
| Skill system | Governance Skills | 10 | 02 |
| Skill system | C++ cross-language bridge | 3 contract runners | 02 |
| Skill system | Typical module cycle | 0.5-2 days | 02 |
| Sv validation | Raw files compared | 133 | 02/03 |
| Sv validation | Valid sample comparisons | 87,047,436 | 02/03 |
| Sv validation | RMSE | 0.050 dB | 02/03 |
| Sv validation | MAE | 0.0059 dB | 02/03 |
| RAG accuracy | Multimodal RAG | ~80% (baseline: 20%) | 04 |
| RAG accuracy | CLIP Top-3 | 94.38% | 04 |
| Detection | ARIS frames parsed | 86,342 | 05 |
| Detection | mAP | 0.889 | 05 |
| Detection | Inference speed | 26.7 FPS | 05 |
| Positioning | TDOA pairs | 25,064 | 06 |
| Positioning | Valid depth values | 21,292 | 06 |
| Positioning | Database records | 3,544,034 | 06 |
| Governance | AI failure modes | 8 | 09 |
| Tech radar | Scoring dimensions | 7 | 10 |

## 6. Repository Navigation / 仓库导航

| Path / 路径 | Content / 内容 |
|---|---|
| `projects/` | 10 project directories with full documentation |
| `methodology/` | Enhanced methodology: evaluation framework (L1/L2/L3), Skill design standards, validation metrics, role-based storytelling |
| `templates/` | Reusable templates: project README, problem framing, evaluation plan, Skill card, postmortem |
| `role-mapping/` | Role-specific project interpretations (AI PM, AI App Eng, AI Solution, Algo Eng) |
| `evidence/` | Anonymized metrics index and public evidence |
| `assets/` | Architecture diagrams (Mermaid), workflow diagrams, sample data contracts |

## 7. Evaluation Framework / 评估框架

All projects use a 3-level graduated evaluation framework:

所有项目使用 3 级分级评估框架：

- **L1 Format Validation / 格式验证**: Schema, types, units, completeness
- **L2 Numerical Validation / 数值验证**: RMSE, MAE, IoU, mAP vs reference
- **L3 Domain Validation / 领域验证**: Physical plausibility, operational usability, expert acceptance

See [Evaluation Framework / 评估框架](methodology/00-enhanced-evaluation-framework.md) for full details.

## 8. Methodology Highlights / 方法论亮点

- [Skill Design Standard / Skill 设计标准](methodology/06-skill-design-standard-enhanced.md): Standard structure, contract templates, development workflow, pseudocode examples
- [Validation Metrics / 验证指标](methodology/07-validation-metrics-methodology.md): Full metric taxonomy with pseudocode, dashboard template
- [Evaluation Framework / 评估框架](methodology/00-enhanced-evaluation-framework.md): L1/L2/L3 with per-level pseudocode
