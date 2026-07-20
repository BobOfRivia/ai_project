# 第 8 章 AI 安全与合规

> **定位**：AI 应用工程师视角下的"安全 + 合规"工程化骨架——不是法律咨询、不是安全研究员视角，而是**把 Prompt 注入、Agent 工具风险、PII、内容审查、地区法规变成日常代码与流水线里的默认控件**。
>
> **2026 现实判断**：
> - 安全已经不是"加个 moderation 调用"——而是**注入防御 + Agent 沙箱 + Guardrails + Red Teaming + 数据驻留 + 监管对齐**的综合工程。
> - 监管钉子已落地：**EU AI Act 通用模型条款 2025-08-02 生效、Commission 执法权 2026-08-02 启动**，违规上限 €15M 或全球营收 3%。
> - 注入仍是 **OWASP LLM Top 10 第 1 名**，企业测试中 **60%+** 的尝试至少部分成功。
> - **MCP 工具中毒**成 2026 新一类高发漏洞，单次披露涉 ~200,000 暴露实例。

---

## 章节结构

| # | 小节 | 一句话定位 |
|---|---|---|
| 8.1 | [安全与合规基础与威胁模型](8.1-security-fundamentals.md) | OWASP LLM Top 10 v2 / 7 大攻击面 / "blast radius" 思维 |
| 8.2 | [Prompt 注入与越狱防御](8.2-prompt-injection-jailbreak.md) | Direct / Indirect、12 层防御、Instruction hierarchy |
| 8.3 | [Agent 与工具安全](8.3-agent-tool-security.md) | MCP 工具中毒、沙箱、最小权限、动作审计 |
| 8.4 | [内容审查与有害输出过滤](8.4-content-moderation.md) | OpenAI Moderation / Llama Guard 4 / MLCommons 危害分类 |
| 8.5 | [Guardrails 框架与产品化](8.5-guardrails-frameworks.md) | NeMo Guardrails / Guardrails AI / LLM Guard 组合落地 |
| 8.6 | [数据隐私与 PII 处理](8.6-pii-data-privacy.md) | Presidio / 三层检测 / 可逆 tokenization |
| 8.7 | [数据驻留 / Zero Retention / 合规部署](8.7-data-residency-deployment.md) | BAA / HIPAA / 区域隔离 / 自托管 vs ZDR API |
| 8.8 | [LLM Red Teaming 与安全测试](8.8-red-teaming.md) | Garak / Promptfoo / PyRIT，CI gate |
| 8.9 | [合规法规速览（EU AI Act / NIST / GDPR）](8.9-regulations-overview.md) | 工程师视角的"哪些条款会影响代码" |
| 8.10 | [安全治理与运营](8.10-security-governance-ops.md) | Incident response / SOC 集成 / 模型与提示审计 |

---

## 2026 全景信号

- **OWASP LLM Top 10 v2 已成事实标准**：把 Prompt Injection (LLM01) / Sensitive Info Disclosure / Supply Chain / Improper Output Handling / Excessive Agency 等十项当 baseline checklist。
- **注入是"必然失守"的——目标是缩小爆炸半径**：业内共识"单点控件都能绕过、组合不能"，工程心智从"防住"切到"出事能止血"。
- **Indirect Prompt Injection 是 2026 头号 Agent 风险**：网页 / 文档 / 邮件 / 工具描述里的隐藏指令；MCP server 的 metadata 已成攻击面。
- **EU AI Act 时间线钉死**：GPAI 条款 2025-08-02 生效、Commission 执法 2026-08-02、高风险系统义务 2026-08-02 起逐步生效。
- **NIST AI RMF GenAI Profile（AI 600-1）** 是美国市场的事实门槛——联邦采购 RFP 普遍引用；GOVERN / MAP / MEASURE / MANAGE 四象限是设计语言。
- **Llama Guard 4 (12B 多模态)** 成开源审查默认；按 MLCommons hazards 分类；与 OpenAI Moderation 拉平甚至更优。
- **Guardrails 双雄 + LLM Guard**：NeMo Guardrails（Colang DSL、<100ms、GPU 加速）+ Guardrails AI（Py/JS、freemium）+ LLM Guard（OSS 输入/输出 validator）；大公司组合用。
- **Red Teaming 工具链成熟**：Garak（NVIDIA、120+ probes、Apache 2.0）/ Promptfoo（2026-03 被 OpenAI 收购 $86M、仍 MIT、50+ 漏洞类型）/ PyRIT（微软、多轮深度）形成"breadth / depth / 探针"三角。
- **PII 处理已工程化**：Presidio + LiteLLM / Portkey 集成是默认；三层检测 = regex+checksum / NER (DeBERTa, piranha) / LLM-judge；可逆 tokenization 配 per-tenant CMK。
- **数据驻留与 ZDR 是企业准入门槛**：AWS Bedrock / Azure OpenAI 提供 BAA + 区域隔离；OpenAI/Anthropic ZDR 需企业合同；高敏感场景仍走自托管。

---

## 与其他章节的边界

