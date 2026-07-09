# Microsoft

微软有几个不同定位的高质量来源，分开看：

> - **Microsoft Research 博客**：<https://www.microsoft.com/en-us/research/blog/>
> - **GraphRAG 项目**：<https://www.microsoft.com/en-us/research/project/graphrag/> · 文档 <https://microsoft.github.io/graphrag/>
> - **Semantic Kernel DevBlog**：<https://devblogs.microsoft.com/semantic-kernel/>

**覆盖主题**：GraphRAG / 知识图谱 RAG（微软是这条路线的主要推动者）、Semantic Kernel（AI 编排框架，2026 已 27k+ stars）、Azure AI 工程实践、前沿研究。

---

## 精选必读

### GraphRAG 路线（→ 本库 2.8）

- 🔴 [GraphRAG Project](https://www.microsoft.com/en-us/research/project/graphrag/) — 知识图谱 RAG 的官方源头。核心价值：回答"全局性"问题（"数据的主要主题是什么"），这是向量 RAG 的盲区。
- 🟡 [LazyGraphRAG: Setting a New Standard for Quality and Cost](https://www.microsoft.com/en-us/research/blog/lazygraphrag-setting-a-new-standard-for-quality-and-cost/) — 大幅降低 GraphRAG 索引成本的方案。**上 GraphRAG 前必读的成本课**。
- 🟢 [GraphRAG 文档](https://microsoft.github.io/graphrag/) — 落地实现与配置。

### Semantic Kernel（→ 第 3、4 章）

- 🟢 [Semantic Kernel DevBlog](https://devblogs.microsoft.com/semantic-kernel/) — 微软的 AI 编排框架，支持 C#/Python/Java，企业/.NET 栈常用。可观测、Planner、插件等实践。

### 综合

- 🟢 [Microsoft Research Blog](https://www.microsoft.com/en-us/research/blog/) — 前沿研究一手发布，含大量 LLM/Agent/检索方向。

---

## 备注

GraphRAG 强大但**建索引比向量 RAG 贵 10–40 倍**（见本库 2.8）。读微软这几篇时重点关注**"什么时候值得"和"成本怎么降"**，而非默认升级。
