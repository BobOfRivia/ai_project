# 第 5 章 视觉 / 语音应用（多模态）

> **本章定位**：把"图像 / 语音 / 视频"从模态接入到生产应用的**工程视角**。区别于算法工程师视角的"VLM 原理 / Diffusion 训练 / Vocoder 细节"，本章聚焦 **API 选型、架构权衡、延迟与成本控制、流式 UI、降级与容灾**。
>
> **学习目标**：能独立判断"这个业务该用 VLM 还是图像生成、Pipeline 还是 Realtime、视频抽帧还是原生长视频模型、哪个云厂 / 哪个聚合器"，并把所选方案落到一个延迟、成本、可观测都达标的生产系统。

---

## 为什么单列一章

2026 的多模态工程图景已经和 2024 完全不一样：

- **VLM 已成"通用入口"**：GPT-5 / Claude / Gemini 2.5 全部 native 多模态；MMMU-Pro 上 GPT-4o 已达 59.9%；OCR / 文档智能 / Computer Use 都从专门模型迁移到通用 VLM
- **图像生成进入三层架构**：Premium（GPT Image 2 / Midjourney V8 / Imagen 4）+ Open-weight（Flux 2 / SD 3.5 / Ideogram 3 / Recraft V3）+ Aggregator（FAL / Replicate / Together / Fireworks）—— 应用工程师默认从聚合器入手
- **图像编辑独立成范式**：Flux Kontext（Pro/Dev/Max）和 GPT Image 2 让"指令式编辑"代替"重新生成"，多轮迭代成主流交互
- **语音架构两条路线公开决战**：传统 **Pipeline（STT → LLM → TTS）** vs **Realtime / S2S**（OpenAI gpt-realtime-2 / Gemini Live / Azure Voice Live）；生产里 pipeline 仍占大头，realtime 在高端对话场景突围
- **STT/TTS 都已 sub-100ms**：Cartesia Sonic 4 ≈ 40ms TTFA、Deepgram Aura-2 ≈ 90ms、Deepgram Nova-3 + Flux 是语音 agent 的事实默认
- **视频理解从"抽帧 + VLM"到"原生长视频模型"**：Gemini 长上下文、LLaVA-Video、Twelve Labs（Marengo + Pegasus）三类路线并存；shot-aware / adaptive / generative frame sampling 是工程关键
- **视频生成 API 大洗牌**：**Sora 2 已宣布弃用**（2026-04-26 宣布、2026-09-24 关闭），生产管线必须迁移到 Veo 3.1 / Kling 3.0 Omni / Seedance 2.0 / Runway Gen-4.5；fal.ai 成为多模型聚合事实入口
- **延迟 + 成本是新常态议题**：多模态 token 单价比文本高 5 ～ 20 倍，视频更甚；流式 UI / 缓存 / 降级链是必修
- **2026 共识架构**：和 ch4 一致 —— **确定性骨架 + 定点 multimodal 调用**；不要让 VLM 当总指挥

---

## 章节结构（10 节）

| # | 小节 | 核心问题 |
|---|---|---|
| 5.1 | 多模态基础与决策框架 | 这个业务该用 VLM、生成、还是组合？2026 模态格局怎么看？ |
| 5.2 | VLM 调用与图像理解 | API 怎么调？OCR / 文档智能 / Visual QA / Computer Use 谁强？ |
| 5.3 | 图像生成 API | Premium / Open-weight / Aggregator 三层格局怎么选？ |
| 5.4 | 图像编辑与可控生成 | Flux Kontext / GPT Image 2 edit / ControlNet 各自适用什么？ |
| 5.5 | 语音识别（ASR/STT） | Whisper / Deepgram Nova-3 / Parakeet / Scribe v2 谁更适合？ |
| 5.6 | 语音合成（TTS） | Cartesia / ElevenLabs / Hume / Aura-2 在延迟 vs 表现力如何取舍？ |
| 5.7 | Realtime 语音对话（S2S vs Pipeline） | gpt-realtime-2 / Gemini Live 上不上？两种架构怎么选？ |
| 5.8 | 视频理解 | 抽帧 + VLM 还是原生长视频模型？Twelve Labs 什么时候上？ |
| 5.9 | 视频生成 API | Sora 2 弃用之后谁补位？Veo / Kling / Seedance / Runway 怎么选？ |
| 5.10 | 多模态应用工程 | 延迟预算、成本、流式 UI、降级、可观测 |

