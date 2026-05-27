# Architecture Diagrams & Data Flows / 系统架构图与数据流

This document collects the key Mermaid architecture and data flow diagrams across the portfolio projects for centralized reference.

本文档汇总了作品集中各项目的核心 Mermaid 系统架构图与数据流图，便于集中查阅。

---

## Project 02: Agent + Skill Algorithm Engineering System

### Architecture: Two-Loop Design

```mermaid
flowchart TD
    subgraph AL["Algorithm Engineering Loop / 算法工程循环"]
        A["User Task / 用户任务"] --> B["Agent Planner / Agent 规划器"]
        B --> C["Skill Registry / Skill 注册中心"]
        C --> D["Contract Checker / 契约检查器"]
        D --> E["Module Executor / 模块执行器"]
        E --> F["Validator (L1/L2/L3) / 验证器"]
        F --> G{"Pass Threshold? / 通过阈值?"}
        G -- "Pass / 通过" --> H["Register Module + Deliver Interface<br>注册模块 + 交付接口"]
        G -- "Fail / 失败" --> I["Failure Signature / 失效特征"]
    end

    subgraph RL["Research Intelligence Loop / 研究情报循环"]
        L["Scheduled Collection<br>定时采集 (Hermes/OpenClaw)"] --> M["Source Items / 来源条目"]
        M --> N["Tech Cards / 技术卡片"]
        N --> O["Feasibility Reports / 可行性报告"]
    end

    I --> J["Technical Reserve Retrieval / 技术储备检索"]
    O --> J
    J --> K["Experiment Plan / 实验方案"]
    K --> F
```

### Data Flow: Agent Workflow

```mermaid
sequenceDiagram
    participant User as User / 用户
    participant Planner as Agent Planner
    participant Registry as Skill Registry
    participant Checker as Contract Checker
    participant Executor as Module Executor
    participant Validator as Validator (L1/L2/L3)
    participant Reference as Echoview Reference
    participant Reserve as Research Reserve

    User->>Planner: Submit task / 提交任务
    Planner->>Registry: Query required Skills / 查询所需 Skill
    Registry-->>Planner: Return Skill manifests / 返回 Skill 清单
    Planner->>Checker: Validate input contract / 验证输入契约
    Checker-->>Planner: Contract OK / 契约通过
    Planner->>Executor: Execute module / 执行模块
    Executor-->>Planner: Module output / 模块输出
    Planner->>Validator: Run L1 check / 执行 L1 检查
    Validator->>Validator: Schema, type, unit checks
    Planner->>Validator: Run L2 check / 执行 L2 检查
    Validator->>Reference: Compare against Echoview
    Reference-->>Validator: Reference values
    Validator-->>Planner: L2 results (pass/fail)

    alt L2 fails
        Planner->>Reserve: Retrieve related techniques
        Reserve-->>Planner: Candidate experiments
        Planner->>Planner: Draft experiment plan
        Planner->>Executor: Re-execute with corrections
        Executor-->>Planner: Updated output
        Planner->>Validator: Re-validate
    end

    Planner->>Validator: Run L3 check / 执行 L3 检查
    Validator-->>Planner: Domain plausibility results

    alt All pass
        Planner->>Registry: Register validated module
        Planner->>User: Deliver interface + report
    end
```

---

## Project 03: Sonar Data Processing Automation

### Architecture: 6-Stage Pipeline

```mermaid
flowchart LR
    subgraph Stage1["Stage 1: Data Ingestion / 数据接入"]
        A["Raw EK80/EK60 File"] --> A1["Format Detection"]
        A1 --> A2["Metadata Extraction"]
        A2 --> A3["Sample Validation"]
    end

    subgraph Stage2["Stage 2: Acoustic Variables / 声学变量"]
        A3 --> B1["Sv Calculation"]
        B1 --> B2["GPS Association"]
        B2 --> B3["Transducer Calibration"]
    end

    subgraph Stage3["Stage 3: Noise Cleaning / 噪声清洗"]
        B3 --> C1["Background Removal"]
        C1 --> C2["Impulse Filtering"]
        C2 --> C3["SNR Thresholding"]
    end

    subgraph Stage4["Stage 4: Lines & Regions / 线与区域"]
        C3 --> D1["Bottom Detection"]
        D1 --> D2["Surface Exclusion"]
        D2 --> D3["ROI Definition"]
    end

    subgraph Stage5["Stage 5: Biological / 生物解释"]
        D3 --> E1["Single Target Detection"]
        E1 --> E2["School Detection"]
        E2 --> E3["AI Pre-review"]
    end

    subgraph Stage6["Stage 6: Aggregation / 聚合报告"]
        E3 --> F1["NASC Calculation"]
        F1 --> F2["EDSU Segmentation"]
        F2 --> F3["Report & Feishu Delivery"]
    end

    A3 -.- AG["AI Decision Gate / AI 决策门"]
    C3 -.- AG
    E3 -.- AG
```

