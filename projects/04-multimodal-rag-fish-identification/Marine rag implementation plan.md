# 海洋鱼类识别 RAG 系统：MVP 方案 & 完整方案

目标物种（10个）：光彩标灯鱼、粗鳞灯笼鱼、短颌灯笼鱼、钝吻灯笼鱼、巨口鱼、银斧鱼、鮟鱇、蝰鱼、眶灯鱼属、巴氏眶灯鱼

---

## 一、MVP 方案

**目标**：跑通核心链路，获得可写进简历的真实指标。  
**范围**：10个物种，每种10张测试图，单链路验证（视觉描述 → 文本RAG → 结构化输出）。  
**预计时间**：3-5天。

---

### Step 1：测试集锁定

**做什么**

从每个物种的图片文件夹里，按以下比例抽取测试图，抽完后移入独立文件夹 `test_set/`，不参与知识库构建：

| 物种 | 总图数 | 抽取测试图数 |
|---|---|---|
| 光彩标灯鱼 | 59 | 8 |
| 粗鳞灯笼鱼 | 61 | 8 |
| 短颌灯笼鱼 | 25 | 5 |
| 钝吻灯笼鱼 | 64 | 8 |
| 巨口鱼 | 269 | 10 |
| 银斧鱼 | 118 | 10 |
| 鮟鱇 | 83 | 10 |
| 蝰鱼 | 91 | 10 |
| 眶灯鱼属 | 300 | 10 |
| 巴氏眶灯鱼 | 94 | 10 |
| **合计** | | **89张** |

抽取原则：随机抽，但要覆盖不同角度（侧面、正面、局部）；低质量图不超过测试集的20%。

**记录**：建一个 `test_set_log.csv`，字段为：文件名、物种、图片质量（high/medium/low）、抽取日期。

**闭环标准**：`test_set/` 目录下10个子文件夹，总图数89张，`test_set_log.csv` 每行对应一张图，无遗漏。

---

### Step 2：知识库内容准备

**做什么**

为10个物种各准备一份结构化描述，存为 `species_profiles/物种名.json`。

字段只需填以下核心字段（MVP阶段，其余字段留空标注"待补"）：

```json
{
  "chinese_name": "",
  "scientific_name": "",
  "family": "",
  "body_shape": "",
  "head_features": "",
  "fins_summary": "",
  "photophores": "",
  "coloration_live": "",
  "depth_range_m": "",
  "geographic_range": "",
  "diagnostic_summary": "",
  "similar_species": [],
  "sources": []
}
```

**内容来源优先级**：
1. 已有docx（灯笼鱼4种）：直接提取，按字段填入
2. FishBase：`https://www.fishbase.se/summary/物种学名`，抓取形态描述和分布
3. WoRMS：确认学名和分类

**闭环标准**：10个JSON文件，每个文件 `diagnostic_summary` 字段不为空，`sources` 字段至少有1条来源记录。

---

### Step 3：搭建RAG系统

**做什么**

用已有原型改造，只保留核心链路：

```
输入图片
  → Qwen-VL 生成结构化形态描述（按模板，不允许编造不可见字段）
  → text-embedding-v4 对描述做向量化
  → Chroma 检索知识库（TopK=3）
  → Qwen-Max 生成识别报告
  → 输出：推荐物种 + 置信度 + 关键证据
```

**视觉描述提示词（固定，不随意修改）**：

```
你是鱼类形态学识别助手。请只根据图片中可见证据填写字段，不要根据常识补全图片中不可见的内容。
如果某字段无法判断，写"不可判定"并说明原因。
请按以下JSON格式输出：
{
  "body_shape": {"value": "", "confidence": 0.0-1.0},
  "head_features": {"value": "", "confidence": 0.0-1.0},
  "fins": {"value": "", "confidence": 0.0-1.0},
  "photophores": {"value": "", "confidence": 0.0-1.0},
  "coloration": {"value": "", "confidence": 0.0-1.0},
  "not_visible": []
}
```

**闭环标准**：用任意1张测试图跑通完整链路，能输出包含物种名和置信度的JSON报告，不报错。

---

### Step 4：批量测试 & 指标记录

**做什么**

对89张测试图逐张跑识别，结果记入 `test_results.csv`：

| 字段 | 说明 |
|---|---|
| image_file | 文件名 |
| true_species | 正确物种 |
| pred_top1 | 系统第一推荐 |
| pred_top3 | 系统前三推荐（逗号分隔） |
| top1_correct | 是否正确（1/0） |
| top3_correct | 是否在前三（1/0） |
| confidence | 系统输出的置信度 |
| image_quality | 图片质量 |
| notes | 错误原因备注 |

