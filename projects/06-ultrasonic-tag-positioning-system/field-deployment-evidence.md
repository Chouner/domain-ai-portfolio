# Field Deployment Evidence / 现场数据与项目演进

## Yichang Field Processing / 宜昌现场处理

The Yichang project extends the same ultrasonic tag positioning workflow into batch field processing. It organizes multi-hour and multi-day receiver data, time synchronization, pinger processing, depth calculation, TDOA localization, trajectory merging, smoothing, heatmap generation, and animation outputs.

宜昌现场项目将同一套超声波标记定位流程扩展到批量现场数据处理，覆盖多小时/多日接收机数据、时间同步、标记处理、深度计算、TDOA 定位、轨迹合并、平滑、热力图和动画输出。

The workflow includes:

- receiver time synchronization and corrected relative time output
- pinger signal processing and first-pulse marking
- depth calculation with valid-depth flags
- per-pinger merged data generation
- 3D TDOA localization with depth as the Z value
- 2D fallback localization when depth is unavailable
- YAPS smoothing and trajectory visualization
- heatmap and animation generation

Field evidence includes 6,300 CSV files, 121 data directories under the main field-data folder, and 992 visual artifacts across HTML, PNG, and GIF outputs.

## Wangyuhe Algorithm Migration / 望虞河算法迁移

The Wangyuhe materials contributed field requirements and algorithm migration evidence for receiver coordinate handling, GPS averaging, GPS-to-Cartesian conversion, double-pulse beacon depth configuration, and TDOA localization based on at least three receivers.

望虞河相关材料补充了接收机坐标处理、GPS 多日平均、GPS 到局部笛卡尔坐标转换、双脉冲参考标定配置和三接收机以上 TDOA 定位的现场需求与算法迁移证据。

The local processing scripts show a pipeline that:

- normalizes raw receiver CSV data
- uses Pinger 33 measurements to estimate time-offset models
- filters receivers and target pinger codes
- keeps records matching configured pulse periods
- merges pinger observations across receivers
- converts receiver GPS coordinates into a local Cartesian frame
- clusters pulse events and solves hyperbolic localization for events captured by at least three receivers

## Engineering Boundaries / 工程边界

Field datasets include successful intermediate outputs and unfinished engineering iterations. One Yichang batch result recorded successful time synchronization and pinger processing across 119 entries, while the downstream depth processor failed due to a Python indentation error. This is retained as process evidence for algorithm integration and batch-run hardening, not as a completed production batch result.

现场数据包含已完成的中间结果，也包含工程迭代边界。宜昌批处理记录中，119 个条目的时间同步和标记处理成功，但后续深度处理阶段因 Python 缩进错误失败。该材料作为算法集成和批处理稳定性建设的过程证据保留，不作为完整生产批处理结果声明。