### Data Flow: Processing Pipeline

```mermaid
sequenceDiagram
    participant Raw as Raw File
    participant Ingestion as Ingestion Module
    participant Calc as Sv Calculator
    participant Noise as Noise Remover
    participant Detect as Line Detector
    participant Bio as Bio Interpreter
    participant Agg as Aggregator
    participant AI as AI Pre-review
    participant Human as Human Expert
    participant Deliver as Feishu Delivery

    Raw->>Ingestion: Load EK80 file / 加载 EK80 文件
    Ingestion->>Ingestion: Parse header, extract metadata
    Ingestion->>Calc: Raw power + metadata
    Calc->>Calc: Compute Sv (calibrated)
    Calc->>Noise: Sv data
    Noise->>Noise: Apply noise masks
    Noise->>AI: Pre-review: noise quality check
    AI-->>Noise: Quality assessment
    Noise->>Detect: Cleaned Sv
    Detect->>Detect: Find bottom/surface lines
    Detect->>Bio: Sv + lines
    Bio->>Bio: Detect targets, schools
    Bio->>AI: Pre-review: detection quality
    AI-->>Bio: Quality assessment
    Bio->>Agg: Processed data
    Agg->>Agg: NASC, EDSU, statistics
    Agg->>AI: Pre-review: aggregation sanity
    AI-->>Agg: Quality assessment
    Agg->>Human: Results for expert review / 专家审阅
    Human-->>Agg: Approved / corrections
    Agg->>Deliver: Final report + echograms
    Deliver->>Deliver: Send to Feishu / 发送至飞书
```

---

## Project 04: Multimodal RAG Fish Identification

### Architecture

```mermaid
flowchart TD
    Image["Fish Image / 鱼类图像"] --> CLIP["CLIP ViT-L/14<br>Visual Feature Extractor"]
    Image --> VL["Qwen3-VL-Plus<br>Structured Image Description"]

    CLIP --> ChromaV["Chroma Vector DB<br>Visual Embeddings"]
    VL --> Embed["text-embedding-v4<br>Semantic Embedding"]

    subgraph KB["Knowledge Base / 知识库"]
        KU["90 Knowledge Units<br>90 个知识单元"]
        Morph["Morphology / 形态"]
        Color["Color/Pattern / 颜色"]
        Habitat["Habitat / 栖息地"]
        Diag["Diagnostic / 鉴别特征"]
        KU --> Morph
        KU --> Color
        KU --> Habitat
        KU --> Diag
    end

    ChromaV --> Candidates["Top-5 Visual Candidates<br>Top-5 视觉候选"]
    Embed --> SemRetrieval["Semantic Retrieval<br>语义检索"]
    SemRetrieval --> KB

    Candidates --> Expert["Qwen3-Max<br>Expert Verification"]
    KB --> Expert

    Expert --> Result["Species + Confidence<br>+ Differential Diagnosis<br>物种 + 置信度 + 鉴别诊断"]
```

### Data Flow

```mermaid
sequenceDiagram
    participant User as User
    participant CLIP as CLIP Encoder
    participant VL as Qwen3-VL-Plus
    participant Chroma as Chroma Vector DB
    participant Embed as text-embedding-v4
    participant Expert as Qwen3-Max

    User->>CLIP: Upload fish image / 上传鱼类图像
    CLIP->>Chroma: Visual feature vector
    Chroma-->>CLIP: Top-5 similar images
    User->>VL: Same image for description
    VL-->>User: Structured description (JSON)

    User->>Embed: Text description
    Embed->>Chroma: Semantic query vector
    Chroma-->>Embed: Top-10 knowledge units

    User->>Expert: Species + visual candidates + knowledge units
    Expert->>Expert: Cross-reference diagnosis
    Expert-->>User: Species name + confidence + sources + differential diagnosis
```

---

## Project 06: Ultrasonic Tag Positioning System

### Architecture

