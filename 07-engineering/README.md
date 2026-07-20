# 第 7 章 工程化与部署（含成本）

> **定位**：应用工程师把"能跑的 demo"变成"经得起线上、扛得住流量、付得起账单"的产品。覆盖**推理优化、部署形态、网关、缓存、限流容灾、Serverless、FinOps**七大主题。
> **与第 6 章关系**：第 6 章解"质量看得见"，第 7 章解"工程跑得稳、账单付得起"——两者共同支撑"上线"。

---

## 2026 全景信号

- **推理服务侧 3+1 格局**：vLLM（默认）/ SGLang（共享前缀、agent / RAG 场景反超）/ TensorRT-LLM（H100 极致延迟 +15-30%），**TGI 已于 2025-12 仅接收 bug fix**；HuggingFace Inference Endpoints 默认 vLLM
- **Prompt caching 是 2026 成本头号杠杆**：Anthropic **90% off**（5min / 1h TTL）、OpenAI 50% off（≥1024 token，自动）、Gemini 按存储时长计费；真实工况 55-82% 月支出下降
- **Hybrid 部署成为共识**：baseline → 自托管（vLLM），overflow → cloud API，frontier → 云（GPT-5 / Claude），PII → 自托管；自托管 **breakeven ≈ 1B token/月 + 60-70% 利用率**
- **LLM Gateway 三档**：LiteLLM（开源 Python、100+ 模型）/ Portkey（企业、<1ms 开销、self-host）/ OpenRouter（200+ 模型 SaaS 市场，100-150ms 开销）
- **Serverless GPU 主流是 Modal / RunPod / Replicate**，AWS Lambda **官方明确无 GPU**；Modal GPU snapshot 让冷启动 <2s（部分模型 10x 加速）
- **推理优化共识栈**：FP8 + Flash Attention 3 + continuous batching + speculative decoding，比朴素 FP16 提升 **5-8x cost-efficiency**
- **KV cache 压缩前沿**：vLLM FP8 KV cache 已 GA、2x 压缩零损；**TurboQuant**（Google, ICLR 2026）压缩 6x、attention 加速 8x
- **FinOps 进入主流**：tag / count / aggregate / enforce 四杠杆；chargeback vs showback；Portkey / LiteLLM gateway-layer 强制预算
- **可靠性 3 个原语**：token bucket per identity / circuit breaker per pattern / fallback chain per route（"传统 SRE 适配 LLM——浪费单位从 ms/请求变成 $/token"）

---

## 章节小节地图

| 小节 | 主题 | 你应该带走的判断 |
|---|---|---|
| 7.1 | 工程化与部署基础与决策框架 | 从 demo 到 prod 的 4 层视角（推理 / 网关 / 应用 / 治理）；SLO/SLA 怎么定 |
| 7.2 | 推理优化技术总览 | 应用工程师角度：FP8 / PagedAttention / continuous batching / speculative decoding 该懂什么、不该懂什么 |
| 7.3 | 推理服务框架选型 | vLLM 默认 / SGLang 共享前缀 / TensorRT-LLM 极致延迟 / Ollama 开发；TGI 已停更 |
| 7.4 | 部署形态决策（自托管 / Serverless / 云 API） | Hybrid 是 2026 默认；breakeven 1B token/月；fintech 案例 47k→8k |
| 7.5 | LLM Gateway 与多 provider 编排 | LiteLLM vs Portkey vs OpenRouter；什么时候自建网关 |
| 7.6 | 成本优化体系 | 五大杠杆 + 优先级：caching > routing > 压缩 > batching > 模型选小 |
| 7.7 | 缓存策略深入 | KV / prompt（provider 端）/ semantic / exact-match；命中率工程；Redis LangCache |
| 7.8 | 限流、并发、容灾、流式 | 3 原语（token bucket / circuit breaker / fallback chain）+ bulkhead + SSE 并发 |
| 7.9 | Serverless GPU 与冷启动治理 | Modal / RunPod / Replicate / Bedrock；GPU snapshot、keepalive 14 min |
| 7.10 | FinOps 与成本治理 | 四杠杆（tag / count / aggregate / enforce）；chargeback / showback；anomaly detection |

---

## 与其他章节边界

| 章节 | 关系 |
|---|---|
| 第 1 章 模型调用 | 1.5 节讲 prompt cache、token 计量入门；本章把它升级为**生产 FinOps 体系** |
| 第 3 章 Agent | Agent 的工具调用并发、重试归本章工程化处理；agent 逻辑本身归 3 章 |
| 第 4 章 工作流 | 工作流是逻辑编排，本章是底层基础设施（限流 / 容灾 / 推理服务） |
| 第 6 章 评估与可观测 | 6 章管"质量信号"，7 章管"性能 / 成本 / 可用性信号"；OTel GenAI 同源 |
| 第 8 章 安全合规 | 数据脱敏、地理隔离与本章"部署形态决策"耦合（PII → 自托管） |

