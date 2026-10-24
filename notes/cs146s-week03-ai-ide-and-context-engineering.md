# CS146S 第 3 周：AI IDE 与上下文工程

- **课程**：Stanford CS146S《The Modern Software Developer》，Fall 2025，主讲 Mihail Eric
- **周次与主题**：第 3 周——AI IDE 的演进与核心能力；Context Engineering（上下文工程）
- **讲次构成**：周一主课（Mihail Eric：《The AI IDE: Fundamentals to Power User》）+ 周五嘉宾场（Cognition 研究负责人 Silas Alberti：《IDE ❤ Agents — An opinionated guide to AI coding in 2025》，2025-10-10）
- **来源**：课程存档页 https://themodernsoftware.dev/fall2025 ；课件镜像 https://github.com/hqhq1025/ai-course-notes
- **依据说明**：基于课程官方 slides（周一主课 deck + 周五 Cognition 嘉宾 deck）整理，辅以第三方中文讲义补足叙述；**非视频总结**

## 总结

### 这周讲什么

第 3 周分两条线。周一主课讲 AI IDE 本身：它为什么是 AI 落地的自然形态、底层怎么工作，以及使用者一侧的核心功夫——上下文工程：复杂任务要写规格书、代码库要整理到"人和 Agent 都能懂"、用配置文件给 Agent 导航。周五嘉宾场由 Cognition（Devin 的开发商）的 Silas Alberti 讲工具格局：AI 编程工具的三个时代、同步与异步的区分，以及 2025 年把规划、编码、测试分配给不同工具的工作流。

### 为什么是 IDE：钟摆效应与简史

IDE（Integrated Development Environment，集成开发环境）是集编辑器、编译器、调试器于一体的全合一工作空间。绝大多数开发工作本来就发生在 IDE 里，所以它是 AI 增强的自然形态（natural form factor）。课程还点出一个观察：IDE 的演进史里，功能整合与开发者定制之间始终存在"钟摆"（see-saw）——一会儿向大而全摆，一会儿向轻量可定制摆，AI IDE 的出现又把钟摆推向功能整合一侧。

简史时间线（slides 给出）：

- **1983**：Turbo Pascal 发布——第一个真正的 IDE
- **1997**：Microsoft Visual Studio 发布——为 C++/Visual Basic 提供高级调试能力
- **2001**：IntelliJ IDEA 发布——上下文代码导航、重构、代码补全
- **2015**：Microsoft VSCode 发布——轻量编辑器 + 高度可扩展的生态
- **2023**：Cursor 发布——首批被广泛使用的 AI 原生 IDE 之一

### AI IDE 的使用模式：从日常模式到 AI 原生

slides 把使用模式分两层。

**日常基础模式（bread-and-butter）**：行内补全（Inline）、函数级生成（Function）、单文件编辑（Single-file）、跨文件修改（Multi-file）。这是 AI IDE 的基本盘，本质是"在编辑器里更快地写和改代码"。

**真正的 AI 原生模式**：后台 Agent（Background agents，自主在后台执行任务）、MCP（连接外部工具）、Memories（学习并记住用户偏好与项目上下文）、Bugbot（做 PR 审查）。这一层不再是"补全"，而是把任务整段交给 Agent。

### 底层原理：Tab 补全与 Chat 各是怎么工作的

**Tab-complete（代码补全）**走的是 infilling（填空式生成）：

1. 取当前代码周围的一小段上下文窗口，加密后发到服务器
2. 服务器运行一个 infilling LLM，在光标处"填空"
3. 建议返回编辑器并显示出来

**Chat（对话模式）**背后是一套语义索引（semantic index）：

1. IDE 把代码切成块（chunks），在服务器端存为 embeddings，文件名做混淆处理（obfuscated filename + code）
2. 用户提问时，检索最相关的代码块，作为上下文喂给 LLM
3. IDE 定期重新索引代码块、同步 embeddings
4. 代码块的差异用 **Merkle tree**（哈希树）计算——只有变了的块才需要重算 embedding，实现高效增量更新

理解这两条链路的意义在于：补全的延迟和质量取决于小窗口 infilling，问答的质量取决于索引与检索——上下文给什么，模型就只能用什么。

### 上下文工程之一：复杂任务要写"规格书"

主课的实践部分先给一条分界线：简单变更不需要在提示词上太费心思；但任务一复杂，你就得变成产品经理（product manager），写一份精心打磨的规格文档（specs doc）。slides 给出的规格书结构共八项：

