# Validation Metrics Methodology / 验证指标体系方法论

## 1. Philosophy / 核心理念

Validation metrics in domain AI differ from generic ML metrics in two ways:

1. **Domain physical constraints** — values must be physically plausible (not just numerically close to reference)
2. **Operational traceability** — every metric must trace back to a specific data source, algorithm step, and validation method

专业领域 AI 的验证指标与通用 ML 指标有两点不同：一是必须满足物理约束（不只是在数值上接近参考），二是操作可追溯（每个指标都要能追溯到具体数据来源、算法步骤和验证方法）。

---

## 2. Metric Governance Principles / 指标治理原则

| Principle / 原则 | Description / 描述 |
|---|---|
| **Source-bound / 源绑定** | Every metric has a defined data source, sample size, and computation method |
| **Threshold-gated / 阈值门控** | Every metric has an explicit pass/fail threshold tied to domain requirements |
| **Level-structured / 分级结构** | Metrics are grouped by L1 (format), L2 (numerical), L3 (domain) |
| **Evolution-tracked / 演进追踪** | Metric values are versioned alongside algorithm versions |
| **Trustworthy / 可信** | Metrics can be reproduced by independent verification with the same data and method |

---

## 3. Metric Taxonomy / 指标分类法

### 3.1 L1: Format Metrics / 格式指标

Purpose: Verify artifact structural correctness before any numerical comparison.

目标：在任何数值比较之前，验证产物的结构正确性。

```python
# L1 Metrics Computation Pseudocode
def compute_l1_metrics(artifact, schema):
    """
    Returns dict of L1 metrics for a given artifact against its schema.
    """
    required = schema["required_fields"]
    field_types = schema["field_types"]
    unit_rules = schema["unit_rules"]

    total_required = len(required)
    present = sum(1 for f in required if f in artifact)
    schema_pass_rate = present / total_required if total_required > 0 else 1.0

    type_errors = 0
    for field, expected_type in field_types.items():
        value = artifact.get(field)
        if value is not None and not isinstance(value, expected_type):
            type_errors += 1

    unit_mismatches = 0
    for field, expected_unit in unit_rules.items():
        actual_unit = artifact.get(f"{field}_unit")
        if actual_unit and actual_unit != expected_unit:
            unit_mismatches += 1

    # Missing value ratio: count NaN/None across all numeric fields
    numeric_fields = [f for f, t in field_types.items() if t in (float, int)]
    total_numeric = 0
    total_missing = 0
    for field in numeric_fields:
        value = artifact.get(field)
        if value is not None:
            if isinstance(value, (list, np.ndarray)):
                arr = np.asarray(value)
                total_numeric += arr.size
                total_missing += np.sum(np.isnan(arr))
            else:
                total_numeric += 1
                if np.isnan(value):
                    total_missing += 1

    missing_ratio = total_missing / total_numeric if total_numeric > 0 else 0

    return {
        "l1_schema_pass_rate": schema_pass_rate,       # target: 1.00
        "l1_type_errors": type_errors,                  # target: 0
        "l1_unit_mismatches": unit_mismatches,          # target: 0
        "l1_missing_value_ratio": missing_ratio,        # target: < 0.01
    }
```

### 3.2 L2: Numerical Accuracy Metrics / 数值精度指标

Purpose: Compare module output against a reference (e.g., Echoview) to quantify numerical error.

目标：将模块输出与参考（如 Echoview）对比，量化数值误差。

| Metric / 指标 | Formula / 公式 | Target / 目标 | Context / 场景 |
|---|---|---|---|
| **RMSE** | sqrt(mean((x̂ - x)²)) | < 0.5 dB (Sv) | Sensitive to large errors; use when outliers matter |
| **MAE** | mean(x̂ - x) | < 0.1 dB (Sv) | Robust to outliers; use when typical error matters |
| **p95 Error** | percentile(x̂ - x, 95) | < 1.0 dB (Sv) | Shows tail behavior; use when worst-case matters |
| **Max Error** | max(x̂ - x) | < 3.0 dB (Sv) | Hard limit; use for safety/regulatory bounds |
| **Pass Rate** | mean(x̂ - x < tol) | > 0.95 | Fraction within tolerance; use for overall quality |
| **IoU** | intersect / union | > 0.85 | Detection masks; use for spatial agreement |
| **Precision** | TP / (TP + FP) | > 0.90 | When false positives are costly |
| **Recall** | TP / (TP + FN) | > 0.85 | When missed detections are costly |
| **F1 Score** | 2 * P * R / (P + R) | > 0.87 | Balanced metric; use when both matter |
| **mAP** | mean(AP per class) | > 0.85 | Object detection overall quality |
| **Relative Error** | (x̂ - x) / x | < 5% | NASC, biomass estimates |
| **Mean Bias** | mean(x̂ - x) | < 0.01 dB | Systematic offset detection |