| 章节 | 边界划分 |
|---|---|
| 第 3 章 Agent / MCP | 那里讲"怎么用工具 / 怎么编排"，这里讲"工具被投毒怎么办 / 沙箱怎么搭" |
| 第 4 章 工作流编排 | 那里讲流程，这里讲流程节点里嵌入的安全 / Guardrails 检查 |
| 第 6 章 评估与可观测性 | 那里讲质量评估与 trace，这里讲**安全测试**（Red Teaming）和**安全事件**的可观测 |
| 第 7 章 工程化 | 那里讲限流容灾成本，这里讲**输入输出层**的安全控件（注入 / PII / 审查） |
| 第 1 章 Prompt 基础 | 那里讲"怎么写 Prompt"，这里讲"Prompt 怎么被攻击 + 怎么硬化" |

---

## 学习路径

```
8.1 (基础 / 威胁模型)
    ↓
8.2 (Prompt 注入) ←──── 8.3 (Agent 安全) ←──── 8.4 (内容审查)
                              ↓
                         8.5 (Guardrails 产品化)
                              ↓
                         8.6 (PII) ←──── 8.7 (数据驻留 / 合规部署)
                              ↓
                         8.8 (Red Teaming, CI gate)
                              ↓
                         8.9 (法规) ←──── 8.10 (治理与运营)
```

- **最小可上线集**：8.1 + 8.2 + 8.4 + 8.6 + 8.8
- **Agent 应用必加**：8.3 + 8.5
- **企业 / 监管行业必做**：8.7 + 8.9 + 8.10

---

## 主要参考（章级）

### OWASP / 注入
- [OWASP Gen AI Security · LLM01:2025 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/)
- [Repello AI · OWASP LLM Top 10 (2026)](https://repello.ai/blog/owasp-llm-top-10-2026)
- [DigitalApplied · Prompt Injection Defense: 12-Layer Framework 2026](https://www.digitalapplied.com/blog/prompt-injection-defense-12-layer-framework-2026)
- [Zylos Research · Indirect Prompt Injection: 2026 State of the Art](https://zylos.ai/research/2026-04-12-indirect-prompt-injection-defenses-agents-untrusted-content/)
- [Tek Ninjas · Prompt Injection 2026 Defense Playbook](https://tekninjas.com/blogs/cybersecurity-ai-agents-prompt-injection-2026/)

### Agent / MCP 安全
- [ITECS · MCP Tool Poisoning: Enterprise AI Agent Security 2026](https://itecsonline.com/post/mcp-tool-poisoning-enterprise-ai-agent-security-2026)
- [NVIDIA · Practical Security Guidance for Sandboxing Agentic Workflows](https://developer.nvidia.com/blog/practical-security-guidance-for-sandboxing-agentic-workflows-and-managing-execution-risk/)
- [Adversa AI · Top Agentic AI Security Resources — June 2026](https://adversa.ai/blog/top-agentic-ai-security-resources-june-2026/)

### Guardrails / 内容审查
- [Galileo · 5 Best AI Guardrails Platforms 2026](https://galileo.ai/blog/best-ai-guardrails-platforms)
- [Maxim · Best AI Guardrails Platforms 2026](https://www.getmaxim.ai/articles/best-ai-guardrails-platforms-in-2026/)
- [AI Safety Directory · NeMo Guardrails vs Guardrails AI (2026)](https://aisecurityandsafety.org/en/compare/nemo-guardrails-vs-guardrails-ai/)
- [Meta · Llama Guard Research Paper](https://ai.meta.com/research/publications/llama-guard-llm-based-input-output-safeguard-for-human-ai-conversations/)

### PII / 隐私
- [AppScale · PII Redaction Pipeline for LLM Workloads 2026](https://appscale.blog/en/blog/pii-redaction-pipeline-llm-presidio-ner-reversible-tokenisation-2026)
- [Microsoft · Presidio SDK](https://microsoft.github.io/presidio/)
- [LiteLLM · Presidio PII Masking Tutorial](https://docs.litellm.ai/docs/tutorials/presidio_pii_masking)

### 数据驻留 / 合规
- [TrueFoundry · LLM Deployment in Regulated Industries: HIPAA / SOC2 / GDPR 2026](https://www.truefoundry.com/blog/llm-deployment-in-regulated-industries-hipaa-soc2-and-gdpr-playbook-for-2026)
- [Abu Bakar Siddik · Zero Data Retention for LLM Providers](https://abubakarsiddik.site/blog/zero-data-retention-llm-providers)

### Red Teaming
- [AppSecSanta · Garak vs Promptfoo (2026)](https://appsecsanta.com/garak-vs-promptfoo)
- [BeyondScale · PyRIT vs Garak vs Promptfoo (2026)](https://beyondscale.tech/blog/ai-red-teaming-tools-comparison-2026)

### 法规
- [European Commission · AI Act Regulatory Framework](https://digital-strategy.ec.europa.eu/en/policies/regulatory-framework-ai)
- [European Commission · GPAI Provider Guidelines](https://digital-strategy.ec.europa.eu/en/policies/guidelines-gpai-providers)
- [NIST · AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST · AI 600-1 Generative AI Profile (PDF)](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf)

<!-- 每节细节请见对应文件 -->
