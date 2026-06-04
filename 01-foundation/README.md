# 第 1 章 · 模型调用与 Prompt 基础

> **定位**：所有上层模块（RAG、Agent、工作流）的共用地基。
> **范围**：从"知道有哪些模型"到"用 API 把模型能力高质量、低成本地调用出来"。
> **不包含**：Memory / 长上下文管理（→ Agent 章节）、微调（用户具备算法背景，已显式排除）。

---

## 二级结构

| § | 章节 | 核心问题 | 文件 |
|---|---|---|---|
| **1.1** | 模型生态与认知 | 我面对的工具长什么样？ | [`1.1-model-ecosystem.md`](./1.1-model-ecosystem.md) |
| **1.2** | API 调用基础（含流式） | 怎么发出一次请求？ | [`1.2-api-basics.md`](./1.2-api-basics.md) |
| **1.3** | Prompt 工程 | 怎么让模型听懂？ | [`1.3-prompt-engineering.md`](./1.3-prompt-engineering.md) |
| **1.4** | 结构化输出与工具调用 | 怎么让输出能被代码消费？ | [`1.4-structured-output.md`](./1.4-structured-output.md) |
| **1.5** | 性能与成本基础 | 怎么调用得便宜又快？ | [`1.5-performance-cost.md`](./1.5-performance-cost.md) |

---

## 各节定位

### 1.1 模型生态与认知
- **关键问题**：主流模型地图、能力维度（推理 / 代码 / 速度 / 思考模式）、Token 与上下文窗口、定价结构
- **覆盖**：Claude / GPT / Gemini 闭源系，Llama / Qwen / DeepSeek / Mistral 开源系
- **目标**：能根据场景判断"用哪个模型"

### 1.2 API 调用基础（含流式）
- **关键问题**：Messages API 结构、多轮对话拼接、生成参数（temperature / top_p / seed / stop）、SDK 选择（官方 vs LiteLLM）、错误处理与重试、**流式输出（SSE）**
- **目标**：能稳定地发出一次请求，并把流式响应正确解析到前端

### 1.3 Prompt 工程
- **关键问题**：基础原则、Few-shot、CoT、XML / Markdown 结构化、角色设定、Prompt 模板管理、避坑（幻觉、拒答）
- **目标**：能写出在生产环境稳定生效的 Prompt

### 1.4 结构化输出与工具调用
- **关键问题**：JSON Mode、Structured Outputs / JSON Schema、Pydantic 集成、Function Calling、并行工具调用、流式结构化输出
- **目标**：让模型输出可以被代码直接消费，不靠脆弱的字符串解析

### 1.5 性能与成本基础
- **关键问题**：Prompt Cache（各家机制）、Token 计量与优化、模型路由（大→小降级）、Batch API、延迟 vs 质量权衡
- **目标**：能估算成本、能在不损失质量的前提下降本