对同一张图重复跑3次，记录3次结果是否一致，用于计算一致性。

**闭环标准**：89行结果全部填入，无空行，能计算出以下4个指标：

- **Top-1准确率** = top1_correct总和 / 89
- **Top-3命中率** = top3_correct总和 / 89
- **跨轮次一致性** = 3次结果相同的图片数 / 89（取10张图做3次重复即可）
- **相似种混淆率** = 眶灯鱼属和巴氏眶灯鱼互相错认的次数 / 20

---

### Step 5：结果分析 & 简历描述

**做什么**

按以下模板写项目描述（填入真实数字）：

> 针对深海鱼类样本稀少、种间形态相似的识别难题，设计并实现基于多模态RAG的鱼类识别系统。以YOLO/CLIP方案20%准确率为基线，构建覆盖10个物种、包含结构化形态字段的多模态知识库；采用Qwen-VL进行图像字段抽取，text-embedding-v4向量化后由Chroma检索，Qwen-Max生成专家级识别报告。在89张测试图上验证：Top-1准确率XX%，Top-3命中率XX%，跨轮次一致性XX%；相似种（眶灯鱼属 vs 巴氏眶灯鱼）混淆率XX%。

**闭环标准**：数字全部来自 `test_results.csv`，无估算，可复现。

---

## 二、完整方案

**目标**：生产可用系统，支持全量物种识别，可交接给下一个人继续开发。  
**范围**：全部~110个物种，三库并行（文本RAG库 + 视觉向量库 + 结构化物种库），六阶段识别流程。

---

### Phase 1：数据与物种清单治理

**Step 1.1 学名归一化**

- 工具：WoRMS API（`https://www.marinespecies.org/rest/`）
- 对每个物种中文名查询接受学名、AphiaID、同物异名
- 输出：`species_master.csv`（中文名、接受学名、AphiaID、FishBase ID）
- 闭环标准：110个物种全部有AphiaID，无重复行，无空学名

**Step 1.2 图片质量分层**

- 基于已有 `marine_image_inventory.xlsx` 的quality字段
- 低质量图（low）单独标注，不进入主知识库，但保留路径备用
- 不可读图（unreadable=1）直接排除
- 闭环标准：每个物种文件夹有 `quality_split.json`，记录high/medium/low各自数量和路径

**Step 1.3 测试集锁定（全量版）**

- 每个物种按10%抽取测试图，图少于10张的物种抽1-2张
- 抽完后锁定，后续所有开发不得使用测试集图片
- 闭环标准：`test_set_full/` 目录，`test_set_full_log.csv` 完整记录

---

### Phase 2：知识库内容构建

**Step 2.1 自动抓取**

优先级顺序抓取，每个字段保留来源：

```python
# 伪代码结构
for species in species_master:
    profile = {}
    profile['taxonomy'] = fetch_worms(species.aphia_id)
    profile['morphology'] = fetch_fishbase(species.fishbase_id)
    profile['distribution'] = fetch_fishbase_distribution(species.fishbase_id)
    profile['sources'] = log_sources()
    save_json(profile, f"species_profiles/{species.chinese_name}.json")
```

- FishBase抓取字段：体长范围、体形、分布水深、地理范围、鳍条数
- WoRMS抓取字段：接受学名、同物异名、分类阶元
- 闭环标准：110个JSON文件，每个文件`sources`字段有≥1条记录，`morphology`不为空

**Step 2.2 大模型字段抽取与补全**

对自动抓取后仍为空的字段，用大模型从原始文本中抽取：

```
提示词：请从以下文本中抽取鱼类形态字段，只抽取文本中明确提到的内容，
不要补全或推断，没有提到的字段输出null。
字段：体形、头部特征、鳍型、发光器分布、体色、水深范围。
文本：{raw_text}
```

- 每次抽取后人工抽查5%
- 闭环标准：`diagnostic_summary` 字段填充率≥90%，`photophores` 字段对灯笼鱼科填充率100%

**Step 2.3 相似种对比知识块**

为外形相近的物种对，手动或用大模型生成差异描述：

```json
{
  "type": "comparison_pair",
  "species_a": "眶灯鱼属",
  "species_b": "巴氏眶灯鱼",
  "key_differences": [],
  "shared_features": [],
  "diagnostic_tip": ""
}
```

