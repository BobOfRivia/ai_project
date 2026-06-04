# AI 应用开发工程师 — 知识脉络

> 定位：**AI 应用开发工程师**（工程、产品落地视角），区别于 AI 算法工程师（不深入模型训练与算法推导）。
> 用法：本文件是顶层骨架，每个模块后续单独展开为独立文档。

---

## 顶层 8 大模块

按"一个 AI 应用从想法到上线"的生命周期组织，依赖关系从下往上：

| # | 模块 | 一句话定位 |
|---|---|---|
| 1 | **模型调用与 Prompt 基础** | 所有上层模块共用的地基：API、Prompt、结构化输出、轻量微调认知 |
| 2 | **RAG（检索增强）** | 让模型用上私有知识，含多模态 RAG / 文档智能 |
| 3 | **Agent（智能体）** | 让模型自主决策、用工具、多步执行，含 MCP、Context Engineering、AI Native UX |
| 4 | **工作流编排** | 把 Prompt / RAG / Agent 组装成复杂应用（Chain、Graph、状态机） |
| 5 | **视觉 / 语音应用** | VLM 调用、图像生成、语音（ASR/TTS/实时对话）、视频 |
| 6 | **评估与可观测性** | Eval、Tracing、A/B、回归 —— 应用工程师的"测试体系" |
| 7 | **工程化与部署（含成本）** | 推理优化、成本控制、缓存、并发、限流、容灾、本地 vs 云 |
| 8 | **AI 安全与合规** | Prompt 注入、内容审查、数据隐私、合规 |

---

## 模块定位与边界

### 1. 模型调用与 Prompt 基础
- **范围**：模型生态认知、API 调用（含流式）、Prompt 工程、结构化输出与 Function Calling、性能与成本（Prompt Cache）
- **不包含**：
  - 长上下文管理 / Memory（归 Agent 的 Context Engineering）
  - **微调**（用户具备算法背景，已显式不纳入应用层学习脉络）

### 2. RAG（检索增强）
- **范围**：基础 RAG（chunking、embedding、retrieval、reranking）、高级 RAG（Hybrid、HyDE、Self-RAG、GraphRAG）、多模态 RAG / 文档智能
- **不包含**：纯检索系统设计（属传统搜索工程）

### 3. Agent（智能体）
- **范围**：ReAct / Plan-and-Execute / Reflection、Tool Use、**MCP**、**Context Engineering**（Memory、长上下文管理、工具描述优化、对话状态）、**AI Native UX**（流式 UI、人机协作、不确定性沟通）、多 Agent 协作
- **MCP 子模块**：作为工具连接标准，主要在 Agent 章节展开；工程化章节会从"数据接入"视角交叉引用

### 4. 工作流编排
- **范围**：Chain、Graph、状态机；LangGraph、LlamaIndex Workflows、Dify 等框架；Human-in-the-loop
- **与 Agent 区别**：工作流强调**确定性流程编排**，Agent 强调**自主决策**；现代框架（如 LangGraph）边界模糊但学习时分开更清晰

### 5. 视觉 / 语音应用
- **范围**：VLM 调用、图像生成（DALL-E、SD API、ControlNet）、语音（Whisper、TTS、Realtime API）、视频抽帧理解、视频生成 API
- **不包含**：多模态 RAG（已归入 RAG）、VLM/Diffusion 模型原理（算法工程师范畴）

### 6. 评估与可观测性
- **范围**：自动评估、LLM-as-Judge、人工评估、Tracing（LangSmith / LangFuse）、A/B、回归测试
- **定位**：应用工程师的"测试体系"，是 2026 招聘明确要求的技能

### 7. 工程化与部署（含成本）
- **范围**：推理优化（vLLM、缓存）、成本控制（token 计量、模型路由、降级）、限流、容灾、并发、本地部署（Ollama）vs 云 API、Serverless
- **强调**：成本控制在 2026 是核心议题

### 8. AI 安全与合规
- **范围**：Prompt 注入与越狱防护、内容审查（Moderation API）、数据隐私 / PII、各地 AI 合规（GDPR、欧盟 AI 法案）

---

## 学习路径建议（粗）

```
1 (基础) → 2 (RAG) → 3 (Agent) → 4 (工作流)
                                      ↓
            6 (评估) ←──────── 7 (工程化) ←──── 5 (多模态，按需)
                                                      ↓
                                              8 (安全，贯穿全程)
```

- **1** 是地基，必须先掌握
- **2 / 3** 是两大主线，可并行学
- **4** 在 2、3 之后学，因为它在编排前者
- **5** 按业务方向选学
- **6 / 7** 是上线必需，越早开始接触越好
- **8** 是贯穿全程的"横切"，不是阶段任务

---

## 文档组织

每个模块独立成**文件夹**，文件夹内每个知识节点独立成 `.md`，章节总览写在 `README.md`：

```
ai_project/
├── OUTLINE.md
├── 01-foundation/        # 模型调用与 Prompt 基础
│   ├── README.md
│   ├── 1.1-model-ecosystem.md
│   ├── 1.2-api-basics.md
│   ├── 1.3-prompt-engineering.md
│   ├── 1.4-structured-output.md
│   └── 1.5-performance-cost.md
├── 02-rag/               # RAG（检索增强）
├── 03-agent/             # Agent（智能体）
├── 04-orchestration/     # 工作流编排
├── 05-multimodal/        # 视觉 / 语音应用
├── 06-evaluation/        # 评估与可观测性
├── 07-engineering/       # 工程化与部署
└── 08-security/          # AI 安全与合规
```
