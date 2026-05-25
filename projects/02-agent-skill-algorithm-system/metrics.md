# Metrics and Measurement Plan

This file defines how the Agent + Skill algorithm system is measured. The goal is to avoid vague claims such as "improved efficiency" and turn the project into a traceable engineering asset.

## 1. Algorithm Development Efficiency

| Metric | Definition | Formula / Measurement | Target |
|---|---|---|---:|
| Module lead time | Time from confirmed contract to validation report | `report_time - contract_confirmed_time` | 0.5-2 days for normal modules |
| Clarification rounds | Number of human clarification cycles before coding | count of requirement-blocking questions | <= 2 |
| Rework cycles | Number of failed validation-remediation loops | count of remediation reports | <= 2 for P0/P1 modules |
| Contract completeness | Required contract fields filled | `filled_required_fields / total_required_fields` | >= 95% |
| Reuse ratio | Modules using existing Skills/contracts/test templates | `reused_components / total_components` | increasing over time |

## 2. Skill Library Quality

| Metric | Definition | Why It Matters |
|---|---|---|
| Skill manifest coverage | Share of modules with Purpose, Formula, Input, Output, Parameters, Evaluation, Limitations | Prevents ad-hoc algorithm calls |
| Dependency coverage | Share of modules with upstream/downstream dependency declarations | Enables Agent orchestration |
| Parameter range coverage | Share of parameters with unit, range, default, and warning rules | Reduces invalid runs |
| Test case coverage | Normal, boundary, noisy, and failure cases per module | Makes regression meaningful |
| Physical constraint coverage | Share of modules with acoustic constraints such as range, Sv units, TVG assumptions, no-data semantics | Prevents plausible but wrong outputs |

## 3. Algorithm Validation Metrics

| Module Type | Metrics | Example Threshold |
|---|---|---:|
| Raw parsing / Sv export | RMSE, MAE, p95 absolute error, max absolute error, valid sample count | RMSE < 0.5 dB |
| Background/impulse/transient noise | SNR gain, mask IoU, false removal rate, retained valid sample ratio | IoU > 0.85 |
| Surface and bottom line | MAE in meters, p95 depth error, continuity break count | MAE < 1 m |
| NASC / EDSU aggregation | Relative error, valid cell ratio, missing cell count | relative error < 5% |
| AI echo-image review | precision, recall, reviewer agreement, false alarm rate | reviewer acceptance > 90% |
| End-to-end pipeline | stage success rate, runtime per raw file, manual intervention count | trend improvement |

## 4. Technical Reserve Metrics

| Stage | Metric | Formula |
|---|---|---|
| Collection | Daily valid item count | accepted source items per run |
| Deduplication | Duplicate rate | `duplicate_items / collected_items` |
| Structuring | Extraction completeness | `filled_required_fields / required_fields` |
| Classification | Review agreement | `human_accepted_labels / reviewed_labels` |
| Retrieval | Precision@K | `relevant_candidates_in_top_k / k` |
| Experiment | Experiment conversion rate | `experiments_started / retrieved_candidates` |
| Adoption | Useful technique rate | `accepted_techniques / tested_techniques` |
| Recovery | Failure-to-report latency | `experiment_report_time - failure_detected_time` |

## 5. Current Evidence

| Evidence | Value | Status |
|---|---:|---|
| Candidate acoustic processing modules | 29 | module map completed |
| Governance Skills | 10 | design completed |
| Explicit public Skill manifests | 4 | implemented/documented |
| P0 modules | 4 | priority plan |
| P1 modules | 9 | priority plan |
| Validated raw files in 38 kHz CW comparison | 133 | completed validation run |
| Matched pings | 1,596 | completed validation run |
| Valid sample comparisons | 87,047,436 | completed validation run |
| Sv RMSE | 0.050 dB | below threshold |
| Sv MAE | 0.0059 dB | below threshold |
| Sv p95 absolute difference | 0.0038 dB | below threshold |

## 6. Decision Rules

- Pass: all required L1/L2 checks pass and the main domain metric reaches the target.
- Conditional pass: main metric passes but boundary cases need human review or additional data.
- Explore: current implementation fails, but related techniques exist in the reserve and the expected implementation cost is acceptable.
- Reject: repeated failures, weak evidence, incompatible data assumptions, or unacceptable engineering cost.