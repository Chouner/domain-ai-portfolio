# Project Retrospectives & Boundary Analysis / 项目复盘与边界分析

This document provides consolidated retrospectives for major portfolio projects, identifying what worked, what was learned, key constraints discovered, and explicit system boundaries.

本文档汇总了主要作品集项目的复盘分析，包括有效方法、经验教训、关键约束和系统边界。

---

## Retrospective 1: Agent + Skill Algorithm Engineering System

### What Worked Well / 有效方法

1. **Physical constraint binding was the key differentiator.** The Skill contract forced developers to explicitly define valid parameter ranges, physical assumptions, and domain boundary conditions before writing code. This eliminated the most common failure mode: losing domain constraints during implementation.

2. **Two-loop design added resilience.** Separating the algorithm engineering loop from the research intelligence loop allowed each to evolve independently. The research loop became a "safety net" — when modules failed validation, the system could automatically propose experiments rather than blocking development.

3. **3-level evaluation was essential for quality control.** The graduated framework (L1 format → L2 numerical → L3 domain) meant failures were caught at the cheapest level first. No L2 comparison was wasted on badly-formatted data.

4. **Echoview as a ground truth reference.** Using Echoview outputs as the reference standard was the right choice — it's the de facto industry tool, and matching its behavior was a prerequisite for stakeholder trust.

### Key Insights / 关键发现

- **Domain constraint binding > code style.** The most impactful work wasn't writing elegant code — it was exhaustively documenting what the algorithm should NOT do.
- **Grouped validation catches more than individual testing.** Validating modules on grouped reference data (multiple files, multiple conditions) consistently caught failures that single-test-case validation missed.
- **Skill manifests are worth the overhead.** Each Skill manifest (~1-2 hours to produce) saved 3-5x that time in debugging, knowledge transfer, and re-validation.

### Boundaries & Limitations / 边界与限制

| Boundary / 边界 | Detail / 详情 |
|---|---|
| **Not a commercial platform** | Partial validated modules; some denoising modules need revalidation |
| **Echoview-dependent reference** | Validation quality depends on Echoview's correctness as reference |
| **Transducer-specific** | Modules validated for specific transducer models (e.g., ES38B); new hardware needs re-validation |
| **Historical data semantics** | Some legacy CSV exports had no-data semantics that need correction before full module registration |

---

## Retrospective 2: Ultrasonic Tag Positioning System

### What Worked Well / 有效方法

1. **Five-level fallback strategy was non-negotiable.** Real-world deployment conditions guaranteed data gaps. The graduated fallback (Standard → Historical → Joint Batch → Relaxed → Prediction) ensured positioning continuity even under adverse conditions.

2. **Clock synchronization trumps solver precision.** A 1m-accurate track with correct clock sync beats 10cm snapshots with timing errors. The three-stage sync (reference beacon + PPS counting + UTC trigger) was the hardest and most important part of the system.

3. **DDD (Domain-Driven Design) provided clarity.** Structuring the code as `ProcessingOrchestrator`, `ProcessedDataRepository`, `PingerRawData`, `BeaconRawData` made the domain logic explicit and testable.

### Key Insights / 关键发现

- **Hardware + algorithm co-design is required for field systems.** Sequential handoff ("hardware team builds it, software team codes it") leads to intractable problems discovered too late.
- **Robustness > ideal accuracy.** Field systems prioritize data continuity and graceful degradation over peak accuracy.
- **Manual verification tools are safety-critical.** Automated systems can silently fail in ways that only a domain expert can detect.

### Boundaries & Limitations / 边界与限制

| Boundary / 边界 | Detail / 详情 |
|---|---|
| **Tag-type specific** | Validated for specific ultrasonic tag models (~60 kHz); new tag types need re-validation |
| **Receiver geometry dependent** | Positioning accuracy degrades with poor receiver array geometry |
| **Multipath environments** | Heavy multipath environments (shallow water, complex bathymetry) degrade signal clarity |
| **Biological speed assumption** | Position prediction (Level 5 fallback) assumes max biological speed of 5 m/s |

---

## Retrospective 3: Multimodal RAG Fish Identification

### What Worked Well / 有效方法

1. **Pivoting from classification to RAG was the right call.** The domain is fundamentally knowledge-intensive, not pattern-intensive. Thousands of species with few samples each is a retrieval problem, not a classification problem.

2. **Structured image description was the critical bridge.** Qwen3-VL-Plus's structured JSON description transformed visual features into text that could be matched against knowledge units — bridging the vision-language gap without fine-tuning.

3. **Semantic aggregation (880 chunks → 90 units) dramatically improved retrieval.** Too-fine granularity (880 fragmented chunks) produced noisy retrieval; coarse, semantically-coherent units were far more effective.

