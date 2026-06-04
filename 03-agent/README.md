# 第 3 章 Agent 开发

> **本章定位**：AI 应用工程师在 2026 的**核心战场**。Agent 是从"模型即服务"走向"自主软件"的关键拐点。
>
> **学习目标**：能独立设计、实现、上线一个生产级 Agent 系统 —— 工具、上下文、记忆、协作、UX、安全、可观测全栈打通。

---

## 为什么这是核心章节

2026 年的工程图景：
- **MCP 已是事实标准**（Anthropic 2024 发布，2026 年成为"AI 的 USB-C"），下载量破亿
- **Workflow vs Agent 的设计分水岭**已被业界共识（Anthropic《Building Effective Agents》）
- **Context Engineering 替代了 Prompt Engineering** 在 agent 场景中的位置
- **Memory 层**形成四强格局（Mem0 / Letta / Zep / LangMem）
- **框架收敛**到 6 家主流（LangGraph / OpenAI Agents SDK / Claude Agent SDK / CrewAI / AG2 / Strands）
- **Computer Use / Browser Agent** 从 demo 进入生产
- **Agent UX 与 Generative UI** 自成设计学科
- **Agent-specific 安全**（跨工具 prompt injection 攻击链）已成独立威胁模型

---

## 章节结构（12 节）

| # | 小节 | 核心问题 |
|---|---|---|
| 3.1 | Agent 基础与设计框架 | Workflow 还是 Agent？何时该让 LLM 自主决策？ |
| 3.2 | 工具工程（Agent 视角） | 如何为 agent 设计高质量的工具？ |
| 3.3 | MCP (Model Context Protocol) | 如何用标准协议把工具/资源/提示接入 agent？ |
| 3.4 | Context Engineering | 如何在有限 token 预算内构造最优上下文？ |
| 3.5 | Memory 系统 | 如何让 agent 跨会话记住事实、偏好、经验？ |
| 3.6 | Planning & Reasoning 模式 | ReAct / Plan-Execute / Reflexion 怎么选？ |
| 3.7 | Multi-Agent 协作 | 单 agent 还是多 agent？怎么编排？ |
| 3.8 | Agent 框架对比与选型 | 6 家主流框架的取舍与选型框架 |
| 3.9 | AI Native UX | 流式 / 中断 / Generative UI / HITL 怎么做 |
| 3.10 | Computer Use / Browser Agent | 桌面与浏览器自动化 agent 的工作模式 |
| 3.11 | Agent 安全与护栏 | 跨工具攻击链与 4 层护栏设计 |
| 3.12 | Agent 评估、可观测与上线 | 怎么评估"轨迹"而非"答案"？ |

---

## 与其他章节的边界

| 本章覆盖 | 不在本章（去别处） |
|---|---|
| Agent 视角的工具工程（3.2） | 协议层 function calling → **1.4** |
| Agent 上下文管理学（3.4） | 单轮 prompt 技巧 → **1.3** |
| Agent-specific 安全（3.11，跨工具攻击） | 通用安全 / 内容审核 / PII / GDPR → **ch8** |
| Agent 评估（3.12，轨迹/工具调用准确率） | 通用 eval / LLM-as-Judge → **ch6** |
| Agent 自主循环（本章整体） | 确定性 workflow 编排 → **ch4** |
| Agent 工程化（3.12） | 通用工程（路由 / cache / 部署） → **ch7** |
| Agent 内部 RAG 调用模式 | RAG 系统本体 → **ch2** |

---

## 学习路径建议

- **必经**：3.1 → 3.2 → 3.3 → 3.4 是地基，缺一不可
- **核心**：3.5（Memory）→ 3.6（Reasoning）→ 3.7（Multi-Agent）是 agent 能力扩展三件套
- **选型**：3.8 在动手写代码前对照决策
- **应用**：3.9（UX）→ 3.10（Computer/Browser）按场景选学
- **上线**：3.11（Security）→ 3.12（Eval/Obs）是生产化必修
