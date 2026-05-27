# Evidence & Metrics Index / 证据与指标索引

This index tracks all public, non-sensitive metrics across the portfolio. Each metric is linked to its source project and evidence document.

本索引追踪作品集中所有公开的、非敏感指标。每个指标都链接到其来源项目和证据文档。

## Metric Table / 指标表

| # | Metric / 指标 | Value / 数值 | Project | Lvl | Status |
|---|---|---|---|---|---|
| 01 | Core acoustic terms in KB | 100 | 01 | L1 | Verified |
| 02 | Knowledge descriptions | 3,789 | 01 | L1 | Verified |
| 03 | Trial deployment duration | 6 months | 01 | L3 | Observed |
| 04 | Users served | 218 | 01 | L3 | Observed |
| 05 | Candidate acoustic modules | 29 | 02 | L1 | Mapped |
| 05b | Implemented acoustic Skills (SKILL.md) | 18 | 02 | L1 | Defined |
| 05c | Skill contracts (typed dataclasses) | 14 | 02 | L1 | Implemented |
| 05d | C++ cross-language contract runners | 3 | 02 | L1 | Implemented |
| 06 | Module priority tiers (P0/P1/P2/P3/D) | 4/9/11/4/1 | 02 | L1 | Planned |
| 07 | Governance Skills | 10 | 02 | L1 | Defined |
| 08 | Explicit Skill manifests | 18 | 02 | L1 | Implemented |
| 08b | Deployment modes | CLI + Web + Docker | 02 | L3 | Implemented |
| 09 | Typical module cycle | 0.5-2 days | 02 | L3 | Observed |
| 10 | Raw files compared (Sv) | 133 | 02/03 | L2 | Validated |
| 11 | Matched pings | 1,596 | 02 | L2 | Validated |
| 12 | Valid sample comparisons | 87,047,436 | 02/03 | L2 | Validated |
| 13 | Sv RMSE vs Echoview | 0.050 dB | 02/03 | L2 | Validated |
| 14 | Sv MAE | 0.0059 dB | 02/03 | L2 | Validated |
| 15 | Sv p95 absolute difference | 0.0038 dB | 02 | L2 | Validated |
| 16 | Engineering threshold (Sv RMSE) | < 0.5 dB | 02 | L2 | Target |
| 17 | ARIS frames parsed | 86,342 | 05 | L1 | Verified |
| 18 | ARIS detection frames | 74,092 | 05 | L2 | Verified |
| 19 | YOLO model layers | 157 | 05 | L1 | Verified |
| 20 | YOLO model parameters | 7,012,822 | 05 | L1 | Verified |
| 21 | YOLO model GFLOPs | 15.8 | 05 | L1 | Verified |
| 22 | Detection mAP | 0.889 | 05 | L2 | Validated |
| 23 | Inference speed (optimized) | 26.7 FPS | 05 | L3 | Observed |
| 24 | Inference speed (baseline) | 5.6 FPS | 05 | L3 | Observed |
| 25 | Pulse groups processed | 4,720 | 06 | L1 | Verified |
| 26 | TDOA pairs computed | 25,064 | 06 | L1 | Verified |
| 27 | Valid depth values | 21,292 | 06 | L2 | Verified |
| 28 | Valid records after filtering | 14,701 | 06 | L2 | Verified |
| 29 | Tags positioned | 4 | 06 | L3 | Verified |
| 30 | Depth range | 60.09-80.07 m | 06 | L3 | Plausible |
| 31 | Max clock offset corrected | 129.52 s | 06 | L2 | Measured |
| 32 | Database records | 3,544,034 | 06 | L1 | Verified |
| 33 | CLIP Top-1 accuracy | 83.15% | 04 | L2 | Validated |
| 34 | CLIP Top-3 accuracy | 94.38% | 04 | L2 | Validated |
| 35 | Family-level accuracy | 96.63% | 04 | L2 | Validated |
| 36 | Answer-source consistency | 100% | 04 | L2 | Verified |
| 37 | Similar-species confusion | 20% | 04 | L2 | Measured |
| 38 | Knowledge units (from 880 chunks) | 90 | 04 | L1 | Verified |
| 39 | Image inventory | 9,019 images, 1,135 species | 04 | L1 | Inventoried |
| 40 | AI failure modes catalogued | 8 | 09 | L1 | Defined |
| 41 | Tech radar scoring dimensions | 7 | 10 | L1 | Defined |
| 42 | Scoring decision rules | 4 tiers | 10 | L1 | Defined |
| 43 | Tech radar stored objects | 4 types | 10 | L1 | Defined |
| 44 | Field challenges documented | 6 | 07 | L3 | Observed |
| 45 | Algorithm modules (fisheries) | 11 | 08 | L1 | Defined |

## Status Legend / 状态图例

| Status | Meaning / 含义 |
|---|---|
| Verified | Value confirmed through data inspection |
| Validated | Value confirmed through reference comparison (e.g., Echoview) |
| Observed | Value observed during deployment or operation |
| Measured | Value measured through systematic testing |
| Defined | Value established by design |
| Mapped | Module mapping completed |
| Planned | Priority planning completed |
| Inventoried | Inventory count verified |
| Target | Threshold/goal, not a measured value |

## Evidence Files / 证据文件

| File | Metrics Covered |
|---|---|
| `evidence/anonymized-metrics/acoustic-qa-agent.md` | #01-#04 |
| `evidence/anonymized-metrics/skill-system.md` | #05-#16 |
| `evidence/anonymized-metrics/aris-jellyfish.md` | #17-#24 |
| `evidence/anonymized-metrics/ultrasonic-positioning.md` | #25-#32 |
| `evidence/demo-outputs/` | Sample visual outputs |
| `evidence/screenshots/` | [Placeholder] |
| `evidence/diagrams/` | Architecture exports |
