# Multimodal Sonar AI Methodology / 多模态声呐 AI 方法论

## Why sonar images are difficult / 为什么声呐图像困难

- Low contrast / 对比度低
- Sparse texture / 纹理稀疏
- Strong noise / 噪声强
- Multipath interference / 多路径干扰
- Weak generalization across devices / 跨设备泛化弱
- High annotation cost / 标注成本高

## General Pipeline / 通用流程

Raw sonar data -> Frame extraction -> Image enhancement -> AI-assisted annotation -> Human review -> Detection model training -> Traditional algorithm correction -> Tracking/counting/statistics -> Expert validation

原始声呐数据 -> 帧提取 -> 图像增强 -> AI 辅助标注 -> 人工复核 -> 检测模型训练 -> 传统算法修正 -> 跟踪/计数/统计 -> 专家验证

## Model Combination Strategy / 模型组合策略

| Component / 组件 | Role / 作用 |
|---|---|
| Multimodal LLM / 多模态大模型 | Pre-annotation, description, reasoning / 预标注、描述、推理 |
| YOLO / YOLO | Stable object detection / 稳定目标检测 |
| Traditional CV / 传统计算机视觉 | Filtering, morphology, counting refinement / 筛选、形态学、计数修正 |
| Expert Rules / 专家规则 | Boundary condition and validation / 边界条件与验证 |

