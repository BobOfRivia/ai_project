# Anthropic Engineering

> 入口：<https://www.anthropic.com/engineering>

Anthropic 官方**工程实践博客**（区别于研究 papers 和产品公告），主打"我们自己怎么构建生产级 AI 系统"的一手经验。是 **Agent / Context Engineering / RAG** 领域被引用最多的权威源头之一。

**覆盖主题**：Agent 构建、Context Engineering、工具与 MCP、RAG/检索、Claude Code 与 Agent SDK、长时运行 agent。

---

## 精选必读

### 🔴 奠基必读

- 🔴 [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents) — 厘清 **agent vs workflow**、5 种编排模式。几乎所有讲 agent 的文章都引它。→ **第 3、4 章**
- 🔴 [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Context Engineering 的权威定义（系统指令/工具/MCP/历史/外部数据如何取舍）。→ **第 3 章**
- 🔴 [How We Built Our Multi-Agent Research System](https://www.anthropic.com/engineering/multi-agent-research-system) — 多 agent 并行研究系统的真实工程复盘，含大量踩坑。→ **第 3、4 章**

### 🟡 强烈推荐

- 🟡 [Contextual Retrieval](https://www.anthropic.com/engineering/contextual-retrieval) — 检索失败率 -49%/-67% 的方法源头（已吸收进本库 2.2）。→ **第 2 章**
- 🟡 [Writing Effective Tools for AI Agents](https://www.anthropic.com/engineering/writing-tools-for-agents) — 工具描述的微小改动带来大幅提升（SWE-bench SOTA）。→ **第 3 章**
- 🟡 [Code Execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp) — 用代码执行让 agent 更高效地用工具、按需加载。→ **第 3 章 MCP**
- 🟡 [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) — 跨越数小时/数天、多个上下文窗口如何保持进度。→ **第 4 章持久化**

### 🟢 工程实操向

- 🟢 [Building Agents with the Claude Agent SDK](https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk) — SDK 落地（compact 自动摘要、环境初始化）。
- 🟢 [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices) — 你正在用的工具本身的最佳实践。
- 🟢 [Equipping Agents with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) — Skills 作为"专业知识包"动态加载。

---

## 一个值得记住的观点

Anthropic 建议：**先用 agentic search（让 agent 自己翻文件夹搜），只有需要更快时才加 semantic search（向量检索）**——语义检索虽快但准确率低、维护难。这与"检索是瓶颈"形成有趣对照：**agent 场景下纯向量 RAG 未必是第一选择**。