**不包含**：
- 模型训练 / 微调（用户已显式排除）
- 算法侧的 attention / batching 实现细节（理解即可，不深入）
- 通用云架构（K8s / Serverless 基础）——只讲 LLM 特有部分

---

## 学习路径建议

```
7.1 基础 + 决策框架
   ↓
7.2 推理优化技术 → 7.3 推理服务框架 → 7.4 部署形态决策
                                          ↓
                              7.5 Gateway 与多 provider 编排
                                          ↓
                              7.6 成本优化总览 → 7.7 缓存深入
                                          ↓
                              7.8 限流容灾 + 7.9 Serverless
                                          ↓
                                   7.10 FinOps 治理
```

- **想理解"为什么 LLM 部署难"**：从 7.1 + 7.2 开始
- **想立刻省钱**：直接 7.6 + 7.7（90% 团队 ROI 最高）
- **想搭生产骨架**：7.3 + 7.5 + 7.8
- **想做 hybrid 自托管**：7.3 + 7.4 + 7.9
- **想建治理体系**：7.10 配合第 6 章

---

## 章节级主要参考（2026）

- [Yotta Labs · Best LLM Inference Engines 2026: vLLM / SGLang / TensorRT-LLM / TGI](https://www.yottalabs.ai/post/best-llm-inference-engines-in-2026-vllm-tensorrt-llm-tgi-and-sglang-compared)
- [Spheron · vLLM vs TensorRT-LLM vs SGLang H100 Benchmarks 2026](https://www.spheron.network/blog/vllm-vs-tensorrt-llm-vs-sglang-benchmarks/)
- [LabelYourData · LLM Inference: Techniques for Optimized Deployment 2026](https://labelyourdata.com/articles/llm-fine-tuning/llm-inference)
- [Mavik Labs · LLM Cost Optimization 2026: Routing, Caching, Batching](https://www.maviklabs.com/blog/llm-cost-optimization-2026)
- [PreMAI · 8 Strategies That Cut LLM API Spend by 80%](https://blog.premai.io/llm-cost-optimization-8-strategies-that-cut-api-spend-by-80-2026-guide/)
- [TokenMix · Prompt Caching Guide 2026: 50-95% Cost Savings](https://tokenmix.ai/blog/prompt-caching-guide)
- [PromptHub · Prompt Caching with OpenAI, Anthropic, Google](https://www.prompthub.us/blog/prompt-caching-with-openai-anthropic-and-google-models)
- [Klymentiev · LLM Gateway 2026: OpenRouter vs LiteLLM vs Portkey vs Helicone](https://klymentiev.com/blog/llm-gateway-guide)
- [Spheron · AI Gateway Setup 2026: LiteLLM, Portkey, Kong](https://www.spheron.network/blog/ai-gateway-litellm-portkey-kong-gpu-cloud/)
- [Effloow · Self-Hosting LLMs vs Cloud APIs 2026](https://effloow.com/articles/self-hosting-llms-vs-cloud-apis-cost-performance-privacy-2026)
- [DigitalApplied · Self-Hosting Open-Weight LLMs 2026 Decision Guide](https://www.digitalapplied.com/blog/self-hosting-open-weight-llms-2026-deployment-decision-guide)
- [Glukhov · LLM Hosting 2026: Local, Self-Hosted, Cloud Compared](https://www.glukhov.org/llm-hosting/)
- [PreMAI · Serverless LLM Deployment: RunPod vs Modal vs Lambda 2026](https://blog.premai.io/serverless-llm-deployment-runpod-vs-modal-vs-lambda-2026/)
- [Blaxel · AWS Lambda GPU Support in 2026](https://blaxel.ai/blog/aws-lambda-gpu)
- [Maxim · Retries, Fallbacks, Circuit Breakers in LLM Apps](https://www.getmaxim.ai/articles/retries-fallbacks-and-circuit-breakers-in-llm-apps-a-production-guide/)
- [TrueFoundry · Rate Limiting AI Agents: 3-Layer Gateway](https://www.truefoundry.com/blog/rate-limiting-ai-agents-preventing-llm-api-exhaustion)
- [Zop · LLM FinOps: Per-Feature Cost Attribution & Token Budgets](https://zop.dev/resources/blogs/llm-finops-per-feature-token-budget/)
- [FinOps Foundation · FinOps for AI Overview](https://www.finops.org/wg/finops-for-ai-overview/)
- [Spheron · Google TurboQuant: 6x KV Cache Compression](https://www.spheron.network/blog/google-turboquant-llm-compression-gpu-cloud/)
- [CallSphere · LLM Inference Optimization: Quantization & Speculative Decoding 2026](https://callsphere.ai/blog/llm-inference-optimization-quantization-speculative-decoding-2026)