```mermaid
flowchart TD
    subgraph HL["Hardware Layer / 硬件层"]
        Tags["Ultrasonic Tags (60 kHz)<br>超声波标签"]
        RX["Receiver Array<br>接收机阵列"]
        Tags --> RX
    end

    subgraph DI["Data Ingestion / 数据接入"]
        Raw["Raw Data Files / CSV"]
        DB["MySQL Database<br>3.54M records"]
        Fetcher["DatabaseDataFetcher<br>数据库获取器"]
        Raw --> Fetcher
        DB --> Fetcher
    end

    subgraph PL["Processing Layer / 处理层"]
        Fetcher --> Sep["Pinger/Beacon<br>Separation"]
        Sep --> Filter["Signal Filtering<br>信号筛选"]
        Filter --> Sync["Clock Synchronization<br>校时 (3-stage)"]
        Sync --> Depth["Depth Calculation<br>深度计算"]
        Depth --> TDOA["TDOA Positioning<br>TDOA 定位"]
        TDOA --> Fallback["5-Level Fallback<br>5 级降级策略"]
    end

    subgraph VL["Visualization Layer / 可视化层"]
        Fallback --> API["Flask API"]
        API --> Web["Web Dashboard / Web 仪表板"]
        subgraph Views["Dashboard Views"]
            XY["XY 2D Plot / 2D 定位图"]
            DepthV["Depth Timeline / 深度时间轴"]
            Heat["Heatmap / 热力图"]
            D3["3D Point Cloud / 3D 点云"]
        end
        Web --> Views
    end
```

### Data Flow: Real-time Processing

```mermaid
sequenceDiagram
    participant DB as MySQL Database
    participant Fetcher as DatabaseDataFetcher
    participant Cache as TimeSyncResultCache
    participant Sync as TimeSyncProcessor
    participant TDOA as TDOA Calculator
    participant Solver as Position Solver
    participant Repo as ProcessedDataRepository
    participant API as Flask API
    participant Web as Web Dashboard

    Web->>API: Request update (every 5s) / 请求更新
    API->>Fetcher: Fetch time window data / 获取时间窗数据
    Fetcher->>DB: SELECT * WHERE write_time BETWEEN start AND end
    DB-->>Fetcher: Pinger + Beacon raw data
    Fetcher-->>API: Raw data

    API->>Cache: Check for cached sync params
    Cache-->>API: Cached or null

    API->>Sync: Synchronize clocks
    Sync->>Sync: Reference beacon alignment
    Sync->>Sync: PPS counting correction
    Sync->>Sync: UTC minute trigger alignment
    Sync-->>API: Synced timestamps
    Sync-->>Cache: Persist sync results

    API->>TDOA: Compute TDOA pairs
    TDOA-->>API: TDOA values per receiver pair

    API->>Solver: Solve position (hyperbolic)

    alt Standard positioning fails
        Solver->>Solver: Try historical parameters
        Solver->>Solver: Try joint batch
        Solver->>Solver: Try relaxed thresholds
        Solver->>Solver: Try linear prediction
    end

    Solver-->>API: Position (x, y, quality_level)
    API->>Repo: Save to database
    API-->>Web: Updated position data
    Web->>Web: Render XY, depth, heatmap, 3D
```

---

## Cross-Project Data Flow / 跨项目数据流总览

```mermaid
flowchart LR
    subgraph Domain["Domain Layer / 领域层"]
        A1["Raw Sonar Data<br>EK80/EK60/ARIS"]
        A2["Acoustic Tags<br>60 kHz Ultrasonic"]
        A3["Domain Documents<br>Papers, Books, PDFs"]
        A4["Fish Images<br>Deep-sea Specimens"]
    end

    subgraph AI["AI & Algorithm Layer / AI 与算法层"]
        B1["Acoustic Processing<br>Sv, NASC, Bottom, Noise"]
        B2["TDOA Positioning<br>Clock Sync, Depth, Fallback"]
        B3["Knowledge Extraction<br>LLM, Entity/Relation, KG"]
        B4["Multimodal RAG<br>CLIP + Qwen-VL + Chroma"]
        B5["YOLO Detection<br>+ Traditional CV"]
    end

    subgraph Sys["System Layer / 系统层"]
        C1["Skill Architecture<br>29 Modules, 10 Governance"]
        C2["Agent Orchestration<br>Plan, Retrieve, Execute, Validate"]
        C3["L1/L2/L3 Evaluation<br>Format, Numerical, Domain"]
        C4["Tech Radar<br>Scored Review Pipeline"]
    end

    subgraph Delivery["Delivery Layer / 交付层"]
        D1["Feishu Reports / 飞书报告"]
        D2["Web Dashboard / Web 仪表板"]
        D3["WeChat QA Agent / 微信问答"]
        D4["C++ DLL / Algorithm Modules"]
    end

    A1 --> B1
    A2 --> B2
    A3 --> B3
    A4 --> B4
    A1 --> B5

    B1 --> C1
    B2 --> C1
    B3 --> C1
    B4 --> C2
    B5 --> C2

    C1 --> C3
    C2 --> C3

    C3 --> D1
    C3 --> D2
    C2 --> D3
    C1 --> D4
```
