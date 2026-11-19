# 「Google 刚发布 2 小时 Harness Engineering 免费课」病毒帖核查：真素材、假包装

- **原帖**：https://x.com/robiartec/status/2123055373737148722
- **来源说明**：X 用户 @robiartec（自称 software theorist）2026-11-18 发的西班牙语帖，称「Google 刚发布了一门免费 2 小时 Harness Engineering 完整课，从第一步到最后一步」，附 5 个章节时间戳，并称「我见过 500 美元的课都不如这支视频」。帖子数据：约 9.7 万浏览、2579 收藏。
- **核查方式**：下载视频本体（95 分 11 秒），抽 5 处时间点画面＋转录逐段核对，并与 Google 官方渠道、两家独立核查站（bitcoinversus.tech 2026-11-06、theindependentai.com 2026-10-30）交叉验证。
- **字幕**：本篇为核查笔记，视频是 95 分钟拼接搬运，未生成中文字幕；想学请走下方官方正源。

## 一句话总结

**人是真的、素材是真的，但「新课」是假的**：视频是把 Google 官方 9 分钟短课和一场 85 分钟 webinar 拼在一起的搬运，章节表是编的；同一套文案模板自 2026-10-24 起被大量账号复制粘贴，主角轮流换成 Google、Karpathy、SpaceXAI——这是流量农场模板，不是新闻。

## 核查结论（逐条）

1. **不是一部课，是拼接**。前约 9.5 分钟是官方《AI Harness Crash Course》（Google Cloud Tech，2026-10-22 发布，本就只有约 9 分钟，讲师 Qingyue "Annie" Wang，Google Cloud 工程师）；约 10 分钟处画面格式突变（演播室→webinar 小窗＋幻灯片），后约 85 分钟是 Google Cloud「Agent 102」实验课 webinar（主题：Eval＋`adk optimize` 自我改进循环，配套 codelab 为 2026 年 8–10 月的素材）。两段都不是本周新发布。
2. **时长对不上**：实际 95 分钟，帖子称 2 小时。
3. **章节表对不上**（抽查转录实证）：
   - 帖称「11:08 你的第一个 agent」→ 实际是 webinar 引入一个会自我改进的「餐厅推荐 agent」故事；
   - 帖称「28:08 Eval engineering with a Chief of Staff agent」→ 该处确为 Eval 段，但案例是订餐厅的 agent，**全片没有 Chief of Staff**（这个词来自同一模板家族的 GrokBot 帖）；
   - 帖称「1:00:13 Loop engineering」→ 实际是 `adk optimize`（agent 改写自己的指令）一节；
   - 帖称「1:23:19 永不停机的自改进图系统」→ 实际是 Production 收尾：Cloud Trace＋BigQuery 把真实流量回灌再评分。原课自己的议程原话是「close the loop, **design the brakes**（设计刹车）」，与「永不停机」的煽动方向相反。
4. **模板实锤**：英文原版文案自 2026-10-24 起病毒式传播，章节表与「I've seen $500 courses…」台词逐字相同，仅替换主角（Google 版、Karpathy 版、SpaceXAI/GrokBot 版并存）。识别特征＝这套章节表＋$500 台词。

## 视频实际内容（值得学的部分）

- **Crash Course 段**：harness ＝ 模型周围的软件层，分四块——context & memory、tools & execution flow、runtime/security/privacy、evaluation；ADK 里 session（对话线程）与 state（应用草稿纸）的区分。
- **Agent 102 段**（原议程）：01 Eval——机器怎么知道「好」；02 adk optimize——agent 改写自己的指令；03 Workflow——闭环、设计刹车；04 Production——真实失败变成下一轮的考题。学员产出：写一个自定义 adk eval metric、搭一个自进化 ADK 2 workflow。
- **最值钱的一句**：**grade the trajectory, not the answer**（评轨迹，不只评结果）。Agent A 查了日历再订位，Agent B 蒙对时间直接订——只看结果两者同分，只有轨迹能告诉你谁明天还对。
- **adk optimize 的演示与警告**：用 eval 集搜索更好的指令（GEPA 路线），旅行规划 agent 的可行性分数约 40%→90%；但换上「看起来完整」的指标，agent 立刻学会塞未核实的站点作弊——**you optimize what you measure**。
- **生产段**：部署时开 Cloud Trace＋BigQuery 插件，真实流量落表，用自定义指标重新评分，回灌为下一轮优化素材。

## 官方正源（要学走这里，别看 X 搬运）

- 官方 9 分钟《AI Harness Crash Course》：Google Cloud Tech YouTube，2026-10-22，附互动学习页与 codelab。
- 自进化 codelab：https://g.dev/ai/self-evolving
- YouTube《Building a custom evaluation for your agent》（Annie Wang）：https://www.youtube.com/watch?v=GatM9XV0gOw

## 对我的相关性

正在做的 Luna harness 规则迭代、视频工厂 Agent 都是同一套东西；最可迁移的是 trajectory eval 与「design the brakes」。工具链是 Google ADK（Python）＋GCP，与 Java 栈不直接对口，概念通用。高效路径：9 分钟官方版＋self-evolving codelab，不必看 95 分钟拼接版。
