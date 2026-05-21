# Clock Synchronization / 接收机校时

## Why Clock Sync is Needed / 为什么需要校时

TDOA positioning depends on arrival-time differences. Receiver clock offset can be mistaken as propagation delay.

TDOA 定位依赖到达时间差，接收机时钟偏移会被误认为传播时延。

## Stage 1: Reference Beacon / 阶段 1：参考信标

Uses fixed reference beacon and known receiver geometry.

使用固定参考信标和已知接收机几何关系。

## Stage 2: PPS Counting / 阶段 2：PPS 计数

Tracks receiver drift through periodic coded signals.

通过周期编码信号跟踪接收机漂移。

## Stage 3: UTC Minute Trigger / 阶段 3：UTC 分钟触发

Uses UTC time labels to align receiver signals in short windows. More robust to reboot and missing initial signals.

使用 UTC 时间标签在短窗口内对齐接收机信号，对重启和初始信号缺失更鲁棒。

