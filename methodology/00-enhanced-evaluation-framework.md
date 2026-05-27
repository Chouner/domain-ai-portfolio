# L1/L2/L3 Evaluation Framework / L1/L2/L3 分级评估框架

## Overview / 概述

The L1/L2/L3 framework provides a graduated evaluation system for domain AI projects. Each level progressively increases in rigor, scope, and domain fidelity.

L1/L2/L3 分级评估框架为专业领域 AI 项目提供了一个逐级递进的评估体系，每一级在严格度、广度和领域真实度上逐步提升。

---

## L1 — Format & Contract Validation / 格式与契约验证

**Goal / 目标:** Verify that the artifact is readable, well-formed, and compatible with downstream consumption.

**Scope / 范围:** Structural correctness without semantic judgment.

| Dimension / 维度 | Metrics / 指标 | Target / 目标 | Method / 方法 |
|---|---|---|---|
| Schema completeness | Schema required field pass rate | 100% | Automated schema check |
| Unit consistency | Unit mismatch count | 0 | Automated unit parser |
| Data type integrity | Type error count | 0 | Automated type check |
| Missing value ratio | Null / NaN ratio per field | < 1% | Statistical scan |
| Contract compatibility | Input/output field match rate | 100% | Contract validator |
| File format integrity | Corrupt or unreadable file count | 0 | File reader check |

**Pseudocode / 伪代码:**

```python
def l1_validate(artifact, schema, contract):
    """
    L1 validation: format, schema, contract compatibility.
    Returns (pass: bool, issues: list[dict]).
    """
    issues = []

    # 1. Schema check
    for field in schema.required_fields:
        if field not in artifact:
            issues.append({
                "level": "L1",
                "type": "missing_field",
                "field": field,
                "detail": f"Required field '{field}' not found in artifact"
            })

    # 2. Type check
    for field, expected_type in schema.field_types.items():
        actual = artifact.get(field)
        if actual is not None and not isinstance(actual, expected_type):
            issues.append({
                "level": "L1",
                "type": "type_mismatch",
                "field": field,
                "expected": str(expected_type),
                "actual": str(type(actual))
            })

    # 3. Unit consistency check
    for field in schema.numeric_fields:
        value = artifact.get(field)
        declared_unit = artifact.get(f"{field}_unit")
        expected_unit = schema.unit_mapping.get(field)
        if expected_unit and declared_unit != expected_unit:
            issues.append({
                "level": "L1",
                "type": "unit_mismatch",
                "field": field,
                "expected": expected_unit,
                "actual": declared_unit
            })

    # 4. Contract compatibility
    for field in contract.output_fields:
        if field not in artifact:
            issues.append({
                "level": "L1",
                "type": "contract_violation",
                "field": field,
                "detail": "Output contract requires this field"
            })

    return len(issues) == 0, issues
```

---

## L2 — Numerical & Reference Validation / 数值与参考验证

**Goal / 目标:** Compare module output against a trusted reference to measure numerical accuracy.

**Scope / 范围:** Cell-level / ping-level / region-level quantitative comparison.

**Prerequisites / 前提条件:** L1 must pass before L2.

| Dimension / 维度 | Metrics / 指标 | Acoustic Target / 声学目标 | Method / 方法 |
|---|---|---|---|
| Point error | RMSE | Sv < 0.5 dB | Cell-by-cell diff with Echoview |
| Absolute error | MAE | Sv < 0.1 dB | Cell-by-cell abs diff |
| Tail error | p95 absolute error | < 1.0 dB | Quantile analysis |
| Max error | Max absolute error | < 3.0 dB | Bounds check |
| Pixel/Region overlap | IoU (Intersection over Union) | > 0.85 | Binary mask comparison |
| Classification match | Accuracy | > 90% | Confusion matrix vs reference |

**Pseudocode / 伪代码:**

