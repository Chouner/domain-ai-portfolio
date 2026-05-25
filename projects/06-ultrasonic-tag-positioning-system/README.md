# Ultrasonic Tag Positioning System / 超声波标记定位系统

## Overview / 项目概述

This project builds the data processing and positioning system for the company's 60 kHz ultrasonic tag and receiver product. The system converts raw receiver detections into depth, synchronized TDOA groups, positioning outputs, and trajectory visualization for underwater target monitoring.

本项目围绕公司的 60 kHz 超声波标记与接收机产品，构建水下目标三维定位的数据处理与算法系统。系统处理接收机输出的原始声学识别数据，包括信标编号、接收机编号、频率、相对到达时间和相关性，完成深度反演、接收机校时、TDOA 定位、边界约束和轨迹平滑，输出水下目标的位置、深度和运动轨迹。

## Product Context / 产品背景

The product consists of two hardware parts: ultrasonic tags and acoustic receivers. A tag emits coded 60 kHz acoustic pulses at configurable transmission periods. The nominal period is determined by the hardware clock and is based on multiples of 1.2 seconds, while field data contains small tag-specific period fluctuations caused by hardware and deployment conditions.

产品由超声波标记和接收机两部分组成。超声波标记使用 60 kHz 换能器发射编码声波，可配置多个发射周期。理论发射时间由硬件时钟决定，通常以 1.2 秒为基础倍数；实际现场数据中，每个标记的发射间隔会存在细微差异。

The tag does not directly transmit a numeric depth value. It converts sensor readings into pulse intervals. In two-pulse mode, the interval between two acoustic pulses encodes depth. In three-pulse mode, the tag can encode both depth and temperature. Depth conversion parameters are calibrated with pressure/vacuum equipment by simulating pressure at different depths and fitting the corresponding `a/b` coefficients.

标记发送深度信息时采用脉冲间隔编码。芯片将传感器读数转换为两个脉冲之间的时间间隔；双脉冲模式传输深度，三脉冲模式可同时传输深度和温度。深度转换参数来自压力/真空设备标定，通过模拟不同深度对应的压力并拟合曲线，得到每个标记对应的 `a/b` 参数，再由时间差还原深度。

## System Architecture / 系统架构

The software system combines a Flask backend, Pandas/NumPy algorithm modules, Plotly-based web visualization, Python validation scripts, and a C++ implementation for integration delivery.

系统由 Flask 后端、Pandas/NumPy 算法模块、Plotly Web 可视化、Python 验证脚本和 C++ 工程实现组成。主处理链路由 `backend/services/processing_pipeline.py` 编排，串联数据读取、接收机选择、时钟同步、脉冲分组、深度计算、TDOA 定位、边界约束、YAPS 轨迹平滑和结果保存。

```text
Receiver raw data
  -> time unwrap
  -> first-arrival extraction
  -> double-pulse matching
  -> depth calibration
  -> depth outlier filtering
  -> cross-receiver validation
  -> Tag33 / UTC clock synchronization
  -> TDOA grouping
  -> LM hyperbolic localization
  -> boundary filtering
  -> trajectory smoothing
  -> database / visualization / interface output
```

## Core Technical Work / 核心技术工作

- Processed raw receiver detections containing tag ID, receiver ID, frequency, relative arrival time, and correlation score.
- Built depth calculation logic for double-pulse acoustic tags, including pulse pairing, depth calibration, valid range filtering, and temporal consistency checks.
- Designed filtering strategies for shallow-water multipath environments, including period consistency filtering and first-arrival based matching.
- Implemented clock synchronization across receivers using reference beacon logic and Tag33 / UTC minute-key correction.
- Built TDOA grouping and localization logic that converts synchronized receiver detections into 2D position results with fixed depth as the Z value.
- Added boundary constraints and trajectory smoothing to improve field visualization and downstream interpretation.
- Maintained Python and C++ implementations of the depth and localization pipeline for algorithm iteration, validation, and integration delivery.

## Key Modules / 关键模块

| Module | Responsibility |
|---|---|
| `backend/services/processing_pipeline.py` | Main processing orchestration |
| `backend/algorithms/depth_calculator.py` | Double-pulse depth calculation |
| `backend/algorithms/time_sync.py` | Receiver clock synchronization |
| `backend/algorithms/tdoa_locator.py` | TDOA localization |
| `backend/services/boundary_service.py` | Boundary constraint loading and filtering |
| `backend/models/track_smooth/yaps_trajectory_optimizer.py` | Trajectory smoothing |
| `scripts/depth_parser_test/pipeline.py` | Python depth validation pipeline |
| `scripts/depth_parser_test/pipeline_locator.py` | Python localization validation pipeline |
| `scripts/depth_parser_test/cpp/pipeline.cpp` | C++ depth and localization implementation |
| `depth_parser_delivery/` | Integration package, test data, outputs, and parameter documentation |

## Related Evidence / 相关证据

- [Metrics](metrics.md)
- [Algorithm Pipeline](algorithm-pipeline.md)
- [Pipeline Validation](pipeline-validation.md)
- [Field Deployment Evidence](field-deployment-evidence.md)
- [Clock Synchronization Notes](clock-synchronization.md)
- [Signal Principle](signal-principle.md)
