# 第 4 章 · 工作流编排

> **定位**：把 Prompt / RAG / Agent / 工具调用**组装成确定性流程**的工程层。
> **范围**：编排原语（Chain / Graph / 状态机）、状态管理、控制流、HITL、持久化、durable execution、主流框架与低代码平台、工作流上线工程化。
> **不包含**：
> - Agent 的自主循环与推理模式 → **第 3 章**（4 章强调"确定性"，3 章强调"自主性"）
> - 单次 prompt 技巧、结构化输出 → **第 1 章**
> - 通用 LLM 工程化（缓存 / 路由 / 部署）→ **第 7 章**

---

## 为什么单列一章

2026 年的工程图景：
- **"确定性骨架 + 定点 agent"成为业界共识架构**：deterministic backbone with intelligence deployed at specific steps；agents 由 backbone 显式调用、完成后控制权回到 backbone。换句话说，**ch4 是生产 AI 应用的结构主干，ch3 是局部能力插件**
- **Workflow vs Agent 分水岭**有了规范表述（Anthropic《Building Effective Agents》五类 pattern：Prompt chaining / Routing / Parallelization / Orchestrator-workers / Evaluator-optimizer）
- **现代编排框架两强格局**：LangGraph（graph + state + 内置 checkpointer）vs LlamaIndex Workflows（event-driven `@step` + Context API，持久化自建）；Burr / Pocket Flow / Haystack Pipelines 退为 niche
- **Durable execution 从 niche 变成 production AI 标配**：Temporal 2026-02 拿 $300M D 轮 / $5B 估值，OpenAI / Replit / Lovable 都在上面跑 agent；Restate / Inngest / Hatchet / DBOS 同档竞争；长跑、跨服务、可恢复的 LLM workflow 不再可选
- **低代码平台百花齐放**：Dify（50k+ stars + 商业云）、Coze（字节，全球 + 国内双版本）、n8n、Flowise、FastGPT、Open WebUI、Botpress、RAGFlow 同档，工程师必须判断"自建代码 vs 平台编排"
- **HITL（人机协同）** 从可选项变成**生产 workflow 的标准节点类型**，与持久化深度耦合

