# CS146S《The Modern Software Developer》课程地图（Fall 2025）

- **课程**：Stanford CS146S "The Modern Software Developer"，主讲 Mihail Eric（前 Amazon Alexa，现 Monaco Head of AI）
- **课程站**：https://themodernsoftware.dev （2025 秋存档页 https://themodernsoftware.dev/fall2025 ）
- **课件镜像**：https://github.com/hqhq1025/ai-course-notes （slides 与中文讲义）；作业仓库 https://github.com/mihail911/modern-software-dev-assignments
- **笔记依据**：课程 slides + 第三方中文讲义整理，**非视频总结**——这门课官方不发布讲座录像（唯一的例外是 Lee Robinson 自行公开的 2026 秋客座讲座，见下方链接）
- **整理日期**：2026-10-24

## 这门课是什么

斯坦福 2025 年秋季首开的 3 学分课程，系统讲「如何与编程 Agent 一起做软件工程」：从 LLM 原理、亲手搭一个 Coding Agent，到上下文工程、安全、代码审查、DevOps 与 AI 产品的未来。每周除主讲外还有一线公司创始人的客座讲座。考核以期末项目为大头（2026 秋版权重：期末项目 50%、开源 PR 贡献 30%、周作业 15%、参与 5%）。

## Fall 2025 十周笔记索引

| 周 | 主题 | 笔记 |
|---|---|---|
| 1 | 课程导论与 2025 年软件开发现状；LLM 工作原理速览 | [week01](cs146s-week01-intro-and-llm-basics.md) |
| 2 | 从零构建 Coding Agent；Model Context Protocol (MCP) | [week02](cs146s-week02-build-a-coding-agent-and-mcp.md) |
| 3 | AI IDE 的演进与核心能力；Context Engineering | [week03](cs146s-week03-ai-ide-and-context-engineering.md) |
| 4 | 如何成为 Agent Manager；嘉宾：Claude Code 的设计哲学（Boris Cherny） | [week04](cs146s-week04-agent-manager-and-claude-code.md) |
| 5 | 构建突破性 AI 开发者产品；为什么终端会重新成为 AI 入口（Warp） | [week05](cs146s-week05-ai-dev-products-and-terminal.md) |
| 6 | 软件安全基础；三大漏洞检测技术（Semgrep） | [week06](cs146s-week06-security-and-vuln-detection.md) |
| 7 | 代码审查的价值与方法；把代码审查设计成一个系统（Graphite） | [week07](cs146s-week07-code-review-as-a-system.md) |
| 8 | Web 应用开发的演进；App Builder 的架构原理（Vercel） | [week08](cs146s-week08-web-apps-and-app-builders.md) |
| 9 | 生产环境运维的挑战；基础设施与 DevOps 原则（Resolve AI） | [week09](cs146s-week09-devops-in-production.md) |
| 10 | 课程回顾：AI 软件开发全景图；十年后的软件开发：五大趋势（无 slides 存档，仅据讲义） | [week10](cs146s-week10-retrospective-and-future.md) |

## 特别篇：2026 秋客座讲座（有视频）

- [常驻型 Agent 是如何工作的（Lee Robinson）](2026-10-24-leerob-always-on-agents.md)——2026 秋季班第 3 周客座讲座（2026-10-08），讲者自行公开了录像，是这门课目前唯一可看的完整视频。

## 2026 秋新版大纲（对照）

2026 秋季班（2026-09-22 至 12-03）把 2025 版约 85% 的内容换掉，新版十周为：①编程 Agent 内部原理 ②高级上下文工程与 MCP ③Agent 技能（Skills）与 CLI ④定制你的 Agent 与代码库（CLAUDE.md/AGENTS.md、hooks、子 Agent）⑤Agent 就绪的代码库 ⑥Agent 代码审查 ⑦安全（提示注入等）⑧后台 Agent ⑨打造 AI 原生团队 ⑩软件工厂与未来。官方承诺新版的讲座视频、幻灯片与代码将陆续免费公开；截至 2026-10-24 尚未成套放出。

## 建议的自学用法（结合在做的视频工厂项目）

- 先看第 2、3 周（搭 Agent、上下文工程）——视频工厂编排器与技能文件的直接理论依据
- 再看第 4 周（Agent Manager、Claude Code 哲学）与 Lee 的常驻 Agent 讲座——对应系统的调度、审批与常驻设计
- 第 6、7 周（安全、代码审查）在做 QA 子 Agent 与自动成长机制时回头看
- 其余周次作为产品与行业视野补完；每周的作业仓库练习可选择性地跟着做
