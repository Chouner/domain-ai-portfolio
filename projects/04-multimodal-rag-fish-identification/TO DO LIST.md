# 工作计划
### step1 整理图片数据，整理为带物种、数量、位置、描述、质量分析的表格用于索引

### step2 根据物种索引，做mvp时挑选10%的样本，做验证

### step3 设计数据库结构和物种描述的json模板，查找权威数据源，使用爬虫工具进行检索和收集，并按照模板存储

### step4 根据描述，对文本和图片进行切片得到rag_chunks.jsonl（每个物种切了8个chunks）入库做embedding

### step5 模型选择与流程设计，输入图片 → [CLIP] 图像向量化 → 视觉库召回Top10候选物种 → [Qwen-VL] 对图片生成结构化字段描述→ [text-embedding-v4] 对描述向量化 → Chroma检索（限定在Top10候选内） → [Qwen-Max] 生成识别报告

### step6 根据步骤搭建pipeline，并整理测试机，设计指标

### step7 安装 chromadb/open-clip-torch、构建 CLIP/Chroma 索引，也没有调用 Qwen API 跑，制作测试集，记录测试结果

### step8 对测试结果进行评估，记录问题和改进方向


