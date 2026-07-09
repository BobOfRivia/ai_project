# OpenAI

> - **Cookbook**（实操配方）：<https://cookbook.openai.com>
> - **官方文档/指南**：<https://platform.openai.com/docs>

OpenAI 的高价值不在"博客"，而在 **Cookbook**——以 notebook 形式给出常见任务的可运行范例，是"从零到能跑"最快的路径。官方文档里的 Prompt / 结构化输出 / 函数调用指南也是标准参考。

**覆盖主题**：Prompt 工程、结构化输出与函数调用、RAG 基础实现、评估（evals）、Assistants/Agents、嵌入与检索。

---

## 精选必读

- 🔴 [OpenAI Cookbook](https://cookbook.openai.com) — 官方配方合集。RAG、函数调用、结构化输出、评估都有可直接改用的 notebook。→ **第 1、2、6 章**
- 🟡 [OpenAI Docs — Guides](https://platform.openai.com/docs/guides) — Prompt engineering、structured outputs、function calling、embeddings 的权威说明。→ **第 1 章**
- 🟢 [Evals 指南](https://platform.openai.com/docs/guides/evals) — 官方评估框架，做回归/质量门禁的起点。→ **第 6 章**

---

## 使用建议

Cookbook 适合"我要实现 X，给我个能跑的最小例子"。**注意跨供应商差异**：本知识库默认用 Claude 模型，读 OpenAI 范例时把模型调用部分对应替换即可，核心模式（RAG/评估/结构化输出）是通用的。