- **Goal**：这次变更的目的是什么
- **Definitions**：LLM 需要先知道哪些前置定义
- **Plan**：高层次的实现拆解
- **Source files being changed**：涉及哪些源文件、代码库的哪些部分相关、为什么相关
- **Test cases**：测试怎么做
- **Edge cases**：有哪些特殊情况要处理
- **Out-of-scope**：什么**不应该**被改动
- **Extensions**：将来还会有哪些相关变更——让 LLM 做有前瞻性的设计，不走捷径（future-proof，不为眼前省事埋坑）

### 上下文工程之二：把代码库整理到"人和 Agent 都能懂"

第二条最佳实践是优化代码库本身：目标是让一个人类和一个 Agent 都能看懂正在发生什么。slides 的原话是，LLM 的很多困惑来自于"拿一个混乱的仓库当上下文去硬做任务"——讲义补充了同一层意思：如果新入职的人类工程师都会对你的代码库犯晕，LLM 一样会。

为 LLM 提供最优上下文，要把下面这些写成完备文档：仓库导览（repo orientation）、文件结构（file structure）、搭建与环境（setup and environment）、最佳实践（best practices）、代码风格（code style）、访问模式（access patterns）、API 与契约（APIs and contracts）。另外一条明确提示：**强烈推荐 monorepo 设计**——单个大仓库让 Agent 更容易把握项目全貌。

再往前一步，用 **Agent 配置文件**帮 LLM 在代码库里导航：

- **CLAUDE.md**：Claude 专用文件，对话开始时会被自动拉进上下文。适合放：常用 bash 命令、核心文件与工具函数、代码风格指南、测试指令
- **.cursorrules**：Cursor 的规则文件
- **AGENTS.md**：开放格式（open format）的 Agent 指导文件
- **llms.txt**：给在网上抓取内容的 LLM 提供导航指引

但 slides 专门加了一条注意事项：**Agent 不会总是遵守这些描述和指令，它们只是指导（guidance）**。讲义据此提醒：不要把关键的安全约束或不变量寄托在配置文件上。

### 嘉宾场：AI 编程工具的三个时代

Silas Alberti 把 AI 编程工具分成三个时代，并给出各自的效率量级（以下数字均为嘉宾在 slides 中给出的主张）：

1. **代码补全时代**：GitHub Copilot 加速写代码，本地、同步，效率提升约 **10%**
2. **AI IDE 时代**：AI IDE 完成单人任务（single-player task completion），效率提升约 **20%**
3. **AI 软件工程师时代**：云端 Agent 规模化并行工作流，效率提升 **6–12 倍**

从百分比到倍数，关键变量是**并行**：前两个时代还是"一个人 + 一个工具"，第三个时代是一个人同时调度多个云端 Agent。

### 同步 vs 异步，以及要躲开的"半异步"区间

嘉宾场的核心框架是同步（sync）与异步（async）的对照：

- **同步**：单线程、人在环中（human-in-the-loop），注意力锁在一个任务上；Agent 一次工作 **20 秒到 1.5 分钟**
- **异步**：多线程，人类把任务委派出去、在多个任务之间切换注意力；Agent 一次工作 **10 分钟到数小时**

把"本地/云端"和"同步/异步"交叉起来看：本地 AI IDE 是本地 + 同步、1 对 1、知识局限于个人（isolated knowledge）；云端 AI Agent 是云端 + 异步、1 对多（"无限个 Devin"供并行调度）、承载组织级知识（organizational knowledge），可以跑在 VPC 里。结论：**云端 + 异步才能实现 10 倍并行**。

但他强调，用异步 Agent 是一项"难、但可学"的技能：大多数人仍在用同步工具，原因在于**管理与委派本身就是一项难掌握的技能**——无论对象是人还是 Agent；它还要求你能在多个任务之间快速切换、迅速进入新上下文。

最值得记住的是中间地带警告——**Semi-Async（半异步）**：大约 30 秒到 3 分钟这个区间卡在心流屏障（flow barrier）上，**要避开**：它太慢，慢到你没法留在心流里等它；又太短，短到不值得切换去做别的任务。两条出路：要么把工具做得更快以保住心流，要么干脆多花时间换取更高的智能水平（trade time for higher intelligence）。

### 2025 年的工作流：规划同步、编码异步、测试同步

嘉宾给出的 2025 年最佳工作流，按阶段分配工具类型：