```python
def compute_l2_metrics(predicted, reference, metric_type, tolerance=None):
    """
    Compute L2 numerical accuracy metrics.
    """
    diff = np.asarray(predicted) - np.asarray(reference)
    abs_diff = np.abs(diff)

    metrics = {
        "l2_rmse": float(np.sqrt(np.mean(diff**2))),
        "l2_mae": float(np.mean(abs_diff)),
        "l2_p95_error": float(np.percentile(abs_diff, 95)),
        "l2_max_error": float(np.max(abs_diff)),
        "l2_mean_bias": float(np.mean(diff)),
        "l2_sample_count": len(diff),
    }

    if tolerance is not None:
        metrics["l2_pass_rate"] = float(np.mean(abs_diff < tolerance))

    if metric_type == "detection_mask":
        pred_mask = np.asarray(predicted).astype(bool)
        ref_mask = np.asarray(reference).astype(bool)
        intersection = np.sum(pred_mask & ref_mask)
        union = np.sum(pred_mask | ref_mask)
        metrics["l2_iou"] = float(intersection / union) if union > 0 else 0.0
        metrics["l2_precision"] = float(intersection / np.sum(pred_mask)) if np.sum(pred_mask) > 0 else 0.0
        metrics["l2_recall"] = float(intersection / np.sum(ref_mask)) if np.sum(ref_mask) > 0 else 0.0
        metrics["l2_f1"] = (2 * metrics["l2_precision"] * metrics["l2_recall"] /
                            (metrics["l2_precision"] + metrics["l2_recall"])) if (metrics["l2_precision"] + metrics["l2_recall"]) > 0 else 0.0

    elif metric_type == "relative":
        # For NASC, biomass — relative error matters
        mask = np.abs(reference) > 1e-10
        rel_diff = abs_diff[mask] / np.abs(reference)[mask]
        metrics["l2_relative_error"] = float(np.mean(rel_diff))
        metrics["l2_relative_error_p95"] = float(np.percentile(rel_diff, 95))

    return metrics
```

### 3.3 L3: Domain Plausibility Metrics / 领域合理性指标

Purpose: Verify that outputs make physical, biological, or operational sense.

目标：验证输出在物理、生物或操作层面上是否合理。

| Metric / 指标 | Check / 检查 | Threshold / 阈值 |
|---|---|---|
| **Bottom continuity** | max(|depth[i] - depth[i-1]|) | < 5 m |
| **NASC consistency** | CV of NASC within each EDSU | < 15% |
| **Biomass sanity** | |estimated - survey| / survey | < 20% |
| **Speed plausibility** | max displacement / dt | < 5 m/s (biological) |
| **Depth range** | min/max within expected | e.g., [0, 2000] m |
| **Frequency-domain** | peak frequency within transducer band | within ±5% of center |
| **Processing time** | wall clock per file | < 30 s/file |
| **Human acceptance** | reviewer agree / total | > 90% |
| **False positive rate** | FP / (FP + TN) | < 5% |
| **Traceability** | output provenance chain complete | 100% |