- 优先处理：所有灯笼鱼科内部、眶灯鱼属内各种
- 闭环标准：同科相似种对比块覆盖率≥80%

---

### Phase 3：三库构建

**Step 3.1 文本RAG库（Chroma）**

每个物种入库4类文档块：

| 块类型 | 内容 | 元数据 |
|---|---|---|
| species_profile_full | 完整结构化描述转自然语言 | species_id, family, confidence |
| diagnostic_features | 只含鉴别特征 | species_id, field_type |
| source_excerpt | 带来源的短文本证据 | source_name, retrieved_date |
| comparison_pair | 相似种差异 | species_a, species_b |

- embedding模型：text-embedding-v4
- 闭环标准：Chroma中文档总数 = 物种数×4（最少），可用脚本验证count

**Step 3.2 视觉向量库（CLIP）**

对全量9019张图片（排除测试集和低质量图）生成CLIP embedding：

```python
# 每张图保存
{
  "image_path": "",
  "species_id": "",
  "quality": "high/medium",
  "clip_embedding": [],  # 512维或768维
  "visible_angle": "lateral/dorsal/ventral/head",  # 人工或模型判断
}
```

- 工具：OpenCLIP（`ViT-B/32`起步，后期可升级SigLIP）
- 存储：Chroma的image collection或Qdrant
- 闭环标准：embedding总数 = 入库图片数，无遗漏，可用随机5张查询验证召回正确

**Step 3.3 结构化物种库（SQLite）**

存储完整的 `species_profile.jsonl`，支持精确过滤：

```sql
-- 核心查询示例
SELECT * FROM species 
WHERE family = '灯笼鱼科' 
AND depth_min <= 500 AND depth_max >= 200
AND photophores_present = 1
```

- 闭环标准：110个物种全部入库，可按科、水深、发光器有无执行过滤查询

---

### Phase 4：六阶段识别流程实现

按已有方案文档实现，优先级顺序：

1. **阶段C**（视觉字段抽取）：最高优先，是MVP核心
2. **阶段E**（RAG证据校验）：第二优先
3. **阶段F**（输出报告）：第三优先
4. **阶段B**（CLIP视觉召回）：Phase 3完成后接入
5. **阶段D**（结构化过滤）：SQLite完成后接入
6. **阶段A**（图像预处理）：最后接入，可先用简单质量检查替代

每个阶段接入后，用测试集跑一次完整指标，记录版本和数字变化。

---

### Phase 5：评估与迭代

**全量评估指标**（对应方案文档第9节）：

| 指标 | 计算方式 | 目标值 |
|---|---|---|
| Top-1准确率 | 正确数/总测试图数 | >80% |
| Top-5命中率 | 前5命中数/总测试图数 | >95% |
| 科级准确率 | 科正确数/总测试图数 | >95% |
| 相似种混淆率 | 同科错认数/同科测试总数 | <15% |
| 一致性 | 3次相同结果数/抽测图数 | >80% |
| 字段抽取完整率 | 非空字段数/总字段数 | >85% |
| 人工复核通过率 | 专家确认正确数/推荐总数 | 记录即可 |

**错误分析流程**：
1. 按物种统计错误率，找出Top 3错误最多的物种
2. 分析错误原因：知识库缺失、图片质量差、相似种混淆
3. 针对性补充知识块或相似种对比块
4. 重新跑测试集，记录指标变化

---

### Phase 6：交接文档

每个模块交接时附：

- **模块说明**：这个模块做什么、输入输出是什么
- **运行方式**：一条命令能跑起来
- **验证方法**：怎么确认这个模块跑对了
- **已知问题**：当前存在的缺陷和局限
- **待办项**：下一步建议做什么

交接文档模板路径：`docs/handover/模块名_handover.md`

---

## 三、两份方案对照

| 维度 | MVP | 完整方案 |
|---|---|---|
| 物种数 | 10 | ~110 |
| 图片数 | 测试89张，知识库其余图不入视觉库 | 全量~9000张入视觉库 |
| 知识库类型 | 文本RAG库（Chroma） | 三库并行（文本+视觉+结构化） |
| 识别链路 | C→E→F（3个阶段） | A→B→C→D→E→F（6个阶段） |
| 评估指标 | 4个 | 7个 |
| 预计时间 | 3-5天 | 2-3个月 |
| 产出 | 真实指标 + 简历描述 | 可交接的完整系统 |
