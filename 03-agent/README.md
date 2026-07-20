# 第 3 章 Agent 开发

> **本章定位**：AI 应用工程师在 2026 的**核心战场**。Agent 是从"模型即服务"走向"自主软件"的关键拐点。
>
> **学习目标**：能独立设计、实现、上线一个生产级 Agent 系统 —— 工具、上下文、记忆、协作、UX、安全、可观测全栈打通。

---

## 为什么这是核心章节

2026 年的工程图景：

- **Workflow vs Agent 的设计分水岭**已被业界共识（Anthropic《Building Effective Agents》），大多数生产系统仍是 Workflow
- **双协议标准成型**：MCP（纵向，agent ↔ tool）已是事实标准，SDK 月下载量破亿；A2A（横向，agent ↔ agent）由 Google 发起，150+ 企业采纳
- **Context Engineering 替代了 Prompt Engineering** 在 agent 场景中的位置（Gartner 2025 宣告）
- **Memory 层**形成四强格局（Mem0 / Letta / Zep / LangMem）+ 新兴的主动式记忆模式
- **Reasoning Models 改变了 Planning 工程**：o3 / Claude-3.7 等推理模型内置规划能力，部分替代手工实现 ReAct / Plan-Execute
- **框架收敛**到 8 家主流（LangGraph / OpenAI Agents SDK / Claude Agent SDK / CrewAI / AG2 / Strands / Google ADK / Pydantic AI），LangGraph 已是生产标准
- **Harness 设计**成为长时运行 Agent 的基础设施标配（Anthropic / OpenAI 均发布专项工程指南）
- **Computer Use / Browser Agent** 从 demo 进入生产（Stagehand 等工具成熟）
- **Agent UX 与 Generative UI** 自成设计学科
- **Agent-specific 安全**（跨工具 prompt injection 攻击链）已成独立威胁模型

---

## 章节结构（12 节）

| # | 小节 | 核心问题 |
|---|---|---|
| 3.1 | Agent 基础与设计框架 | Workflow 还是 Agent？何时该让 LLM 自主决策？ |
| 3.2 | 工具工程（Agent 视角） | 如何为 agent 设计高质量的工具？并行调用与 Code-as-Orchestrator 怎么做？ |
| 3.3 | Agent 标准协议：MCP 与 A2A | MCP（纵向）和 A2A（横向）各解决什么问题，如何配合？ |
| 3.4 | Context Engineering | token 预算怎么分配？5 层上下文如何动态管理？ |
| 3.5 | Memory 系统 | 跨会话记忆四强对比；主动式记忆如何自主构建知识库？ |
| 3.6 | Planning & Reasoning 模式 | ReAct / Plan-Execute 怎么选？推理模型崛起后还需要手写规划吗？ |
| 3.7 | Multi-Agent 协作 | 单 agent 还是多 agent？怎么编排、共享状态、防死锁？ |
| 3.8 | Agent 框架对比与选型 | 8 家主流框架的取舍；以"主约束"为核心的选型决策 |
| 3.9 | AI Native UX | 流式 / 中断 / Generative UI / HITL 怎么做？ |
| 3.10 | Computer Use / Browser Agent | 桌面与浏览器自动化 agent 的工作模式与选型 |
| 3.11 | Agent 安全与护栏 | 跨工具攻击链与 4 层护栏设计 |
| 3.12 | Agent 工程化：Harness、评估与可观测 | 长时运行怎么设计 Harness？轨迹怎么评估？生产怎么观测？ |

---

## 与其他章节的边界

| 本章覆盖 | 不在本章（去别处） |
|---|---|
| Agent 视角的工具工程（3.2） | 协议层 function calling 基础 → **1.4** |
| Agent 上下文管理学（3.4） | 单轮 prompt 技巧 → **1.3** |
| MCP 与 A2A 协议（3.3） | 通用 API / HTTP 集成工程 → **ch7** |
| Agent-specific 安全（3.11，跨工具攻击） | 通用安全 / 内容审核 / PII / GDPR → **ch8** |
| Agent 评估（3.12，轨迹/工具调用准确率） | 通用 eval / LLM-as-Judge → **ch6** |
| Agent Harness / 上线工程（3.12） | 通用工程（路由 / cache / 部署） → **ch7** |
| Agent 自主循环（本章整体） | 确定性 workflow 编排 → **ch4** |
| Agent 内部 RAG 调用模式 | RAG 系统本体 → **ch2** |

---

## 学习路径建议

- **必经**：3.1 → 3.2 → 3.3 → 3.4 是地基，缺一不可
- **核心**：3.5（Memory）→ 3.6（Reasoning）→ 3.7（Multi-Agent）是 agent 能力扩展三件套
- **选型**：3.8 在动手写代码前对照决策（重要：先读 3.8 再选框架）
- **应用**：3.9（UX）→ 3.10（Computer/Browser）按场景选学
- **上线**：3.11（Security）→ 3.12（Harness + Eval + Obs）是生产化必修

---

## 各节写作状态

| 节 | 状态 | 备注 |
|---|---|---|
| 3.1 | ✅ 完成 | |
| 3.2 | ✅ 完成 | 待补：并行工具调用 / Code-as-Orchestrator 子节 |
| 3.3 | ✅ 完成 | 已改名 agent-protocols，MCP + A2A 双协议 |
| 3.4 | ✅ 完成 | 5 层模型 + token 预算 + 历史管理四策略 |
| 3.5 | ✅ 完成 | 三范式 + 五方案对比 + 生命周期（写/取/更新/遗忘） |
| 3.6 | ✅ 完成 | 五模式对比 + 推理模型如何让规划分层/变轻 |
| 3.7 | ✅ 完成 | Anthropic vs Cognition 交锋 + 五编排模式 + 三难题 |
| 3.8 | ✅ 完成 | 先问"要不要框架" + 8 家全景 + 按主约束选型 |
| 3.9 | ✅ 完成 | 三交互范式 + 流式(token/step) + HITL(notify/question/review) + Generative UI + 六信任模式 |
| 3.10 | ✅ 完成 | 三篇：总纲(路线/对比/选型) + 产品深拆(11家) + 实现深拆(九构件/Manus上下文六课/SoM) |
| 3.11 | ✅ 完成 | 独立威胁模型 + 致命三要素(断链) + 完整攻击链(含记忆投毒) + 四层护栏 + CaMeL设计级防御 |
| 3.12 | 🚧 进行中 | 三支柱拆多文件：①Harness ✅（harness-engineering，含 Pydantic AI harness-first；+ 组件深挖 harness-sandbox：沙箱是什么/解决什么/三形态选型）②评估 🔲 ③可观测 🔲 |