> **参考来源**（本章框架核对所引用，2026-06）：
> - [Anthropic · Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
> - [LangChain Docs · Workflows and Agents](https://docs.langchain.com/oss/python/langgraph/workflows-agents)
> - [Anthropic 多 agent 架构对比 (softmaxdata)](https://softmaxdata.com/blog/agent-architectures-compared/)
> - [Morph · LLM Workflows 2026](https://www.morphllm.com/llm-workflows)
> - [The New Stack · Temporal Replay 2026](https://thenewstack.io/temporal-replay-2026-news/)
> - [Render · Durable workflow platforms for AI agents](https://render.com/articles/durable-workflow-platforms-ai-agents-llm-workloads)
> - [Jimmy Song · Open Source AI Agent Platform 对比 2026](https://jimmysong.io/blog/open-source-ai-agent-workflow-comparison/)
> - [Knowlee · Dify Alternatives 2026](https://www.knowlee.ai/blog/dify-alternatives-2026)
> - [PkgPulse · Inngest vs Trigger.dev v3 vs Restate 2026](https://www.pkgpulse.com/guides/inngest-vs-trigger-dev-v3-vs-restate-2026)

---

## 章节结构（10 节）

| § | 章节 | 核心问题 | 文件 |
|---|---|---|---|
| **4.1** | 工作流基础与决策框架 | 什么是 LLM workflow？Workflow vs Agent 怎么落地？五类 workflow pattern？ | [`4.1-workflow-fundamentals.md`](./4.1-workflow-fundamentals.md) |
| **4.2** | 编排原语：Chain / Graph / DAG / State Machine | 用什么结构表达流程？为什么 LangGraph 选 graph、LlamaIndex 选 event-driven？ | [`4.2-orchestration-primitives.md`](./4.2-orchestration-primitives.md) |
| **4.3** | 状态管理与数据流 | 节点间怎么共享数据？State schema、reducer、消息累积模式？ | [`4.3-state-data-flow.md`](./4.3-state-data-flow.md) |
| **4.4** | 控制流：条件 / 循环 / 并发 / 子图 | 怎么表达"重试到通过"、"并发评估再聚合"、"递归子任务"？ | [`4.4-control-flow.md`](./4.4-control-flow.md) |
| **4.5** | Human-in-the-Loop 与可中断恢复 | 怎么让流程在关键节点暂停、等人批准、可恢复（interrupt / resume）？ | [`4.5-human-in-the-loop.md`](./4.5-human-in-the-loop.md) |
| **4.6** | 持久化与 Durable Execution | Checkpointer、time-travel debug、跨进程/重启幸存的工作机制 | [`4.6-persistence-durable-execution.md`](./4.6-persistence-durable-execution.md) |
| **4.7** | 主流框架对比与选型 | LangGraph / LlamaIndex Workflows / Burr / Haystack Pipelines / Pocket Flow 的取舍 | [`4.7-orchestration-frameworks.md`](./4.7-orchestration-frameworks.md) |
| **4.8** | 低代码 / 可视化编排平台 | Dify / Coze / n8n / Flowise / FastGPT 的位置，何时用、何时不用 | [`4.8-low-code-platforms.md`](./4.8-low-code-platforms.md) |
| **4.9** | 事件驱动与 Durable Execution 引擎 | Temporal / Inngest / Restate / Prefect 把 LLM workflow 当 durable function 跑的路线 | [`4.9-event-driven-durable-engines.md`](./4.9-event-driven-durable-engines.md) |
| **4.10** | 工作流工程化：部署、监控、版本、回归 | 怎么让 workflow 上生产？版本管理、灰度、回归、指标？ | [`4.10-workflow-engineering.md`](./4.10-workflow-engineering.md) |

---

## 各节定位

### 4.1 工作流基础与决策框架
- **关键问题**：LLM workflow 的定义、Anthropic 五类 pattern（Prompt chaining / Routing / Parallelization / Orchestrator-workers / Evaluator-optimizer）、**Workflow vs Agent 决策矩阵**（确定性 vs 自主性、可观测 vs 灵活性、成本 vs 能力）、**2026 共识架构**"确定性骨架 + 定点 agent"（backbone 调用 agent，控制权回到 backbone）
- **目标**：能在一个新任务前判断"该用 workflow 还是 agent，哪类 pattern"，并能用 backbone-and-agent 模式把复杂应用拆成可控结构

### 4.2 编排原语：Chain / Graph / DAG / State Machine
- **关键问题**：Chain（顺序）、DAG（无环图）、Graph（含环）、State Machine（状态机）四类抽象的表达力差异；**LangGraph 的图模型** vs **LlamaIndex Workflows 的事件驱动模型** vs **Burr 的状态机模型**
- **目标**：能为业务的流程复杂度选对编排抽象

### 4.3 状态管理与数据流
- **关键问题**：State schema（TypedDict / Pydantic）、**reducer 函数**（如何把节点输出 merge 到 state）、消息累积（`add_messages`）、共享状态 vs 局部状态、状态污染防护
- **目标**：能为复杂 workflow 设计一个清晰、可演进的 state schema

### 4.4 控制流：条件 / 循环 / 并发 / 子图
- **关键问题**：条件路由（conditional edges）、循环（带终止条件的回环，如"重试到通过"）、**并发分支与聚合**（map-reduce 风格）、子图（subgraph）与模块化、递归调用
- **目标**：能用编排原语表达常见的复杂 workflow pattern（评估-改写循环、并发评审、分治子任务）

### 4.5 Human-in-the-Loop 与可中断恢复
- **关键问题**：**Interrupt / Resume 机制**（LangGraph 的 `interrupt()`、LlamaIndex 的 `wait_for_event`）、审批节点、人工编辑节点、超时与升级、与 4.6 持久化的依赖关系
- **目标**：能在 workflow 中嵌入"等人批准"节点，且支持长时间跨度的恢复

### 4.6 持久化与 Durable Execution
- **关键问题**：**Checkpointer**（SQLite / Postgres / Redis）、**Time-travel debug**（回到任一历史 state 重放）、跨进程 / 重启幸存、Durable execution 的核心抽象（**确定性回放**）、与 HITL 的耦合
- **框架对比要点**：LangGraph **内置 checkpointer**（开箱即用）vs LlamaIndex Workflows **持久化自建**（灵活但工程量大）—— 这是 4.7 选型的关键差异之一
- **目标**：能让长跑（数分钟～数天）workflow 在崩溃 / 重启 / 人审等待下不丢状态

### 4.7 主流框架对比与选型
- **关键问题**：**两强格局** —— LangGraph（图 + 状态 + 内置 checkpointer + 内置 HITL）vs LlamaIndex Workflows（event-driven `@step` + Context API，持久化与 HITL 自建但与 RAG 链路更顺）；**niche 备选** —— Burr（状态机）/ Haystack Pipelines（DAG）/ Pocket Flow（极简图）按场景选。维度：控制力 / 学习曲线 / 持久化 / HITL / 调试体验 / 生态 / 与 RAG 链路融合度
- **与 3.8 区别**：3.8 从"agent 自主循环"视角看 LangGraph，本节从"workflow 编排能力"视角看；两节互相 see-also
- **目标**：能为具体项目从代码型框架中选一个，理解为什么"两强 + niche 备选"是 2026 的现实格局

### 4.8 低代码 / 可视化编排平台
- **关键问题**：**Dify**（开源 LLMOps，50k+ stars + 商业云）、**Coze**（字节，全球 / 国内双版本，多渠道发布）、**n8n**（通用 workflow + LangChain Agent 节点）、**Flowise**（LangChain 可视化）、**FastGPT**（RAG 强）、**Open WebUI** / **Botpress** / **RAGFlow**（同档）的定位差异；**自建代码 vs 平台编排**的决策（迭代速度 / 控制粒度 / 团队角色 / 成本 / 数据合规 / 多渠道发布）
- **目标**：能判断"PoC 用 Dify、生产改自建代码"这类常见路径，而不是非黑即白

### 4.9 事件驱动与 Durable Execution 引擎
- **关键问题**：**Temporal**（活动 + workflow，2026-02 拿 $300M D 轮 / $5B 估值，OpenAI / Replit / Lovable 都在上面跑 agent；Replay 2026 推出 serverless workers + workflow streams）、**Inngest**（事件驱动 + AgentKit）、**Restate**（durable function，service-boundary）、**Hatchet** / **DBOS**（同档新势力）、**Prefect**（数据 workflow）—— 把 LLM workflow 当 durable function 跑的路线；与 LangGraph 路线的差异（业务级编排引擎 vs LLM 原生框架）；何时该上 Temporal 而不是 LangGraph
- **2026 关键判断**：durable execution 已从 niche 变成 production AI 标配；LLM 调用 / 工具 / agent 步骤是 non-deterministic，必须放进 Activity 才能让 orchestration 可靠
- **目标**：能识别"业务级 durable workflow"需求（跨天、跨服务、强一致），并在 LLM 原生框架与传统 workflow 引擎之间选对路线

### 4.10 工作流工程化：部署、监控、版本、回归
- **关键问题**：Workflow 的**版本管理**（结构变更 vs prompt 变更）、灰度发布、**回归测试**（轨迹级而非答案级，与 3.12 / ch6 交叉）、监控指标（节点 latency / 失败率 / interrupt 频率 / token 成本）、与 LangSmith / LangFuse 的集成
- **目标**：能让 workflow 在生产环境可观测、可演进、可回滚

---

## 与其他章节的边界

| 本章覆盖 | 不在本章（去别处） |
|---|---|
| 确定性流程编排（图 / 状态 / checkpoint） | Agent 自主循环 → **第 3 章整体** |
| Workflow pattern（Anthropic 五类）| ReAct / Plan-Execute / Reflexion 推理模式 → **3.6** |
| LangGraph 作为编排框架视角（4.7）| LangGraph 作为 agent 框架视角 → **3.8** |
| Workflow 内的 HITL 机制（中断 / 恢复）| 前端流式 / Generative UI / HITL 交互 UX → **3.9** |
| Workflow 通用工程化（4.10）| 通用 LLM 工程化（缓存 / 路由 / 部署）→ **第 7 章** |
| Workflow 内的 RAG 调用 | RAG 系统本体 → **第 2 章** |
| 节点 prompt 的写法 | Prompt 工程 → **1.3**；结构化输出 → **1.4** |

---

## 学习路径建议

- **必经**：4.1 → 4.2 → 4.3 是地基（什么是 workflow、用什么结构表达、状态怎么管）
- **核心**：4.4（控制流）→ 4.5（HITL）→ 4.6（持久化）是表达力三件套
- **选型**：4.7（代码框架）→ 4.8（低代码）→ 4.9（durable 引擎）三选一或组合
- **上线**：4.10 是生产化必修，与 ch6 / ch7 强联动