```python
def l2_validate(module_output, reference_output, module_type):
    """
    L2 validation: numerical comparison against reference.
    """
    metrics = {}
    passed = True

    if module_type == "sv_calculation":
        diff = module_output["sv"] - reference_output["sv"]

        # RMSE
        metrics["rmse"] = float(np.sqrt(np.mean(diff**2)))

        # MAE
        metrics["mae"] = float(np.mean(np.abs(diff)))

        # p95 error
        metrics["p95_abs_error"] = float(np.percentile(np.abs(diff), 95))

        # Max error
        metrics["max_abs_error"] = float(np.max(np.abs(diff)))

        # Pass rate: percentage of cells within tolerance
        tolerance = 0.5  # dB
        metrics["pass_rate"] = float(np.mean(np.abs(diff) < tolerance))

        # Threshold check
        thresholds = {
            "rmse": 0.5,
            "mae": 0.1,
            "p95_abs_error": 1.0,
            "pass_rate": 0.95
        }

        for key, threshold in thresholds.items():
            if metrics.get(key, float("inf")) > threshold:
                passed = False

    elif module_type == "detection_mask":
        intersection = np.logical_and(module_output["mask"], reference_output["mask"]).sum()
        union = np.logical_or(module_output["mask"], reference_output["mask"]).sum()
        metrics["iou"] = float(intersection / union) if union > 0 else 0.0
        metrics["precision"] = float(intersection / module_output["mask"].sum()) if module_output["mask"].sum() > 0 else 0.0
        metrics["recall"] = float(intersection / reference_output["mask"].sum()) if reference_output["mask"].sum() > 0 else 0.0

        if metrics["iou"] < 0.85:
            passed = False

    elif module_type == "bottom_detection":
        diff = module_output["bottom_depth"] - reference_output["bottom_depth"]
        metrics["rmse_depth"] = float(np.sqrt(np.mean(diff**2)))
        metrics["mae_depth"] = float(np.mean(np.abs(diff)))
        metrics["within_1m_rate"] = float(np.mean(np.abs(diff) < 1.0))

        if metrics["within_1m_rate"] < 0.95:
            passed = False

    return passed, metrics
```

---

## L3 — Domain & Production Validation / 领域与生产验证

**Goal / 目标:** Check whether the result is physically meaningful and operationally usable.

**Scope / 范围:** Physical plausibility, workflow integration, end-to-end usability.

**Prerequisites / 前提条件:** L1 + L2 must pass before L3.

| Dimension / 维度 | Metrics / 指标 | Target / 目标 | Method / 方法 |
|---|---|---|---|
| Physical plausibility | Bottom line continuity | No jumps > 5 m between pings | Inter-ping gradient check |
| Physical plausibility | NASC spatial consistency | CV < 15% within EDSU | Statistical analysis |
| Biological validity | Biomass estimate vs survey | Relative error < 20% | Expert comparison |
| Operational usability | Processing time per file | < 30 s per file | Runtime measurement |
| Reviewer acceptance | Expert acceptance rate | > 90% | Human review |
| False detection rate | False positive rate | < 5% | Manual annotation spot check |
| Edge case handling | Known boundary case pass rate | 100% | Test suite on boundary inputs |
| Traceability | Output-to-input trace | Full chain | Dependency graph walk |

**Pseudocode / 伪代码:**