```python
def compute_l3_metrics(module_output, context, module_type):
    """
    Compute L3 domain plausibility metrics.
    """
    metrics = {}
    issues = []

    if module_type == "bottom_detection":
        depths = np.asarray(module_output["bottom_depth"])
        jumps = np.abs(np.diff(depths))
        metrics["l3_max_bottom_jump_m"] = float(np.max(jumps))
        metrics["l3_bottom_jump_count"] = int(np.sum(jumps > 5.0))
        metrics["l3_bottom_continuity_pass"] = metrics["l3_max_bottom_jump_m"] < 5.0

    elif module_type == "nasc":
        nasc = np.asarray(module_output["nasc"])
        edsu_ids = np.asarray(module_output["edsu_id"])
        cv_list = []
        for edsu in np.unique(edsu_ids):
            edsu_nasc = nasc[edsu_ids == edsu]
            if len(edsu_nasc) > 1 and np.mean(edsu_nasc) > 0:
                cv = np.std(edsu_nasc) / np.mean(edsu_nasc)
                cv_list.append(cv)
        metrics["l3_nasc_cv_mean"] = float(np.mean(cv_list)) if cv_list else 0
        metrics["l3_nasc_cv_max"] = float(np.max(cv_list)) if cv_list else 0
        metrics["l3_nasc_consistency_pass"] = metrics["l3_nasc_cv_mean"] < 0.15

    elif module_type == "positioning":
        positions = module_output["positions"]
        times = [p["time"] for p in positions]
        coords = np.array([[p["x"], p["y"]] for p in positions])
        speeds = []
        for i in range(1, len(positions)):
            dt = times[i] - times[i-1]
            dx = coords[i] - coords[i-1]
            speed = np.sqrt(np.sum(dx**2)) / max(dt, 0.001)
            speeds.append(speed)
        max_speed = context.get("max_biological_speed_mps", 5.0)
        speeds = np.array(speeds)
        metrics["l3_max_speed_mps"] = float(np.max(speeds)) if len(speeds) > 0 else 0
        metrics["l3_implausible_speed_count"] = int(np.sum(speeds > max_speed))
        metrics["l3_speed_plausibility_pass"] = metrics["l3_implausible_speed_count"] == 0

    elif module_type == "qa_agent":
        # Human-reviewed metrics
        total = module_output.get("total_answers", 1)
        metrics["l3_hallucination_rate"] = module_output.get("hallucination_count", 0) / total
        metrics["l3_handoff_rate"] = module_output.get("handoff_count", 0) / total
        metrics["l3_citation_coverage"] = module_output.get("cited_answers", 0) / total

    metrics["l3_issues"] = issues
    metrics["l3_overall_pass"] = len(issues) == 0

    return metrics
```

---

## 4. Metric Dashboard Template / 指标仪表盘模板

```markdown
# Validation Dashboard: [Module/System Name]

## Summary / 总览

| Level | Status | Key Metric | Value | Target |
|---|---|---|---|---|
| L1 Format | ✅/❌ | Schema pass rate | 1.00 | 1.00 |
| L2 Numerical | ✅/❌ | RMSE | 0.050 dB | < 0.5 dB |
| L3 Domain | ✅/❌ | Expert acceptance | 95% | > 90% |

## L1 Details

| Metric | Value | Target | Pass |
|---|---|---|---|
| Schema pass rate | ___ | 1.00 | ☐ |
| Type errors | ___ | 0 | ☐ |
| Unit mismatches | ___ | 0 | ☐ |
| Missing value ratio | ___ | < 0.01 | ☐ |

## L2 Details

| Metric | Value | Target | Pass |
|---|---|---|---|
| RMSE | ___ | ___ | ☐ |
| MAE | ___ | ___ | ☐ |
| p95 error | ___ | ___ | ☐ |
| Max error | ___ | ___ | ☐ |
| Pass rate | ___ | > 0.95 | ☐ |

## L3 Details

| Metric | Value | Target | Pass |
|---|---|---|---|
| Physical constraint | ___ | ___ | ☐ |
| Operational time | ___ | ___ | ☐ |
| Expert acceptance | ___ | > 0.90 | ☐ |
| Traceability | ___ | 1.00 | ☐ |

## Data Provenance / 数据溯源

- Source dataset: [anonymized description]
- Sample count: _____
- Reference tool & version: Echoview v___
- Validation date: _____
- Validator: [role / person ID]
```

---

## 5. Cross-Project Metric Reference / 跨项目指标参考

| Project | Key L2 Metric | Value | Key L3 Metric | Value |
|---|---|---|---|---|
| 02 Agent+Skill | Sv RMSE | 0.050 dB | Bottom continuity | Line-level pass |
| 03 Sonar Auto | Sv MAE | 0.0059 dB | Expert acceptance | Pipeline-level |
| 04 Multimodal RAG | Top-1 accuracy | 83.15% | Hallucination rate | 0% |
| 05 ARIS Jellyfish | mAP | 0.889 | False positive rate | TBD |
| 06 Ultrasonic Pos. | Clock sync error | corrected | Trajectory smoothness | TBD |
| 08 Fisheries Modules | Echoview benchmark | per-module | Biomass estimate error | per-module |
