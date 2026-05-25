# Pipeline Validation / 流水线验证

## Test Data / 测试数据

The validation slice uses `test_data/depth_parser/raw_slice.csv`, a two-hour field data slice from 2025-11-29 08:00 to 10:00. It contains 9,836 raw receiver detections from receivers R1, R2, R3, and R7, covering Pinger_1 and Pinger_6 double-pulse depth tags.

验证数据切片来自 `test_data/depth_parser/raw_slice.csv`，覆盖 2025-11-29 08:00 到 10:00 两小时现场数据，包含 R1、R2、R3、R7 四个接收机和 Pinger_1、Pinger_6 两组双脉冲深度标记。

## Step Outputs / 步骤输出

| Step | Output | Purpose |
|---|---|---|
| Step 0 | `step0_output.csv` | Unwrap receiver-relative time into monotonic time |
| Step 4+5 | `step45_output.csv` | Extract first-arrival candidates |
| Step 5.5 | `step55_output.csv` | Validate first-arrival credibility under multipath |
| Step 6-8 | `step68_output.csv` / `cpp_step68_output.csv` | Match double pulses and calculate depth |
| Layer 2 | `layer2_output.csv` / `cpp_layer2_output.csv` | Sliding-window median filtering |
| Layer 3 | `layer3_output.csv` / `cpp_layer3_output.csv` | Cross-receiver depth validation |
| Step A | `stepA_output.csv` / `cpp_stepA_output.csv` | Tag33 / UTC clock synchronization |
| Step B | `stepB_output.csv` / `cpp_stepB_output.csv` | Multi-receiver TDOA grouping |
| Step C | `stepC_output.csv` / `cpp_stepC_output.csv` | TDOA localization output |

## Reproducibility Structure / 可复现结构

The project keeps intermediate CSV outputs for each major step. This makes algorithm failures traceable to the specific stage: raw data quality, time unwrapping, first-arrival selection, double-pulse pairing, depth filtering, clock correction, TDOA grouping, or localization solving.

项目保留每个主要步骤的中间 CSV 输出，使问题能够定位到具体阶段：原始数据质量、时间展开、首达波选择、双脉冲匹配、深度过滤、校时偏移、TDOA 分组或定位求解。

## Delivery Form / 交付形态

The delivery materials are organized around algorithm reproducibility, result verification, and system integration.

交付内容围绕算法可复现、结果可验证、系统可集成组织。

- Web system for historical processing, real-time data display, depth visualization, positioning results, and trajectory display.
- Python algorithm scripts for depth parsing, clock synchronization, TDOA localization, parameter tuning, and field data review.
- C++ algorithm implementation for integrating the core depth and localization pipeline into external systems.
- Interface-oriented input and output formats covering device ID, receiver coordinates, tag parameters, algorithm parameters, region boundary, raw receiver detections, depth results, localization results, statistics, and repeat/exception data.
- Field test data slices covering multiple receivers, multiple tags, and complete processing windows.
- Step-by-step test outputs for depth calculation, synchronization, TDOA grouping, and localization validation.
- Parameter documentation covering receiver coordinates, tag codes, transmission periods, depth calibration coefficients, valid depth ranges, sound speed, grouping windows, boundary constraints, and filtering thresholds.

## Parameter Sources / 参数设定来源

| Source | Parameters |
|---|---|
| Hardware and calibration | tag code, transmission period, pulse mode, depth `a/b` coefficients |
| Field deployment | receiver coordinates, receiver groups, reference beacon / Tag33 configuration, boundary, water-depth range |
| Algorithm filtering | first-arrival window, correlation threshold, pulse interval range, depth jump threshold, cross-receiver validation window, TDOA grouping window, residual threshold |
