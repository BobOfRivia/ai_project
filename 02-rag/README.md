# 第 2 章 · RAG（检索增强生成）

> **定位**：让模型用上私有/外部知识，是除 Agent 外最重要的应用模式。
> **范围**：从文档处理到检索、重排、生成、再到高级 RAG 架构与多模态 RAG。
> **不包含**：
> - 通用 Eval（→ 第 6 章），但 RAG 特有的评估指标（RAGAS、Recall@K 等）会在 2.10 工程化或对应小节附带提及
> - Embedding 模型训练（算法工程师范畴）—— 本章只讲**选型与使用**

---

## 二级结构

| § | 章节 | 核心问题 | 文件 |
|---|---|---|---|
| **2.1** | RAG 基础与决策框架 | RAG 是什么？何时用？整体架构？ | [`2.1-rag-fundamentals.md`](./2.1-rag-fundamentals.md) |
| **2.2** | 文档处理与切片 | 怎么把原始资料变成可检索单元？ | [`2.2-document-processing.md`](./2.2-document-processing.md) |
| **2.3** | 向量化与向量数据库 | 怎么存、用什么存？ | [`2.3-vectorization.md`](./2.3-vectorization.md) |
| **2.4** | 检索策略 | 怎么取出最相关的内容？ | [`2.4-retrieval.md`](./2.4-retrieval.md) |
| **2.5** | 查询理解与改写 | 怎么让用户的问题更适合被检索？ | [`2.5-query-understanding.md`](./2.5-query-understanding.md) |
| **2.6** | 重排（Reranking） | 怎么把检索结果按相关性精排？ | [`2.6-reranking.md`](./2.6-reranking.md) |
| **2.7** | 上下文组装与生成 | 怎么把检索结果给到模型？ | [`2.7-context-generation.md`](./2.7-context-generation.md) |
| **2.8** | 高级 RAG 架构 | Self-RAG / CRAG / GraphRAG / Agentic RAG / Long RAG | [`2.8-advanced-rag.md`](./2.8-advanced-rag.md) |
| **2.9** | 多模态 RAG / 文档智能 | 表格、图表、复杂 PDF、多模态检索 | [`2.9-multimodal-rag.md`](./2.9-multimodal-rag.md) |
| **2.10** | RAG 工程化 | 增量更新、数据版本、监控、A/B、上线运维 | [`2.10-rag-engineering.md`](./2.10-rag-engineering.md) |

---

## 各节定位

### 2.1 RAG 基础与决策框架
- **关键问题**：RAG 是什么、整体架构（Indexing / Retrieval / Generation 流水线）、**RAG vs 长上下文 vs 微调** 的决策矩阵、主流框架对比（LangChain / LlamaIndex / Haystack / Dify）
- **目标**：能在一个新场景下判断"该不该用 RAG、用什么形态的 RAG"

### 2.2 文档处理与切片
- **关键问题**：文档加载（Loaders）、版面解析（Unstructured / LlamaParse / Marker / pdfplumber）、切片策略（Fixed-size / Recursive / Semantic / Document-aware）、chunk size 与 overlap 权衡
- **目标**：能为不同格式（PDF / Word / HTML / Markdown / 代码）选对处理管线

### 2.3 向量化与向量数据库
- **关键问题**：Embedding 模型选型（OpenAI / Cohere / BGE / Voyage / 中文模型）、维度与成本权衡、向量库对比（Pinecone / Qdrant / Milvus / Weaviate / pgvector / Chroma）、元数据过滤、多租户与权限
- **目标**：能为业务量级、预算、部署形态选对向量库与 Embedding 模型

### 2.4 检索策略
- **关键问题**：稠密检索（Dense / Vector）、稀疏检索（BM25 / SPLADE）、**混合检索**（Hybrid，含 RRF 融合）、元数据过滤、Top-K 与召回率权衡
- **目标**：能搭出符合业务召回质量要求的检索层

### 2.5 查询理解与改写
- **关键问题**：Query Rewriting、Query Expansion、HyDE（Hypothetical Document Embeddings）、Multi-query Retrieval、Step-back Prompting、Routing（多源路由）
- **目标**：能针对"用户提问与文档表达不一致"的核心痛点做工程化优化

### 2.6 重排（Reranking）
- **关键问题**：为什么需要重排（召回与排序解耦）、Cross-encoder Reranker、LLM-as-Reranker、主流模型（Cohere Rerank / BGE-Rerank / Jina）、Latency 与质量权衡
- **目标**：能用重排把 Top-K 候选精排到 Top-N，显著提升生成质量

### 2.7 上下文组装与生成
- **关键问题**：Context 拼装顺序、Prompt 模板（如何把检索结果塞进 prompt）、**引用与溯源**（Citations / Source attribution）、幻觉抑制、**Lost-in-the-middle** 应对（重要内容放头尾）
- **目标**：能让模型的输出既"用得上检索结果"又"能被审计追溯"

### 2.8 高级 RAG 架构
- **关键问题**：**Self-RAG**（自我反思）、**CRAG**（Corrective RAG，纠错）、**GraphRAG**（Microsoft 知识图谱 RAG）、**Agentic RAG**（Agent 驱动的 RAG）、**Long RAG**（大块 + 长上下文模型）
- **目标**：能识别这些架构的适用场景，知道工程代价

### 2.9 多模态 RAG / 文档智能
- **关键问题**：表格 RAG（结构化提取 + 检索）、图表理解（Chart / Diagram VQA）、复杂 PDF（多栏 / 公式 / 嵌入图）、多模态向量化（CLIP / 多模态 Embedding）、混合存储策略
- **目标**：能处理"非纯文本"场景下的 RAG 需求

### 2.10 RAG 工程化
- **关键问题**：增量更新（文档变更检测、向量更新）、数据版本管理、**RAG 评估**（Recall@K / MRR / NDCG / RAGAS 框架）、监控（检索质量漂移、命中率）、A/B 实验、Latency 优化、Cost 控制
- **目标**：能让 RAG 系统在生产环境稳定运行、可观测、可演进

---

> 各节内部展开见后续讨论。
