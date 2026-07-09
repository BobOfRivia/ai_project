# 向量数据库（Pinecone / Weaviate / Qdrant）

向量库厂商的"学习中心"往往是**高质量、厂商中立度尚可的 RAG 教学内容**（讲检索原理、混合检索、评估，不只是卖产品）。

> - **Pinecone Learn**：<https://www.pinecone.io/learn/>
> - **Weaviate Blog**：<https://weaviate.io/blog>
> - **Qdrant Articles**：<https://qdrant.tech/articles/>

**覆盖主题**：向量检索原理、ANN 索引（HNSW/IVF）、混合检索、重排、评估、多向量/Late Interaction、多模态检索。

---

## 精选必读

- 🔴 [Pinecone Learn](https://www.pinecone.io/learn/) — RAG/向量检索最系统的免费教学之一：chunking、检索、重排、评估都有深入长文。→ **第 2 章**
- 🟡 [Weaviate Blog](https://weaviate.io/blog) — 混合检索（vector + BM25 + 过滤）讲得最透之一；重排 nDCG 提升的基准数据常被引用（见本库 2.6）。→ **第 2 章**
- 🟡 [Qdrant Articles](https://qdrant.tech/articles/) — 检索性能、多向量/ColPali、Late Interaction 落地文章质量高。→ **第 2 章 2.3/2.4/2.9**

---

## 备注

看厂商内容注意**利益立场**：涉及"选哪个库"时交叉对比多方（本库 2.3 已做过横评）；但涉及**通用检索原理与方法**时，这几家的技术长文质量普遍很高，可放心学。
