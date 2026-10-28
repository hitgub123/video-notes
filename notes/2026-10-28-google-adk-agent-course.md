# 谷歌 ADK 官方课合辑：从第一个 Agent 到长时运行与多智能体系统

- **原视频（X）**：https://x.com/huxlab/status/2115240099495788552
- **发布者**：Hux（@huxlab）转帖推荐；内容本身为 Google 官方 ADK（Agent Development Kit）系列视频
- **来源说明**：推文称其为「一门 70 分钟的官方课」，实际是把谷歌 6 段官方视频剪拼成的一条长视频（ADK 入门实操、Agent 记忆三部曲、长时运行 Agent、MCP 两段、多 Agent 系统），主题连贯，但并非单一课程
- **时长**：72 分 15 秒
- **笔记日期**：2026-10-28（英文语音转录 + AI 翻译总结；中文字幕见仓库 `subtitles/2026-10-28-google-adk-agent-course.zh.srt`）

## 章节

- 0:00 从零构建第一个 Agent：博客写作 Agent（规划 + 校验循环）
- 8:04 记忆之一 · 短期记忆：Session、Events 与 State
- 14:44 记忆之二 · 持久化：数据库 Session + 用户偏好库
- 22:11 记忆之三 · 长期记忆：Vertex AI Memory Bank 与语义检索
- 28:41 长时运行 Agent：能睡几天、醒来不丢上下文的入职协调 Agent
- 40:08 MCP 实操：给博客 Agent 接上 Google Trends 的 MCP Server
- 47:41 MCP vs API：为什么说 MCP 是「给模型的协议」
- 60:29 多 Agent 系统：搜索 Agent + 核查 Agent + 编排者

## 总结

### 模块一：第一个 Agent（博客写作 Agent）

先给定义：Agent 是不只回答、还能自己决定步骤并采取行动的软件，底层循环来自 ReAct 论文——推理 → 行动（调工具）→ 观察结果 → 再决定。Agent 分三类：**顺序型**（流水线，可预测但僵硬）、**反应型**（看当前状态临场决定，灵活但不规划）、**规划型**（先列计划再执行，多步任务用它）。

实操用 ADK 搭一个博客 Agent，结构值得记：

- **Blog Planner**（LLM Agent）：把主题变成 Markdown 大纲，关键设计是 `output_key`——产出存进共享 State（如 `blog_outline`），下一个 Agent 自动取用，Agent 之间靠 State 而不是聊天记录传数据
- **校验器 Agent**：只检查不创作，大纲齐全回「okay」，缺东西回「retry」并说明缺什么
- **LoopAgent**：把 Planner + 校验器包成循环，校验不过自动重试，上限 3 次——生成与评估分离的最小实现
- **Blog Writer** 同理再来一套（写手 + 校验 Loop），两个 Loop 各自包成工具交给 **Root Agent（Blogger）** 编排：规划 → 写作 → 补 3 个备选标题和 2 个推文钩子
- 运行用 `adk web`，有现成的调试 UI

### 模块二至四：记忆三部曲（旅行规划 Agent 贯穿示例）

**① 短期记忆 = Session。** Session 是一次完整对话，内含 **Events**（全部消息的记录，像文字实录）和 **State**（结构化的小便利贴，如餐厅名、确认号——免得模型为找一个值去翻 15 条消息）。默认的 InMemorySessionService 一开新 Session 就「失忆」；复用同一个 Session 即可记住上下文。铁律：不要在工具代码里直接改 Session 对象，要通过 ADK 给的 context 写 State，改动才会被记进事件日志、后续才能持久化。定位：像 RAM，快但临时。

**② 持久化 = 换存储引擎 + 偏好库。** 把 Session Service 换成 DatabaseSessionService（落盘，可接 Postgres），Agent 逻辑一行不改，App 重启后凭 Session ID 续上原对话。再加一张用户偏好表（一行一个 user_id + key，如 dietary=vegetarian），配两个工具：`recall_user_preference` 与 `save_user_preference`，并在指令里定死顺序：**开场先 recall → 个性化规划 → 收尾前 save**。这样新会话（新 Session ID）也能立刻认出老用户。注意分工：持久 Session 续的是「同一段对话」，偏好库管的是「跨对话的长期事实」。

**③ 长期记忆 = Memory Bank。** Session Service 管进行中的对话，Memory Service 管长期档案，两者是不同角色。Vertex AI Memory Bank 的做法：会话结束调 `add_session_to_memory` 把整段对话（甚至图片、视频、音频）交给它，它用一个模型**抽取事实**、用另一个模型**生成向量**，之后按语义检索（搜「双轮车辆」能命中「自行车」的记录）。Agent 侧挂 **PreloadMemoryTool**：每一轮回复前自动拿用户新消息去记忆库检索、把最相关事实注入提示词，Agent 本身不用写任何检索逻辑。演示：用户曾分享过一张古建筑照片，几周后新会话问「按我以前分享的推荐文化目的地」，Agent 能答出「喜欢历史建筑 + 海边」的组合推荐。

三层记忆合起来就是：Session/State（工作记忆）→ 持久 Session + 偏好库（跨重启、跨会话认人）→ Memory Bank（跨周跨月、跨模态的语义档案）。