---

## 与其他章节的边界

| 本章覆盖 | 不在本章（去别处） |
|---|---|
| VLM **API 调用与图像理解应用**（5.2） | 多模态 RAG / 文档检索系统 → **ch2** |
| 视频抽帧 / 长视频 VLM **应用工程**（5.8） | 视频内容 RAG / 语义检索系统 → **ch2** |
| **图像 / 语音 / 视频生成 API**（5.3 / 5.6 / 5.9） | VLM / Diffusion / Vocoder 模型原理 → **算法工程师范畴，不在本知识库** |
| Computer Use 的**视觉理解**部分（5.2） | Computer Use Agent 主体（决策循环、工具、安全）→ **3.10** |
| Realtime 语音对话**架构与 API 选型**（5.7） | 语音 agent 的 memory / planning / 工具 → **ch3** |
| 多模态调用的**延迟 / 成本 / 降级**（5.10） | 通用工程（路由 / cache / 部署） → **ch7** |
| 多模态**输出的安全审核**（点到为止） | 内容审查 / Moderation / 合规 → **ch8** |

---

## 学习路径建议

- **必经**：5.1 → 5.2 是多模态地基（视觉理解是入口最广的能力）
- **生成线**：5.3 → 5.4（图像）→ 5.9（视频）按业务方向选学
- **语音线**：5.5 + 5.6 →（如果做对话）5.7（Realtime 架构）
- **理解线**：5.2 → 5.8（视频理解）；视频管线工程量大、单独评估
- **上线**：5.10 在任何方向落地前都要预算延迟和成本

---

## 主要参考（章节级，子节内还有更具体的来源）

- [Dextra Labs · Top 10 Vision Language Models 2026](https://dextralabs.com/blog/top-10-vision-language-models/)
- [BentoML · Multimodal AI: Open-Source VLM Guide 2026](https://www.bentoml.com/blog/multimodal-ai-a-guide-to-open-source-vision-language-models)
- [Effloow · AI Image Generation Tools Compared 2026](https://effloow.com/articles/ai-image-generation-tools-compared-2026)
- [NovaKit · Image Generation APIs Compared 2026](https://www.novakit.ai/blog/ai-image-generation-apis-2026-compared)
- [Digital Applied · Image Generation API Pricing 2026](https://www.digitalapplied.com/blog/ai-image-generation-api-pricing-comparison-2026)
- [OpenAI · Introducing gpt-realtime and Realtime API updates](https://openai.com/index/introducing-gpt-realtime/)
- [OpenAI · Advancing voice intelligence with new models](https://openai.com/index/advancing-voice-intelligence-with-new-models-in-the-api/)
- [AssemblyAI · Best Speech-to-Speech Voice Agent API 2026](https://www.assemblyai.com/blog/best-speech-to-speech-voice-agent-api)
- [AssemblyAI · The voice AI stack for building agents 2026](https://www.assemblyai.com/blog/the-voice-ai-stack-for-building-agents)
- [Famulor · Realtime vs Pipeline Voice Agent Architecture 2026](https://www.famulor.io/blog/realtime-vs-pipeline-voice-agent-architecture-guide-2026)
- [Future AGI · Best TTS Providers 2026](https://futureagi.com/blog/best-text-to-speech-providers-2026/)
- [Future AGI · Best Speech-to-Text APIs 2026](https://futureagi.com/blog/speech-to-text-apis-in-2026-benchmarks-pricing-developer-s-decision-guide/)
- [Twelve Labs · Marengo & Pegasus product overview](https://www.twelvelabs.io/product/models-overview)
- [AWS · TwelveLabs on Amazon Bedrock](https://aws.amazon.com/blogs/aws/twelvelabs-video-understanding-models-are-now-available-in-amazon-bedrock/)
- [Get AI Perks · Best AI Video Generators 2026](https://www.getaiperks.com/en/blogs/44-best-ai-video-generators-2026)
- [Crazyrouter · AI Video Generation Comparison 2026](https://crazyrouter.com/en/blog/ai-video-generation-comparison-2026)
- [Pixflow · Best AI Video Generator 2026](https://pixflow.net/blog/best-ai-video-generator/)
