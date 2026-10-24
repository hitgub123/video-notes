# CS146S 第 4 周：Agent Manager 与 Claude Code 的设计哲学

- **课程**：Stanford CS146S《The Modern Software Developer》，Fall 2025，主讲 Mihail Eric
- **周次与主题**：第 4 周——周一正课《如何成为 Agent Manager》（How to be an Agent Manager）；周五嘉宾讲座：Claude Code 的设计哲学（讲者 Boris Cherny，Anthropic，Claude Code 创造者，2025-10-17）
- **来源**：课程存档页 https://themodernsoftware.dev/fall2025 ；课件镜像 https://github.com/hqhq1025/ai-course-notes
- **依据说明**：本笔记基于课程官方 slides 整理（辅以第三方中文讲义作结构参照），非视频总结
- **笔记日期**：2026-10-24

## 一、如何成为 Agent Manager（周一正课）

### 软件开发团队的演进：从管自己到管一群 Agent

这一讲先给结论：开发者的角色正在经历第四次转型。课件给出的演化路径是——单个开发者管理自己的产出 → 团队负责人管理多名开发者的产出 → 负责人管理多名开发者的产出（由 AI 系统辅助）→ **单个开发者管理多个 AI Agent 的工作量**。

课件的时间线把这件事放进软件团队的简史里：1940 年代是单人包办整个项目的时代；1960 年代因 NASA、国防部等大型项目的需求出现第一批软件团队；1970 年代起软件团队成为主流、专业分工形成；1990 年代团队协作进一步成熟；2023 年起团队开始由 AI 编程系统辅助；到 2025 年，形态变成"开发者管理一群各式各样的 Agent"，并继续向 2030 年演进。换句话说，"Agent Manager"不是一种新工具用法，而是一种新的组织形态：你的下属不再是人，而是 Agent。

### 软件任务的分解：人还剩下哪几步

课件把一个典型软件任务拆成七步，并标注每步由谁主导（绿色 = 人类，蓝色 = Agent）：

1. 提出高层需求 —— 人类
2. 把需求转成设计文档 —— 人类 / Agent 共同
3. 按设计文档实现方案 —— Agent
4. 添加测试 —— Agent
5. 确保 CI（持续集成）通过 —— Agent
6. 代码审查 —— Agent
7. 更新文档 —— Agent

分工的含义很直接：在 Agent 管理模式下，人类工程师的最高价值集中在第 1–2 步——需求理解和架构设计。这两步需要业务上下文、技术判断力和"品味"，恰好是当前 AI 最薄弱的环节；后面的执行、测试、审查、文档都可以成规模地委派出去。

### 指导 Agent 的四种技术

要当好 Agent Manager，课件给出四件可操作的工具：

**1. Agent 行为文件（Agent behavior files）**。如 CLAUDE.md、.cursorrules、AGENTS.md，在项目层面给 Agent 写明行为规范——相当于给新员工的入职手册，Agent 每次开工先读它。

**2. Hooks**。在预定义事件上触发的**确定性脚本**——注意它不是让模型自由发挥，而是事件发生时必定执行的脚本。课件列出的事件类型包括：PreToolUse（工具调用前）、PostToolUse（工具调用后）、UserPromptSubmit（用户提交提示词时）、PreCompact（上下文压缩前）等。Hooks 适合做硬性拦截和自动检查，比如提交前强制跑格式化。

**3. Commands**。把高频使用的提示词存成文件，让 Agent 按需执行——相当于给 Agent 配一套标准作业流程。课件给的用例：运行测试、审查代码、生成 git commit 并推送。

**4. Subagents（子 Agent）**。运行时委派机制：主 Agent 把活分派给子 Agent 干。课件列出两个目的：一是为不同类型的工作塑造不同的"开发者人格"（如前端人格、后端人格）；二是为不同的工作流干净地分离上下文，互不污染。子 Agent 拥有定制化的系统提示、工具集和独立的上下文窗口。课件特别点出：这代表了一种走向——**Agent 管理其他 Agent**。讲义补充的社区用例：awesome-claude-agents 仓库的 CLAUDE.md、SuperClaude Framework，都是围绕子 Agent 分工搭建的实践。

### 最佳实践：先有防护栏，再谈放手

- **要有过硬的后盾（backstops）**：代码库里的测试、CI/CD 的最佳实践，是敢让 Agent 自主干活的前提
- **每个 Agent 都要可审计**：给 Agent 产生的每一个 diff 打上标记，出了问题能追溯到哪次委派
- **不同类任务用不同模型**：按任务难度匹配模型，而不是一律用最强的
- **任务越复杂，前期越要多带**：复杂任务需要在前期多花引导，不能和简单任务一样完全异步甩手
- **定期 checkpoint**：频繁 commit，Agent 偏离方向时能回到上一个存档点

### 尚未解决的问题

