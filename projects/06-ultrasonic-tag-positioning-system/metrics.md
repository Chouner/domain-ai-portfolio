# Metrics / 指标

## Validation Slice / 验证数据切片

| Metric | Value | Note |
|---|---:|---|
| Raw receiver records | 9,836 | `test_data/depth_parser/raw_slice.csv` |
| Test window | 2025-11-29 08:00-10:00 | Two-hour field slice |
| Receivers in validation slice | 4 | R1, R2, R3, R7 |
| Depth beacon pairs | 2 | Pinger_1, Pinger_6 |
| Time-unwrapped records | 9,836 | Step 0 output |
| Validated depth records | 3,276 | Layer 3 / C++ depth output |
| TDOA grouping records | 2,644 | Step B output |
| Python localization outputs | 491 | Step C output |
| C++ localization outputs | 510 | C++ Step C output |

## Algorithm Parameters / 算法参数

| Parameter | Value | Purpose |
|---|---:|---|
| Minimum receivers for TDOA group | 3 | Ensures solvable localization event |
| TDOA grouping window | 0.5 s | Groups same acoustic event across receivers |
| Test depth range | 0-5 m | Filters invalid depth results in shallow-water configuration |
| Cross-receiver depth validation window | 10 s | Compares depth records across receivers |
| Cross-receiver depth deviation threshold | 0.2 m | Filters inconsistent depth values |
| Tag33 correlation threshold | 500 | Clock synchronization anchor filtering |

## Historical Project Evidence / 历史项目证据

| Metric | Value | Note |
|---|---:|---|
| Pulse groups parsed | 4,720 | Earlier positioning dataset |
| Optimal TDOA data pairs selected | 25,064 | Earlier positioning dataset |
| Valid depth values calculated | 21,292 | Earlier positioning dataset |
| Records retained after quality filtering and clock correction | 14,701 | Earlier positioning dataset |
| Tags positioned | 4 | Earlier positioning dataset |
| Valid depth range | 60.09-80.07 m | Earlier deep-water positioning dataset |
| Maximum receiver clock offset corrected | 129.52 s | Earlier positioning dataset |

## Field Batch Evidence / 现场批处理证据

| Metric | Value | Note |
|---|---:|---|
| Yichang project CSV files | 6,300 | Field data and intermediate outputs |
| DATA_from20251128 directories | 121 | Multi-hour / multi-day field data structure |
| DATA_from20251128 CSV files | 1,164 | Batch processing source files |
| Visual artifacts | 992 | HTML / PNG / GIF outputs across experiments |
| V6 speed-filtered trajectory points | 78 | One-hour Pinger_9 trajectory sample |
| V6 removed overspeed points | 1 | Speed threshold: 3.0 m/s |
| YAPS smoothed max speed | 0.111 m/s | One-hour smoothed trajectory sample |
| YAPS average deviation from V6 | 3.51 m | Smoothed trajectory comparison |

## Metrics Not Claimed / 未声明指标

- Absolute positioning error against independent ground truth.
- Production-wide positioning success rate across all deployments.
- Hardware-level tag decoding accuracy inside the receiver chip.
- Scientific movement-speed conclusion from sparse points without deployment-specific review.