```python
def l3_validate(module_output, context, module_type):
    """
    L3 validation: domain and production-level checks.
    Requires L1 and L2 to already pass.
    """
    issues = []

    if module_type == "bottom_detection":
        # Physical plausibility: bottom line continuity
        depths = module_output["bottom_depth"]
        for i in range(1, len(depths)):
            jump = abs(depths[i] - depths[i-1])
            if jump > 5.0:  # meters
                issues.append({
                    "level": "L3",
                    "type": "bottom_discontinuity",
                    "ping_index": i,
                    "jump_meters": float(jump),
                    "detail": f"Bottom depth jumped {jump:.1f}m between ping {i-1} and {i}"
                })

    elif module_type == "nasc_aggregation":
        # Physical plausibility: NASC spatial consistency
        nasc_values = module_output["nasc"]
        edsu_ids = module_output["edsu_id"]
        for edsu_id in np.unique(edsu_ids):
            edsu_nasc = nasc_values[edsu_ids == edsu_id]
            if len(edsu_nasc) > 1:
                cv = np.std(edsu_nasc) / (np.mean(edsu_nasc) + 1e-8)
                if cv > 0.15:
                    issues.append({
                        "level": "L3",
                        "type": "nasc_inconsistency",
                        "edsu_id": int(edsu_id),
                        "cv": float(cv),
                        "detail": f"NASC CV within EDSU {edsu_id} exceeds 15% threshold"
                    })

    elif module_type == "positioning":
        # Physical plausibility: positioning trajectory continuity
        positions = module_output["positions"]
        for i in range(1, len(positions)):
            dt = positions[i]["time"] - positions[i-1]["time"]
            dx = positions[i]["x"] - positions[i-1]["x"]
            dy = positions[i]["y"] - positions[i-1]["y"]
            speed = np.sqrt(dx**2 + dy**2) / max(dt, 0.001)
            max_speed = context.get("max_biological_speed", 5.0)  # m/s
            if speed > max_speed:
                issues.append({
                    "level": "L3",
                    "type": "biologically_implausible",
                    "index": i,
                    "speed_ms": float(speed),
                    "max_expected_ms": max_speed,
                    "detail": f"Position jump implies speed {speed:.1f} m/s, exceeding biological maximum {max_speed} m/s"
                })

    return len(issues) == 0, issues
```

---

## Level Summary / 分级总览

```
┌─────────────────────────────────────────────────────────────┐
│                    Evaluation Escalation                    │
├─────────┬─────────────────┬──────────────┬──────────────────┤
│  Level  │  Focus          │  Executor    │  Fails When      │
├─────────┼─────────────────┼──────────────┼──────────────────┤
│  L1     │  Format check   │  Automated   │  Missing fields  │
│         │  Schema check   │  pre-commit  │  Wrong types     │
│         │  Unit check     │  hook        │  Unit mismatch   │
├─────────┼─────────────────┼──────────────┼──────────────────┤
│  L2     │  Numerical      │  Automated   │  RMSE/MAE >      │
│         │  Reference      │  CI/CD       │  threshold       │
│         │  Comparison     │  pipeline    │  IoU < threshold │
├─────────┼─────────────────┼──────────────┼──────────────────┤
│  L3     │  Domain         │  Human +     │  Physical        │
│         │  Plausibility   │  Automated   │  violation       │
│         │  Operational    │  review      │  Expert reject   │
│         │  Usability      │              │  Missing trace   │
└─────────┴─────────────────┴──────────────┴──────────────────┘
```

## Across the Portfolio / 跨项目应用

| Project / 项目 | L1 | L2 | L3 | Key L2 Metric / L2 指标 | Key L3 Metric / L3 指标 |
|---|---|---|---|---|---|
| 02 Agent+Skill | Schema pass | Sv RMSE 0.050 dB | Bottom continuity | Module contract | Reviewer acceptance |
| 03 Sonar Auto | File schema | MAE 0.0059 dB | Processing time | Cell diff | Expert acceptance |
| 04 Multimodal RAG | Knowledge unit schema | Top-1 83.15% | Expert usability | Retrieval hit rate | Hallucination rate |
| 05 ARIS Jellyfish | Annotation schema | mAP 0.889 | False positive rate | IoU | Detections per frame |
| 06 Ultrasonic Pos. | Data schema | Clock sync error | Biological speed check | TDOA residual | Trajectory smoothness |
| 08 Fisheries Mod. | Module contract | Echoview RMSE | Biomass estimate | Sv error | NASC CV within EDSU |
