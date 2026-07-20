# 第 6 章 评估与可观测性

> **本章定位**：AI 应用工程师 2026 招聘明确要求的核心能力。它是 **AI 应用的"测试与监控体系"**——既不是传统软件测试（输出非确定、断言要更智能），也不是模型训练评估（关注的是端到端应用质量，不是 benchmark 分数）。
>
> **学习目标**：能为一个 LLM/Agent/RAG 应用建立完整的**离线评估 + 在线可观测 + CI 闸门**体系，让"模型 / Prompt / 检索配置改一次"都能用数据回答"变好了还是变差了"。

---

## 为什么这是核心章节

2026 的工程图景：

- **OpenTelemetry GenAI Semantic Conventions 正式化**：`gen_ai.*` 命名空间在 2026 初退出实验，Datadog / Honeycomb / Jaeger v2 / LangSmith / OpenLLMetry 全部接入；**vendor lock-in 被打穿**，trace 数据可移植
- **三家可观测平台拉开身位**：**LangSmith**（LangChain 生态 / 2026-03 Sandboxes + NVIDIA 合作）、**Arize Phoenix**（OTel 原生 + 企业合规）、**Braintrust**（eval-driven CI/CD 一体化）；**Langfuse** 2026-01 被 ClickHouse 收购，仍是开源自托管首选
- **eval 框架格局收敛**：**Promptfoo**（YAML / red-team）+ **DeepEval**（pytest / 14+ metrics）+ **RAGAS**（RAG 专模）+ **Inspect AI**（agent / capability eval）是生产里高频出现的组合；MLflow Evaluate 是平台自带方案
- **LLM-as-Judge 已不可裸用**：position bias（GPT-4 40% 不一致）、verbosity（~15% 膨胀）、self-preference 三类偏差是必须治理的；月度对齐人评成标准 SOP
- **Agent 评估是新前沿**：两层结构 —— step-level tracing（已解决）+ outcome scoring（未解决）；trajectory efficiency、tool-call accuracy、retrieval quality 成一等公民指标；BFCL / GAIA / TRAJECT-Bench 是公开 benchmark
- **CI 评估闸门成标配**：PR 触发 → 跑 golden set → 回归则不准合并；所有上面提到的平台都把这个动作标准化了
- **online + offline 双轨**：offline 防回归、online 看真实分布漂移、refusal / retry / 拒答 / 成本异常都要监控
- **2026 共识工程实践**：**先建评估，再调 prompt**；没有 golden set 的应用等于没有版本号

---

## 章节结构（10 节）

| # | 小节 | 核心问题 |
|---|---|---|
| 6.1 | 评估与可观测基础与决策框架 | 评估 vs 可观测的边界？2026 的"评估栈"长什么样？ |
| 6.2 | 评估方法论 | 基于参考答案 / LLM-as-Judge / 人评 / 代码断言 怎么混用？ |
| 6.3 | LLM-as-Judge 深入与偏差治理 | 5 大偏差怎么测、怎么修？G-Eval、pairwise vs scoring 怎么选？ |
| 6.4 | RAG 评估 | faithfulness / context precision / answer relevancy 等 RAG 专属指标 |
| 6.5 | Agent 评估（轨迹 + 工具） | 怎么评"轨迹"而不是"答案"？BFCL / GAIA 怎么用？ |
| 6.6 | 评估框架对比与选型 | Promptfoo / DeepEval / RAGAS / Inspect AI / MLflow Evaluate 怎么选？ |
| 6.7 | OpenTelemetry GenAI 标准 | `gen_ai.*` 语义约定怎么落地？PII 怎么处理？ |
| 6.8 | 可观测平台选型 | LangSmith / Langfuse / Arize Phoenix / Braintrust / Helicone 怎么选？ |
| 6.9 | 数据集与 Golden Set 工程 | 怎么从 0 建评估数据集？怎么用 user feedback 扩充？ |
| 6.10 | CI 闸门与线上监控 | PR 闸门、A/B、drift、refusal / retry / cost 怎么监控？ |

---

## 与其他章节的边界

| 本章覆盖 | 不在本章（去别处） |
|---|---|
| 应用级评估方法论（6.2 / 6.3） | 模型 benchmark / 训练评估 → 算法工程师范畴 |
| RAG 应用评估（6.4） | RAG 系统本体 → **ch2** |
| Agent 轨迹与工具评估（6.5） | Agent 本体（推理 / memory / 工具）→ **ch3** |
| OTel GenAI + 平台选型（6.7 / 6.8） | 通用 APM / 基础设施监控 → DevOps 范畴 |
| LLM-as-Judge 偏差治理（6.3） | 通用 prompt 工程 → **1.3** |
| CI 评估闸门（6.10） | 通用 CI/CD → 工程范畴 |
| 拒答 / 安全相关在线监控（6.10 提及） | Prompt 注入 / 内容审核 / 合规主体 → **ch8** |

---

## 学习路径建议

- **必经**：6.1 → 6.2 → 6.3 是评估方法论地基
- **垂域**：6.4（RAG）/ 6.5（Agent）按业务方向深挖
- **选型**：6.6（eval 框架）→ 6.8（可观测平台）在动手前对照
- **标准**：6.7（OTel GenAI）越早接入越省搬迁成本
- **上线**：6.9（数据集）→ 6.10（CI 闸门 + 线上）是生产化必修

---

## 主要参考（章节级，子节内还有更具体的来源）

- [Latitude · Best LLM Observability Tools 2026](https://latitude.so/blog/best-llm-observability-tools-agents-latitude-vs-langfuse-langsmith)
- [Braintrust · Langfuse Alternatives 2026](https://www.braintrust.dev/articles/langfuse-alternatives-2026)
- [Anudeep · LangSmith vs Arize vs Braintrust 2026](https://anudeepsri.medium.com/langsmith-vs-arize-vs-braintrust-e397e4728a76)
- [Future AGI · LLM-as-Judge Best Practices 2026](https://futureagi.com/blog/llm-as-judge-best-practices-2026)
- [Future AGI · LLM-Judge Bias Mitigation 2026](https://futureagi.com/blog/evaluating-llm-judge-bias-mitigation-2026/)
- [Future AGI · Agent Evaluation Frameworks 2026](https://futureagi.com/blog/agent-evaluation-frameworks-2026)
- [Future AGI · Evaluating Tool-Calling Agents 2026](https://futureagi.com/blog/evaluating-tool-calling-agents-2026/)
- [Confident AI · LLM Agent Evaluation Complete Guide](https://www.confident-ai.com/blog/llm-agent-evaluation-complete-guide)
- [OpenTelemetry · Inside the LLM Call: GenAI Observability](https://opentelemetry.io/blog/2026/genai-observability/)
- [MLflow · OpenTelemetry GenAI Semantic Conventions](https://mlflow.org/docs/latest/genai/tracing/opentelemetry/genai-semconv/)
- [genai.qa · Promptfoo vs DeepEval vs RAGAS 2026](https://genai.qa/blog/promptfoo-vs-deepeval-vs-ragas/)
- [CallSphere · RAG Evaluation Frameworks 2026](https://callsphere.ai/blog/rag-evaluation-frameworks-2026-ragas-trulens-deepeval)
- [LangChain · LLM Evals: Production Monitoring to Regression](https://www.langchain.com/articles/llm-evals)
- [Braintrust · 5 Best AI Evaluation Tools 2026](https://www.braintrust.dev/articles/best-ai-evaluation-tools-2026)
