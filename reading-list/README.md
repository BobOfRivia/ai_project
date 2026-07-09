# 📚 推荐阅读 · 高质量文章资源库

> 收录值得反复读的 **AI 应用工程**优质文章来源，按组织/来源分子文件夹整理。
> 每个子文件夹一个 `README.md`：介绍该来源覆盖什么、为什么值得读、精选必读文章（带链接），并标注**对应本知识库的哪一章**。

---

## 为什么单独建这个库

训练数据会过时，二手总结会失真。**一手工程博客**（尤其是构建了这些系统的团队自己写的）是判断"什么真正落地、什么只是营销"的最可靠信源——这也是本知识库整理原则之一：**联网 + 批判性借鉴一手来源**。

---

## 子文件夹索引

| 来源 | 定位 | 主要对应章节 |
|---|---|---|
| [Anthropic Engineering](./anthropic-engineering/) | Agent / Context Engineering / RAG 的权威一手经验 | 2 RAG、3 Agent、4 编排 |
| [LangChain / LangGraph](./langchain/) | 主流编排框架的工程实践与年度趋势报告 | 3 Agent、4 编排 |
| [Microsoft](./microsoft/) | Research 博客（GraphRAG）+ Semantic Kernel 框架 | 2 RAG、3 Agent、4 编排 |
| [OpenAI](./openai/) | Cookbook 实操配方 + 官方指南 | 1 基础、2 RAG、6 评估 |
| [Google / DeepMind](./google-deepmind/) | 研究前沿 + Vertex/Gemini 工程实践 | 1 基础、5 多模态 |
| [LlamaIndex](./llamaindex/) | 数据/检索框架，RAG 落地首选之一 | 2 RAG |
| [向量数据库](./vector-databases/) | Pinecone/Weaviate/Qdrant 的高质量 RAG 学习内容 | 2 RAG |
| [个人技术博客](./individuals/) | Lilian Weng / Chip Huyen 等高信噪比作者 | 全部 |

---

## 使用建议

- **想深入某一章** → 打开对应子文件夹，从标 🔴 的必读开始。
- **判断某个新技术靠不靠谱** → 优先查一手团队博客，而不是二手教程。
- **发现好文章** → 追加到对应子文件夹的 `README.md`，保持这个库持续生长。

## 标记说明

- 🔴 必读（行业标准引用源 / 奠基性）
- 🟡 强烈推荐（对应正在建的模块）
- 🟢 实操向 / 按需查阅
