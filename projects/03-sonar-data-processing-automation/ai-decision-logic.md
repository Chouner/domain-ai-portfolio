# AI Decision Logic / AI 决策逻辑

```python
def select_pipeline(data_profile, task_goal):
    """
    Simplified public version.
    Real business rules and parameters are removed.
    公开简化版本，真实业务规则和参数已移除。
    """
    if data_profile["data_type"] == "imaging_sonar":
        return ["frame_extraction", "image_enhancement", "target_detection", "tracking"]
    if data_profile["data_type"] == "echosounder_csv":
        return ["csv_cleaning", "sv_validation", "bottom_detection", "nasc_calculation"]
    if data_profile["data_type"] == "ultrasonic_tag":
        return ["pulse_grouping", "depth_calculation", "clock_sync", "tdoa_positioning"]
    return ["manual_review"]
```