### 模块五：长时运行 Agent（Long-Running Agents）

引谷歌博客《Building Long-Running AI Agents that Never Lose Context with ADK》的开源示例：**新员工入职协调 Agent**——发欢迎包后**停住几天**等新人签字，再等 IT 备机、物流送笔记本，最后生成第一天日程。它的「工作单元」不是一条提示词，而是一个跨天跨周的工作流。和普通聊天 Agent 的三个本质区别：

1. **持久化状态模式**：状态是数据库里的**状态机枚举**，不是原始聊天 JSON 堆在向量库里——不会上下文污染，不会几周后 token 爆炸；每步转移都落盘打检查点，容器崩了重部署也能原地续跑，不产生「幻觉记忆」
2. **事件驱动休眠**：步骤之间 Agent 完全休眠，没有轮询、没有空转线程烧算力；靠 Webhook、定时、人工审批、工具回调等外部事件唤醒（演示里 UI 不做乐观更新，必须等后端 resume 一轮真正跑完才刷新——以此证明模式是真的）
3. **评估与生成分离**：Agent 不能给自己打分——多家实验室（含 Anthropic）的研究结论一致：Agent 自评会系统性高估质量。对策是三件套：**Planner（规划）+ Generator（生成）+ Evaluator（独立评估，实测线上结果）**

长时 Agent 常撞的三堵墙正对应上面三点：上下文退化（窗口塞满质量掉）、缺持久状态原语（Agent 漂移、摆烂、放弃）、自我验证不可靠（不知道自己到底做完没有、做得好不好）。工程侧的解法统称 **Agent Harness 工程**：计划写成 Markdown 当「活文档」、变更日志当实验笔记、Ralph Loop 式长循环保底，再加托管基础设施（Gemini Enterprise Agent Platform 的 Session + Memory Bank 就是这套东西的产品化）。

### 模块六：MCP 实操 + MCP vs API

**实操**：把一个查 Google Trends 的普通 Python 函数包成 MCP Server——FunctionTool 自动从函数签名和 docstring 生成 schema（不用手写），Server 只做两件事：`list_tools`（被问「你有什么工具」时自报家门）和 `call_tool`（名字 + JSON 参数进，JSON 结果出），走 stdio 通信。ADK 的 Root Agent 把它当普通工具挂上，博客 Agent 写作前先查真实热点，不再凭模型记忆编。MCP 的四个价值：**隔离**（工具崩了不拖垮 Agent）、**互操作**（工具用什么语言写都行）、**可发现**（schema 自描述，运行时才认识也能用）、**可扩展**（加/换/版本化工具不用改 Agent）。

**概念**：API 是「程序对程序」的确定性契约——调用方必须事先知道端点、参数形状，全靠开发者读文档、写胶水、再在提示词里反复教模型怎么调。MCP 把这层反过来：它是「模型与环境」之间的**语义协议**，工具自带机器可读的说明，模型在运行时自己发现、自己决定何时调用。关键定性：**MCP 不取代 API，而是坐在 API 之上**——被替换的是模型与 API 之间的中间层，客户端从「另一个程序」变成了「模型本身」。类比：HTTP 当年统一了互联网协议，MCP 想统一 AI 的工具接入（一个连接器写一次，Gemini、Claude、GPT 都能用）。尚存的挑战说得很诚实：生态采用度、安全与权限分层（能力范围、鉴权要进协议层，已在做）、以及开发者思维要从「端点与路由」转向「能力与上下文」。

### 模块七：多 Agent 系统

先用 `adk create / adk run / adk web` 三件套起步，然后两步进化：

- **给 Agent 装工具**：函数工具（`get_current_time` 回答「现在几点」——模型本身不可能知道）与 ADK 内置工具（Google Search）。要点：装工具 = 把模型的能力边界往外推，工具清单是一个列表，想装几个装几个
- **为什么需要第二个 Agent**：搜索 Agent 给的来源里混着 404 和幻觉 URL——于是造一个 **URL Verifier Agent**（带 fetch_url 工具，输出 verified / not verified / inconclusive，本质是 LLM-as-Judge），再造 **Researcher Agent** 把 Search 与 Verifier 两个 Agent **当作工具**挂在自己名下做编排（Agent-as-Tool）。多 Agent 的收益：每个 Agent 单一职责，性能、记忆、成本都可单独管；还能按任务难度分模型——简单活用便宜快的，复杂推理用贵而强的

## 对自己项目的备注

- 本课的「校验 Loop」和长时模块的「三件套（规划/生成/评估分离）」正是人生K线工厂已在用的结构：生成脚本 → 独立事实核查回路、produce 断点续跑 = 检查点思想；可以对照 Memory Bank 一节想想工厂的「人物档案库」要不要从纯文件升级为可语义检索的记忆层
- MCP 一节与本站 CS146S 第 2 周笔记（从零构建 Coding Agent 与 MCP）互为印证：那边是原理手写，这边是 ADK + 现成 Server 的工程接法
- 推文宣传的「从玩具 Demo 到工业级架构的边界」，实际就落在模块五的三要素上：状态落盘、事件唤醒、评估分离——三缺一都撑不起「7×24 生产级」这个说法
