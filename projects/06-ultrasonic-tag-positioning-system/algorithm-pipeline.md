# Algorithm Pipeline / 算法流水线

## Raw Signal Model / 原始信号模型

Receivers output detected acoustic events after chip-level decoding. Each row contains the detected tag code, receiver ID, acoustic frequency, receiver-relative arrival time, and a correlation score. The tag code is decoded from the pulse waveform pattern, while the correlation score reflects waveform similarity as calculated inside the chip.

接收机输出的是芯片完成一次识别后的声学事件数据，包含信标编号、接收机编号、频率、相对到达时间和相关性。信标编号来自发波波形匹配，相关性代表识别波形与目标编码波形的一致程度。

## Depth Calculation / 深度计算

Depth is calculated from double-pulse tags. The algorithm identifies valid `code1` and `code2` pulse pairs, calculates the pulse interval, and converts it into depth using tag-specific calibration coefficients. The calibration coefficients come from pressure equipment tests and are configured per tag pair.

深度由双脉冲标记单独计算。算法识别有效的 `code1` 与 `code2` 配对，计算两个脉冲之间的时间差，再使用标记对应的标定参数还原深度。

Depth filtering combines multiple conditions:

- transmission-period consistency
- first-arrival extraction
- valid `code1` / `code2` interval range
- valid water-depth range
- sliding-window median jump filtering
- cross-receiver depth consistency checking
- speed and acceleration constraints for depth selection in field trajectories

## Multipath Handling / 多径处理

Shallow-water environments produce dense multipath reflections and background false detections. The project supports two filtering strategies.

The period-consistency strategy keeps records whose neighboring `code1` interval matches the configured tag transmission period. It is suited for post-processing and cleaner deployments because it follows the physical emission rule strictly.

The first-arrival strategy finds a credible `code1` first arrival in short clustered detections and then searches for `code2` inside the valid pulse interval window. It is suited for shallow-water deployments where multipath reflection makes strict period filtering too sparse.

## Clock Synchronization / 接收机校时

TDOA localization depends on time differences across receivers. The project evolved through three synchronization schemes:

- Reference beacon synchronization: uses a fixed beacon with known position and known receiver distances to estimate receiver time offsets.
- PPS counting synchronization: uses periodic PPS-like acoustic codes to estimate receiver drift relative to the starting signal.
- UTC minute-key synchronization: uses UTC-tagged minute events, compares the same UTC event across receivers, and applies short-window offset correction for real-time processing.

The validation pipeline uses Tag33 / UTC-style correction. It groups Tag33 detections by minute key, estimates receiver offsets with median statistics, and applies the offsets to other tag detections before TDOA grouping.

## TDOA Localization / TDOA 定位

After depth calculation and clock synchronization, the system groups first-arrival detections from multiple receivers within a 0.5 second window. A localization event requires at least 3 receivers.

The solver fixes depth as the Z value and solves the planar `(x, y)` position with a hyperbolic TDOA model. The iterative solver minimizes the residual between theoretical time-difference distance and observed receiver time difference. Boundary constraints are applied to avoid accepting positions outside the deployment area.

## Trajectory Processing / 轨迹处理

Field outputs are further processed with boundary filtering, speed constraints, and trajectory smoothing. The system includes YAPS-style smoothing and visualization outputs for sparse positioning points, with caveats retained when point density is not sufficient for scientific speed interpretation.
