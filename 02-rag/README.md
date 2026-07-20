# 第 2 章 · RAG（检索增强生成）

> **定位**：让模型用上私有/外部知识，是除 Agent 外最重要的应用模式。
> **范围**：从文档处理到检索、重排、生成、再到 Modular/Agentic RAG 架构与多模态 RAG。
> **不包含**：
> - 通用 Eval（→ 第 6 章），但 RAG 特有的评估指标（RAGAS 四件套、Recall@K 等）会在 2.10 工程化或对应小节附带提及
> - 通用 Prompt 注入与越狱防护（→ 第 8 章），本章只在 2.10 给 **RAG 特有安全**（数据投毒 / 间接注入）一个锚点并交叉引用
> - Embedding 模型训练（算法工程师范畴）—— 本章正文只讲**选型与使用**；训练原理另设**深入选读** [`2.3.1-embedding-model-training.md`](./2.3.1-embedding-model-training.md)，应用工程师按需读
>
> **一条贯穿全章的认知**：2026 年 RAG 失败点约 **73% 在检索、而非生成**——"检索是瓶颈"，本章 2.2–2.6 的篇幅权重据此倾斜。

---

## 二级结构

| § | 章节 | 核心问题 | 文件 |
|---|---|---|---|
| **2.1** | RAG 基础与决策框架 | RAG 是什么？何时用（vs 长上下文 / 微调）？整体架构？ | [`2.1-rag-fundamentals.md`](./2.1-rag-fundamentals.md) |
| **2.2** | 文档处理与切片 | 怎么把原始资料变成可检索单元？ | [`2.2-document-processing.md`](./2.2-document-processing.md) |
| **2.3** | 向量化与向量数据库 | 怎么存、用什么存（单向量 vs 多向量）？ | [`2.3-vectorization.md`](./2.3-vectorization.md) |
| ↳ 2.3.1 | Embedding 模型怎么训练（深入选读） | 在 LLM 基础上改了什么？主流训练管线？ | [`2.3.1-embedding-model-training.md`](./2.3.1-embedding-model-training.md) |
| **2.4** | 检索策略 | 怎么取出最相关的内容？ | [`2.4-retrieval.md`](./2.4-retrieval.md) |
| ↳ 2.4.1 | ANN 找最近邻（补充） | 向量库怎么在毫秒内找最近邻？HNSW/IVF/PQ 怎么调？ | [`2.4.1-ann-nearest-neighbor.md`](./2.4.1-ann-nearest-neighbor.md) |
| **2.5** | 查询理解与改写 | 怎么让用户的问题更适合被检索？ | [`2.5-query-understanding.md`](./2.5-query-understanding.md) |
| **2.6** | 重排（Reranking） | 怎么把检索结果按相关性精排？ | [`2.6-reranking.md`](./2.6-reranking.md) |
| **2.7** | 上下文组装与生成 | 怎么把检索结果给到模型？ | [`2.7-context-generation.md`](./2.7-context-generation.md) |
| **2.8** | Modular / Agentic RAG 架构 | Self-RAG / CRAG / RAPTOR / GraphRAG / Adaptive / Agentic RAG | [`2.8-modular-agentic-rag.md`](./2.8-modular-agentic-rag.md) |
| **2.9** | 多模态 RAG / 文档智能 | 表格、图表、复杂 PDF、视觉优先检索（ColPali） | [`2.9-multimodal-rag.md`](./2.9-multimodal-rag.md) |
| **2.10** | RAG 工程化 | 增量更新、评估、监控、A/B、安全、上线运维 | [`2.10-rag-engineering.md`](./2.10-rag-engineering.md) |

---

## 各节定位

### 2.1 RAG 基础与决策框架
- **关键问题**：RAG 是什么、整体架构（Indexing / Retrieval / Generation 流水线）、**RAG vs 长上下文 vs 微调** 的决策矩阵、主流框架对比（LangChain / LlamaIndex / Haystack / Dify）
- **目标**：能在一个新场景下判断"该不该用 RAG、用什么形态的 RAG"
- **说明**：CAG（Cache-Augmented Generation）本质是"长上下文 + Prompt/KV Cache"的推理优化（≈ Prompt Caching，归 1.5 / 第 7 章），非独立检索架构，本章只在决策矩阵里作为"长上下文"路线的一部分点到，不单列

### 2.2 文档处理与切片
- **关键问题**：文档加载（Loaders）、版面解析（Unstructured / LlamaParse / Marker / pdfplumber）、切片策略（Fixed-size / Recursive / Semantic / Document-aware）、chunk size 与 overlap 权衡
- **目标**：能为不同格式（PDF / Word / HTML / Markdown / 代码）选对处理管线
- **补充**：
  - **Contextual Retrieval（Anthropic）**——embedding/BM25 前给每个 chunk 预置 50–100 token 上下文摘要，检索失败率 -49%（叠加重排 -67%），靠 prompt caching 低成本落地
  - **Small-to-Big / Parent-Document**——用小块检索保精度、回溯父文档给生成足够上下文

