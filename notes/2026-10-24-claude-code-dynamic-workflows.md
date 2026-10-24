# Claude Code 主题演讲：动态工作流、多 Agent 并行与 Fable 5（Code with Claude 大会）

- **原视频（X）**：https://x.com/aresotik/status/2108984260837425478
- **分享者**：ares. 🎧（@aresotik），帖子发布于 2026-10-10，帖子文案为西班牙语
- **出处**：Anthropic「Code with Claude」大会的 Claude Code 主题演讲（讲者未在视频中自报姓名；本场之前有 Caitlin、Angela 关于在 Claude 平台上构建生产级 Agent 的演讲）
- **原视频语言**：英语
- **时长**：12 分 15 秒
- **笔记日期**：2026-10-24（英文语音转录 + AI 翻译总结；中文字幕见仓库 `subtitles/2026-10-24-claude-code-dynamic-workflows.zh.srt`）

> ⚠️ **帖子文案与实际内容不符**：原帖宣称这是「12 分钟完整课程：如何搭建能自我改进的 Agent，全程使用 Claude Fable 5」。实际内容是一场 Claude Code 产品主题演讲——新界面、Routines、安全扫描、动态工作流与 Fable 5 发布，并没有搭建自改进 Agent 的教学步骤。本笔记按视频实际内容整理。

## 章节

- 0:00 开场：Claude Code 的使命——弥合想法与已交付产品
- 2:08 工作方式的变化：从逐条审查到自动模式
- 2:12 界面扩张：CLI、IDE、桌面版、CLI 的 Agent 视图
- 4:27 按用户反馈长出的五件产品
- 6:49 企业案例：Spotify 与 Mercari
- 7:51 演示：动态工作流并行本地化 13 种语言
- 10:44 收尾：Claude Fable 5 与「同一个故事的三个层次」

## 总结

### 他讲的是什么

Claude Code 团队的大会主题演讲。主线是：Claude Code 的使命是弥合「一个想法」与「一件已交付产品」之间的差距；过去一年，开发者与它的协作方式从"逐条审查每一处修改"进化到"自动模式委托权限、只审最终 PR"；为支撑这种并行、多会话的工作方式，产品从 CLI 扩张到 IDE、桌面版和 Agent 控制台，并按用户反馈长出了代码审查、移动端、Routines、安全扫描、动态工作流五件产品；压轴演示用动态工作流把一个网站并行本地化成 13 种语言，并发布新模型 Claude Fable 5。

### 工作方式的变化

- 去年：给 Claude Code 一个任务后，逐一审查它做的每个修改、在每个小细节上给详细指令
- 现在：用自动模式（auto mode）把权限委托给它，只在它已经测试完改动、提出一个待审查的 PR 之后才介入
- 结果数字：工程师人均交付的代码量是过去几年的 8 倍（且这是在工程团队规模已大幅增长的前提下）

### 界面与底座

- **CLI**：Claude Code 的起点，至今是高级用户主场——极简文本界面、最大的控制权与可定制性
- **IDE 扩展**：同样的改动能力，但能全程跟看每一处代码变化
- **桌面版 Claude 里的 Claude Code**（讲者最常用）：全屏图形界面、内置预览、侧边栏控制面、能渲染图像与富格式输出；一个统一视图统管所有本地与云端会话，并标记哪些 Agent 在工作、哪些在等你的输入
- **CLI 的 Agent 视图**（最新）：不离开终端的控制台——看到什么在等你、什么在运行、什么已完成；行内回复即可解阻，或跳进跳出任何会话而不丢位置
- 底座统一：IDE 扩展和桌面版应用都构建在 **Claude Agent SDK** 之上，与外部开发者用的是同一个 SDK

### 按用户反馈长出的五件产品

1. **代码审查**：用户想少花时间在 review 上 → 部署一支 Agent 团队专抓 PR 里的关键 bug；每天数千家公司在用，包括 Anthropic 内部全部团队
2. **移动端**：用户想随时随地写代码 → Remote Control + iOS/Android 版 Claude Code，"去公园摸摸草也能把任务做完"
3. **Routines（例行任务）**：用户想让 Claude Code 自动处理新工单 → 配置一次，即可按定时计划、webhook 或 API 调用在正确的任务上自动启动它
4. **Claude Security**：用户落地的代码多到安全团队跟不上 → 夜间扫描代码库，标出一系列漏洞（包括它认为最关键的），并可一键启动 Claude Code 会话逐个处理
5. **动态工作流（dynamic workflows）**：用户想跑横跨整个代码库的大重构、大迁移 → 以确定性结构并行运行数十乃至数百个 Agent