4. **MVP-first approach validated before scaling.** The 10-species / 89-image MVP proved the concept; scaling to 110 species was incremental, not a leap of faith.

### Key Insights / 关键发现

- **Knowledge unit quality > quantity.** Well-structured knowledge units with diagnostic features outperformed larger but poorly-structured knowledge bases.
- **Baseline analysis changed the route.** The initial CLIP baseline (83.15% Top-1) showed promise but also revealed failure modes (20% similar-species confusion) that guided the RAG architecture design.

### Boundaries & Limitations / 边界与限制

| Boundary / 边界 | Detail / 详情 |
|---|---|
| **Image quality dependent** | Damaged specimens, juvenile stages, and poor lighting degrade accuracy |
| **Species coverage** | Long-tail species with 0-1 reference images cannot be identified |
| **Similar-species confusion** | ~20% error rate for morphologically similar species; DNA verification needed |
| **Not a real-time system** | Multi-stage RAG pipeline has latency unsuitable for real-time identification |

---

## Retrospective 4: ARIS Jellyfish Detection

### What Worked Well / 有效方法

1. **AI-assisted annotation was the only practical path.** Pure manual annotation of 86,000+ sonar frames was economically impossible. The multimodal LLM → human review → model training → model prediction → human review loop created a virtuous cycle.

2. **Traditional CV still matters.** Simple intensity checks and morphological filters eliminated YOLO false positives from noise artifacts — a small addition with outsized impact.

3. **Inference speed optimization unlocked practical use.** The 4.8x speedup (5.6 → 26.7 FPS) moved the system from "interesting research" to "deployable product."

### Key Insights / 关键发现

- **Sonar images require different assumptions than optical images.** Assumptions about texture, color, and boundary clarity from optical CV don't transfer to acoustic imaging.
- **Annotation pipeline design is a UX problem, not just an ML problem.** The annotation interface and workflow must minimize cognitive load on domain experts.

### Boundaries & Limitations / 边界与限制

| Boundary / 边界 | Detail / 详情 |
|---|---|
| **High-clutter environments** | Performance degrades in dense target clusters |
| **Very small targets** | Juvenile jellyfish below ~5 pixels are unreliable |
| **ARIS-specific** | Model trained on ARIS imagery; performance on other sonar types untested |
| **Single species** | Currently trained on jellyfish; extension to multi-class requires re-annotation |

---

## Retrospective 5: Fisheries Resource Assessment Algorithm Modules

### What Worked Well / 有效方法

1. **Echoview as the unambiguous reference.** No subjective "ground truth" — Echoview output IS the standard. Any discrepancy is a bug to fix, not a debate to have.

2. **Cell-level, ping-level, and region-level comparison.** Testing at multiple granularities caught errors that single-level comparison missed.

3. **C++ DLL delivery format bridged Python research to production software.** The team could use Python for rapid development and Echoview comparison, then deliver a C++ DLL for integration.

### Key Insights / 关键发现

- **Tolerance-based validation matters more than absolute error.** For most biological applications, being within 0.5 dB of Echoview is "good enough" — chasing sub-0.01 dB precision has diminishing returns.
- **Module contracts are the deliverable, not the code.** Downstream integrators care about the input/output contract, not the implementation details.

### Boundaries & Limitations / 边界与限制

| Boundary / 边界 | Detail / 详情 |
|---|---|
| **Echoview version-locked** | Modules validated against specific Echoview version; versions may differ |
| **Transducer-specific calibration** | Each transducer model requires separate calibration |
| **Not full-featured** | 11 modules is a subset of full acoustic processing capabilities |
| **Freshwater vs. seawater** | Absorption coefficient tables may differ between environments |

---

## Cross-Project Lessons Learned / 跨项目经验总结

### Universal Principles / 通用原则

1. **Domain constraints first, code second.** In every domain AI project, understanding and binding physical/biological/regulatory constraints was more impactful than code optimization.

2. **Graduated evaluation (L1→L2→L3) saves time.** Fixing format errors at L1 is 10x cheaper than discovering them at L3.

3. **Baselines change routes.** Running thorough baselines (Project 04: CLIP 83% → RAG 80%) revealed failure modes that shaped the entire architecture decision.

4. **Human-in-the-loop is not a weakness — it's safety.** In high-expertise domains, automated systems must defer to experts when uncertain.

5. **MVP before scale.** Every project that started with an MVP (10 species, 10 files, 1 transducer) succeeded faster than those that tried to build for all cases upfront.

### Anti-patterns to Avoid / 应避免的反模式

1. **Over-engineering before validation.** Building a full platform before validating core algorithms leads to waste.
2. **Treating domain metrics like generic ML metrics.** "Accuracy" without physical plausibility is meaningless in scientific domains.
3. **Ignoring data provenance.** A metric without a traceable data source is not evidence — it's a number.
