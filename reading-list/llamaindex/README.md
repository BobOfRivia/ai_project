# LlamaIndex

> 入口：博客 <https://www.llamaindex.ai/blog> · 文档 <https://docs.llamaindex.ai>

**数据/检索框架**的官方博客，是文档密集型 RAG 的落地首选之一（最认真对待"检索质量"）。内容偏摄取、索引、检索、查询引擎的工程模式。

**覆盖主题**：文档解析（LlamaParse）、索引与检索模式、Agentic RAG、结构化数据、生产部署（LlamaAgents）。

---

## 精选必读

- 🔴 [LlamaIndex Blog](https://www.llamaindex.ai/blog) — 框架新特性与 RAG 模式一手发布。2025 年末重要发布：**LlamaParse v2**（解析）、**LlamaAgents**（一键部署）、LlamaSheets（乱表格）、LlamaSplit（文档切分）。→ **第 2 章**
- 🟡 [Understanding LlamaIndex（文档）](https://docs.llamaindex.ai) — 从零到可跑 RAG 管线的最快路径；Parent-Document、Small-to-Big 等检索模式有现成实现（见本库 2.2/2.6）。→ **第 2 章**
- 🟢 [LlamaParse](https://docs.llamaindex.ai/en/stable/llama_cloud/llama_parse/) — 复杂 PDF 解析，本库 2.2 提到的托管解析方案。

---

## 备注

生产团队常见组合（见本库 2.1）：**LlamaIndex 做摄取与检索 + LangGraph 做编排与 Agent**。学 RAG 检索层，LlamaIndex 文档比大多数二手教程更靠谱。
