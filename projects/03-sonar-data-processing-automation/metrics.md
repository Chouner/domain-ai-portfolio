# Sonar Automation Metrics

## 1. Data Processing Metrics

| Metric | Formula | Use |
|---|---|---|
| File success rate | `processed_raw_files / total_raw_files` | Batch reliability |
| Artifact completeness | `generated_required_artifacts / required_artifacts` | Delivery readiness |
| Valid sample ratio | `valid_cells / total_cells` | Data quality |
| Runtime per raw file | `total_runtime / raw_file_count` | Throughput |
| Manual intervention count | count of human-blocking events | Automation level |

## 2. Algorithm Accuracy Metrics

| Stage | Metrics | Target / Interpretation |
|---|---|---|
| Raw-to-Sv | RMSE, MAE, p95 absolute error, max absolute error | RMSE < 0.5 dB |
| Noise removal | SNR gain, mask IoU, false removal rate | IoU > 0.85 |
| Surface/bottom | MAE meters, p95 error, break count | bottom error < 1 m |
| NASC/EDSU | relative error, valid cell ratio | relative error < 5% |
| AI pre-review | precision, recall, false alarm rate, reviewer agreement | reviewer acceptance > 90% |

## 3. Current Evidence

| Evidence | Value | Status |
|---|---:|---|
| 38 kHz raw files compared | 133 | validated slice |
| Matched pings | 1,596 | validated slice |
| Samples per ping in comparison | 54,541 | validated slice |
| Valid sample comparisons | 87,047,436 | validated slice |
| Sv RMSE | 0.050 dB | pass |
| Sv MAE | 0.0059 dB | pass |
| Sv p95 absolute difference | 0.0038 dB | pass |
| Sv max absolute difference | 4.824 dB | tail error retained for review |
| Demo transducers | 6 | SDK artifact test |
| Demo pings per transducer | 12 | SDK artifact test |
| Historical CSV shape | 1,747 x 4,006 | exploration evidence |
| Columns retained after empty-column removal | 2,402 | 59.96% retained |
| Columns removed | 1,604 | 40.04% removed |
| Historical Sv range | -133.87 to -4.15 dB | exploration evidence |

## 4. Evidence Boundary

- The 38 kHz raw-to-Sv comparison is treated as validated evidence.
- Historical CSV cleaning and bottom-detection experiments are treated as exploration evidence only.
- Background-noise and median-filter comparisons need revalidation under corrected no-data semantics; the previous `-999` assumption should not be used as final evidence.
- Final production claims should include the dataset version, frequency, calibration assumptions, no-data definition, parameter set, reference export, and validation script hash.