- **规划（Planning）→ 同步**：在 Windsurf 与 Devin 之间做规划，手段包括 DeepWiki（自动生成的代码库文档/wiki）、Ask Devin（直接向 Devin 提问了解代码库）、Codemaps（代码地图），以及在 Windsurf 里用 DeepWiki
- **编码（Coding）→ 异步**：把编码整段委派给 Agent（Devin）
- **测试（Testing）→ 同步**：常见流程是先把任务委派给 Devin（异步），然后在 Windsurf 里测试并打磨它交回的变更（同步）

对未来的判断是：如果异步 Agent 能自主完成测试，杠杆还会再放大——而这正在慢慢变成现实。他对全景的概括是：规划、编码、测试三个阶段都会逐步异步化、自主化；**人类工程师的角色变成 Agent 管理者**——用同步工具啃最难的问题，用异步工具拿 10 倍杠杆。未来最有价值的技能也随之明确：**委派与多线程、代码阅读、规划 / 界定范围 / 架构设计**。

### 讲义的延伸整理：团队评估三问

中文讲义在嘉宾场之外补了一个组织层面的评估框架——判断一套 AI IDE 是否值得在团队推广，可以问三件事：它是否真正减少了上下文切换（还是让人在大量外部文档和提示词模板间来回跳）？它是否让代码库更透明（放大仓库里已有的结构化信息，而不是掩盖混乱）？它是否有稳定的回退机制（Agent 改错时，能否靠测试、审查和清晰的 diff 快速恢复）？讲义的落点是：能落地的 AI IDE 必须同时优化"人"和"仓库"，上下文工程最终会倒逼团队重构自己的知识管理方式。

## 核心概念速查

| 概念 | 一句话 |
|---|---|
| AI IDE | 把 AI 能力深度嵌入编辑器/编译器/调试器一体工作空间的开发环境；分日常模式（补全、单/多文件编辑）与 AI 原生模式（后台 Agent、MCP、记忆、PR 审查） |
| 钟摆效应（see-saw） | IDE 演进中功能整合与开发者定制之间的反复摆动，AI 把钟摆再次推向功能整合 |
| Infilling | Tab 补全的底层机制：取光标周围小窗口上下文，加密发往服务器，由 infilling LLM"填空"后返回建议 |
| 语义索引（semantic index） | Chat 的底层机制：代码块以 embedding 形式存于服务器，提问时检索最相关的块作上下文；文件名做混淆处理 |
| Merkle tree | 哈希树，用于计算代码块差异，只对变更的块重算 embedding，实现索引的高效增量更新 |
| 规格书（spec doc） | 复杂任务的 PM 式提示文档：Goal、Definitions、Plan、源文件、测试用例、Edge cases、Out-of-scope、Extensions 八项 |
| Agent 配置文件 | 给 Agent 导航代码库的说明文件：CLAUDE.md（自动载入）、.cursorrules、AGENTS.md（开放格式）、llms.txt（面向网络抓取的 LLM）；只是指导，Agent 不保证遵守 |
| Monorepo | 把代码放在单一大仓库的设计；因便于 Agent 把握项目全貌而被课程强烈推荐 |
| 同步工具（sync） | 单线程、人在环中，Agent 工作 20 秒–1.5 分钟，代表形态是本地 AI IDE |
| 异步工具（async） | 人类委派后切换注意力，Agent 工作 10 分钟–数小时，代表形态是云端 Agent（如 Devin）；云端 + 异步 = 10 倍并行 |
| 半异步陷阱（Semi-Async） | 30 秒–3 分钟区间：太慢保不住心流、太短不值得多任务切换，要避开；出路是更快，或用时间换智能 |
| 委派（delegation） | 把任务整段交给异步 Agent 并在其间切换处理其他任务的能力；嘉宾认为这是难学但回报最高的技能，也是工程师转向"Agent 管理者"的核心 |

## 来源与精度说明

本笔记依据 CS146S Fall 2025 官方 slides（周一主课《The AI IDE: Fundamentals to Power User》与周五嘉宾场 Cognition 的《IDE ❤ Agents》，讲者 Silas Alberti）整理，叙述结构参照第三方中文讲义（ai-course-notes 仓库 week03-notes.tex）。slides 本身是提纲式文本，细节稀疏处以讲义的概括为准；二手整理可能有遗漏或转述偏差，文中的效率数字（10% / 20% / 6–12 倍、10 倍并行）为嘉宾在课件中的主张，非独立验证数据。适合理解与复习，不作逐字引用。