### 2.3 向量化与向量数据库
- **关键问题**：Embedding 模型选型（OpenAI / Cohere / BGE / Voyage / 中文模型）、维度与成本权衡、向量库对比（Pinecone / Qdrant / Milvus / Weaviate / pgvector / Chroma）、元数据过滤、多租户与权限
- **目标**：能为业务量级、预算、部署形态选对向量库与 Embedding 模型
- **补充**：**单向量 vs 多向量（Multi-vector）表征**——ColBERT 式 per-token 向量与 Late Interaction 的存储/索引代价（含 PLAID、MUVERA 等压缩手段），为 2.4 的 late-interaction 检索打基础
- **深入选读 2.3.1**：Embedding 模型的训练原理——在 LLM 基础上做的改造（池化 / 双向化 / 指令）、对比学习与 InfoNCE、多阶段训练管线、hard negative 挖掘、LLM-based 现代范式（E5-mistral / LLM2Vec / NV-Embed）。算法向，应用工程师按需

### 2.4 检索策略
- **关键问题**：稠密检索（Dense / Vector）、稀疏检索（BM25 / SPLADE）、**混合检索**（Hybrid，含 RRF 融合）、元数据过滤、Top-K 与召回率权衡
- **目标**：能搭出符合业务召回质量要求的检索层
- **补充**：
  - **Late Interaction 检索（ColBERT / MaxSim）**——接近 cross-encoder 精度、接近 bi-encoder 速度，介于稠密检索与重排之间的第三条路
  - **深入选读 2.4.1**：ANN 近似最近邻——为什么要近似、三大思路（图/聚类/哈希）+ 量化压缩、**HNSW**（M/efConstruction/efSearch）、**IVF**（nlist/nprobe）、**PQ / IVF-PQ**、距离度量、filtered ANN 过滤坑、选型速查。建索引与调优时用得上
  - **结构化数据 RAG / Text-to-SQL**——精确数值、聚合、过滤类查询走 NL→SQL 直连结构化库，而非文本相似度

### 2.5 查询理解与改写
- **关键问题**：Query Rewriting、Query Expansion、HyDE（Hypothetical Document Embeddings）、Multi-query Retrieval、Step-back Prompting、Routing（多源路由）
- **目标**：能针对"用户提问与文档表达不一致"的核心痛点做工程化优化
- **补充**：**Adaptive RAG 的查询复杂度分级**——小分类器把 query 分为"直答 / 单跳检索 / 多跳检索"并路由（判别信号：多实体、因果/比较连接词等）；架构侧的完整形态在 2.8 展开，这里聚焦查询侧的分类与路由

### 2.6 重排（Reranking）
- **关键问题**：为什么需要重排（召回与排序解耦）、Cross-encoder Reranker、LLM-as-Reranker、主流模型（Cohere Rerank / BGE-Rerank / Jina）、Latency 与质量权衡
- **目标**：能用重排把 Top-K 候选精排到 Top-N，显著提升生成质量

### 2.7 上下文组装与生成
- **关键问题**：Context 拼装顺序、Prompt 模板（如何把检索结果塞进 prompt）、**引用与溯源**（Citations / Source attribution）、幻觉抑制、**Lost-in-the-middle** 应对（重要内容放头尾）
- **目标**：能让模型的输出既"用得上检索结果"又"能被审计追溯"

### 2.8 Modular / Agentic RAG 架构
- **关键问题**：**Self-RAG**（自我反思）、**CRAG**（Corrective RAG，纠错）、**RAPTOR**（递归聚类+摘要建树，多粒度检索）、**GraphRAG**（Microsoft 知识图谱 RAG）、**Adaptive RAG**（查询复杂度路由，2026 主流最佳实践）、**Agentic RAG**（Agent 驱动的 RAG）、**Long RAG**（大块 + 长上下文模型）
- **目标**：能识别这些架构的适用场景，知道工程代价
- **组织方式**：按"反思纠错（Self-RAG/CRAG）→ 结构化组织（RAPTOR/GraphRAG）→ 路由调度（Adaptive/Agentic）"三条主线归类，避免罗列
- **术语边界**：本节是三范式里的**范式③ Modular/Agentic**。范式②的 **"Advanced RAG"（查询改写+混合检索+重排等增强手段）已拆在 2.4–2.7**，两者别混（详见 2.1 第三节的术语澄清）

### 2.9 多模态 RAG / 文档智能
- **关键问题**：表格 RAG（结构化提取 + 检索）、图表理解（Chart / Diagram VQA）、复杂 PDF（多栏 / 公式 / 嵌入图）、多模态向量化（CLIP / 多模态 Embedding）、混合存储策略
- **目标**：能处理"非纯文本"场景下的 RAG 需求
- **补充**：**视觉优先 / OCR-free 检索（ColPali / ColQwen）**——把 PDF 页当图像编码为 patch 向量、MaxSim 打分，绕过 OCR 直接检索图表/表格/版面；对比传统"OCR→切块→embedding"管线的取舍与存储代价（每页约千级向量）

### 2.10 RAG 工程化
- **关键问题**：增量更新（文档变更检测、向量更新）、数据版本管理、**RAG 评估**（Recall@K / MRR / NDCG，及 **RAGAS 四件套：Context Precision / Context Recall / Faithfulness / Answer Relevance**，可区分"检索失败 vs 生成失败"）、监控（检索质量漂移、命中率）、A/B 实验、Latency 优化、Cost 控制（naive ~$0.001 / hybrid+rerank ~$0.005 / agentic $0.02–0.10 每 query 的数量级参考）
- **目标**：能让 RAG 系统在生产环境稳定运行、可观测、可演进
- **补充**：**RAG 特有安全**——数据投毒（PoisonedRAG：百万库中 5 篇恶意文档即可高概率劫持特定问答）、检索内容被默认信任导致的间接注入；此处给锚点，纵深防护交叉引用 **第 8 章**

---

> 各节内部展开见后续讨论。