课件最后留下两个开放问题：一是如何自动化每个任务最初 10%–20% 的调研阶段（目前这段最依赖人）；二是如何维护一个待处理任务的队列——对一次性的小修改较容易，但对持续性工作流仍是挑战。课件另有一页"工作流走查"（workflow walkthrough），提取文本中只有标题、无具体内容，此处不展开。

### 讲义提炼：管理多 Agent 时，瓶颈在任务切分

中文讲义在这一讲之外补了一层组织层面的提炼（属整理者归纳，非课件原文）：当开发者开始同时管理多个 Agent，项目管理方式本身也要变——任务必须可切分（需求模糊就无法安全地并行委派）、边界必须清晰（每个 Agent 负责哪些文件、哪些验证步骤、什么提交粒度，事先说清）、审查必须标准化（越是并行，越需要一致的 diff 审查和回滚机制）。讲义的判断是：Agent Manager 的主要失败模式不是模型不够聪明，而是任务切分不合理——先是项目管理能力，然后才是提示词技巧。

## 二、嘉宾讲座：Claude Code 的设计哲学（Boris Cherny）

### 编程正处于拐点

Boris Cherny 开场先给三句话总纲：① 编程正在改变；② 用 Claude Code 选好你的路径；③ **按六个月后的情况去思考**（think six months out）。

他用两条指数曲线论证"拐点"：编程语言的生产力在对数尺度上指数增长——从 Fortran、COBOL、BASIC、Pascal、C、C++、Java、Python 一路到今天，AI 是最新一波增长的驱动力；IDE 的生产力走的是同一条曲线——课件逐一点名了 IBM 029 打卡机（1964）、ed（1969）、Smalltalk-80（1980）、Visual Basic（1991）、Eclipse（2001）、Copilot（2021）、Devin（2024），直到 Cursor 和 Claude Code。

同步演进的还有**验证（verification）**：手动调试 → 静态类型（Algol）→ 形式化验证 → 抽象解释 → 自动化测试 → 持续集成 → 基于性质的测试（QuickCheck）→ 依赖类型 → 端到端测试 → 混沌测试（Chaos Monkey）→ AI 驱动的漏洞测试、单元测试生成（TestGen）、模糊测试（Sapienz）→ 自我对弈（self play）……工具在变强，验证手段也必须跟着变强，这是本讲的一条暗线。

### Claude Code 的路线：无处不在

课件把 Claude Code 的方法概括为 "works everywhere"，三条设计原则：

1. **Terminal-native**：原生于终端，而不是某个 IDE 的插件
2. **Low-level model access**：提供对底层模型的低层直接访问，不把模型包死
3. **Infinitely hackable**：无限可魔改、可扩展

"无处不在"还体现在它覆盖**整个软件开发生命周期（SDLC）**：Discover（探索代码库与历史、搜索文档）→ Design（规划项目、写技术规格、定义架构）→ Build（实现代码、写并运行测试、创建 commit 和 PR）→ Deploy（自动化 CI/CD、配置环境、管理部署）→ Support & Scale（调试错误、大规模重构、监控用量与性能）。它的做法是熟练使用团队已有的全部 CLI 工具（git、docker、bq 等），让开发者专注于"解法"，而不是"语法"。

### 一个 Claude Code，多种形态

同一个 Claude Code 以多张面孔出现：**终端**（命令行直接用）、**IDE**（集成进编辑器）、**Web & iOS**（claude.ai/code）、**GitHub App**（用 `/install-github-app` 安装）、**SDK**（程序化调用）。SDK 的例子很能说明它的定位：`claude -p "what did i do this week?" --allowedTools Bash(git log:*) --output-format stream-json` 可以把 Claude Code 当成管道里的一个环节——比如把 GCP 日志命令的输出经管道喂给它做"关联错误与提交"，再用 jq 提取 `.result`；后端可接 Anthropic、Bedrock 或 Vertex 的 API。安装方式也保持极简：`npm install -g @anthropic-ai/claude-code`。

### 四类典型用法

课件列出的使用场景：① 代码库问答与调研；② 写代码（一把梭 1-shot、结对 sidekick、原型 prototype 三种姿势）；③ 集成工具与 MCP；④ 驱动自动化。展开来说：

**代码库问答**。直接拿问题问它，让它自己翻代码和历史："如何新建一个 `@app/services/ValidationTemplateFactory`？"、"为什么 `recoverFromException` 有这么多参数？查 git 历史回答"、"为什么我们在 `@src/login.ts` 里加 if/else 修了 issue #18363？"、"看一下 PR #9383，仔细核对哪些 app 版本受到了影响"、"我上周交付了什么？"

**教它用你的工具**。先直接让它用团队的 CLI（如 "use the barley cli to check for error logs"）；工具沉淀下来后，用 `claude mcp add barley_server -- node myserver` 把自研服务注册成 MCP server，再让它走 MCP 查日志——工具集成是渐进的。

**按任务适配工作流**。同一工具，不同任务用不同的流程节奏，课件给了三种模式：

- 探
...[truncated 1269 chars]