这五件"原语"可以彼此组合，目的是让开发者更容易适应工程形态的继续变化。

### 企业案例

- **Spotify**：在 Claude Agent SDK 上构建后台 Agent，读取一份用日常英语写的迁移计划，再启动一支 Agent 舰队去开 PR 迁移数千个仓库；每月向生产环境合并超过 1,000 个 PR，迁移时间缩短 90%
- **Mercari**（日本 C2C 平台）：整个工程团队都在用 Claude Code，测得工程产出同比增长 90%

### 演示：动态工作流并行本地化

- 场景：虚构公司 Kaizen Operations 的营销网站只有英文，要进入另外 13 个市场
- 单 Agent 做法：一个 Claude Code 把网站转成日语（探索代码库、建语言选择器、翻译全部文本）约花 3 分钟；13 种语言顺序做要将近 1 小时，还要大量手动发提示
- 动态工作流做法：一个提示让 Claude 生成一个可重复的工作流，12 个翻译 Agent 同时并行；翻译完成后自动再建 12 个校验 Agent 检查各语言成果；12 个本地化版本一次产出
- 工作流可以保存为 JavaScript 代码，供以后翻译时复用；同类用法还有大规模迁移、代码库审计、性能优化

### 收尾：三层故事

讲者把当天大会的三场演讲归纳为同一个故事的三个层次：Diane 的模型能力曲线、Angel 的"运行在你所掌控的基础设施上的 Agent"，以及本场的 Claude Code。剩下的差距只是"我们能多快把这些能力投入工作"。这一切都运行在新模型 **Claude Fable 5** 上——"我们为 Agent 工作交付过的最好的模型"，即日上线，向所有 Claude Code 用户开放。

## 核心概念速查

| 概念 | 一句话 |
|---|---|
| 动态工作流（dynamic workflows） | 用确定性结构编排数十至数百个 Agent 并行干活；流程可保存为 JavaScript 代码复用 |
| Routines（例行任务） | 配置一次，由定时、webhook 或 API 触发，自动在指定任务上启动 Claude Code |
| 自动模式（auto mode） | 把权限委托给 Agent，人只在它测完改动、提出 PR 后才介入审查 |
| Agent 视图（agents view） | CLI/桌面端的控制台：看哪些会话在等你、在运行、已完成 |
| Claude Agent SDK | IDE 扩展、桌面版与外部开发者共用的 Agent 构建底座 |
| 代码审查 Agent 团队 | 部署一组 Agent 专抓 PR 中的关键 bug，替代人肉 review 的第一道筛 |
| 多开 Claude（multi-Clauding） | 并行运行多个 Claude Code 会话的工作方式（用户给的爱称） |
| Claude Fable 5 | 本场发布的新模型，定位为 Agent 工作的最强模型 |

## 与我的项目的关联

动态工作流的形态——编排器把大任务拆成确定结构、并行派子 Agent、再派一轮校验 Agent、整个流程沉淀为可复用代码——与我的「人生 K 线视频工厂 Agent 化」方案（video-agent-plan.md）里 P2/P3 的设计（主编排器 + QA 子 Agent + 技能文件复用）基本同构；这场演示的"12 翻译 + 12 校验并行"是最直观的参照画面。

## 精度说明

本笔记基于机器转录（Whisper）+ AI 翻译整理。Whisper 把 "Claude" 系统性误听为 cloud / Quad（转录稿中的 "cloud code""Quad Code""Cloud Fable 5" 均为 Claude Code / Claude Fable 5），另有 multi-clotting（应为 multi-Clauding）、shift product（应为 shipped product）、termnistic（应为 deterministic）等零星误听，已按语境统一校正。讲者姓名未在音频中出现，故未记录。X 帖文案（西班牙语）与视频实际内容有出入，见文首说明。适合理解与复习，不作逐字引用。
