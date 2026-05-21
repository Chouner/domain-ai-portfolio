# Annotation Pipeline / 标注流程

## Annotation Problem / 标注问题

Manual annotation is expensive because sonar targets are fuzzy and domain-specific.

由于声呐目标模糊且具有领域特异性，人工标注成本较高。

## AI-assisted Annotation Design / AI 辅助标注设计

1. Extract sonar frames / 提取声呐帧
2. Use multimodal model for candidate target description / 使用多模态模型描述候选目标
3. Generate structured JSON annotation / 生成结构化 JSON 标注
4. Human review and correction / 人工复核与修正
5. Convert to YOLO format / 转换为 YOLO 格式
6. Train detection model / 训练检测模型

## Annotation Template / 标注模板

- frame_id / 帧 ID
- target_description / 目标描述
- duration / 持续时间
- morphology / 形态
- motion_pattern / 运动模式
- target_position / 目标位置
- bounding_box / 边界框
- confidence / 置信度

