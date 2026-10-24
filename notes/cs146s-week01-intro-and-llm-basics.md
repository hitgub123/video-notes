# CS146S 第 1 周：课程导论与 LLM 工作原理速览

- **课程**：Stanford CS146S《The Modern Software Developer》，Fall 2025，主讲 Mihail Eric
- **周次与主题**：第 1 周——课程导论与 2025 年软件开发现状；LLM 工作原理速览；LLM Prompting 技术体系
- **来源**：课程存档页 https://themodernsoftware.dev/fall2025 ；课件镜像 https://github.com/hqhq1025/ai-course-notes
- **依据说明**：基于课程官方 slides（周一、周五两讲）整理，非视频总结；slides 文本稀疏处以中文讲义为准并保持概括
- **笔记日期**：2026-10-24

## 本周结构

- 周一讲：Introduction and How LLMs are Made（导论 + LLM 是怎样炼成的）
- 周五讲：LLM Power Prompting（提示词技术体系）

## 总结

### 课程导论：2025 年的软件开发现状

课程开篇先摆出"2025 年的世界现状"。先是坏消息（slides 该页仅残留与 Windsurf 团队相关的条目，细节未在材料中展开），随后是好消息——这是全课的基调：

- 软件开发者正拥有**历史上最高的生产力潜力**；
- 借助 AI 编程工具，工程师能以空前的速度上手新技术栈和工具；
- 全课的核心论断：**"You won't be replaced by AI. You'll be replaced by a competent engineer who knows how to use AI."**（取代你的不是 AI，而是会用 AI 的胜任工程师。）

讲师特别强调：**这不是一门 "vibe coding" 课**。重点不是让 AI 随意生成代码，而是理解 AI 编程工具的原理、掌握高质量的人机（human-agent）协作模式。

课程的四个核心理念（"The Takeaway"）：

1. **Human-agent engineering（人机协作工程）**：专注于 AI 尚未替代的技能——业务理解、技术架构决策，目标是让自己成为 tech lead。
2. **LLM 的能力上限取决于你**：好的 context 产生好的代码；如果你自己都理解不了你的代码库，LLM 也不能（"If you can't understand your codebase, neither will an LLM"）。
3. **大量阅读和审查代码**：学会分辨好代码与坏代码，培养"品味"（taste）。
4. **激进地实验**：目前还没有成熟的软件开发范式，每个人都在摸索；这门课会介绍很多工作流和工具，要试出适合自己的。

课程安排：讲座为周一/周五 8:30–9:20；10 周内容从 LLM 基础、编程 Agent 构建、AI IDE、终端工具、测试与安全、代码审查、UI 自动化构建、DevOps 一直讲到 AI 软件工程的未来。交付物是 9 个每周作业（作业仓库 github.com/mihail911/modern-software-dev-assignments）加 1 个期末开放项目，评分比例为项目/作业/参与 = 80/15/5。每周还有来自当今顶尖 AI 开发者创业公司的创始人客座讲座。主讲人 Mihail Eric 的背景：Stanford 本硕，在 Amazon Alexa 做过最早一批 LLM，创办并出售过一家 ML 教育创业公司，也创办过一家 YC 支持的 AI 编程公司，现任一家销售领域隐形创业公司的 Head of AI。

### LLM 工作原理速览：自回归的下一个 token 预测

周一讲的后半用 5 页 slides 给工程师速览 LLM 原理。一句话定义：**LLM 是做 next-token prediction 的自回归（autoregressive）模型**——不断预测下一个 token 并接龙下去。内部流程分四步：

1. **Tokenize（分词）**：用固定词汇表把输入文本切成 token；
2. **Embedding（嵌入）**：把每个 token 转成固定维度的数值向量（约 1–3K 维）；
3. **Transformer 层**：经过 12–96+ 层 Transformer，核心是 self-attention 机制（Vaswani et al. 2017）；
4. **输出概率分布**：在整个词汇表上给出下一个 token 的概率分布（slides 的示意图里，"the / idx / cat" 的概率分别是 0.3 / 0.6 / 0.1，模型据此选词续写）。

讲义补充：self-attention 让模型在生成每个 token 时能"看到"输入序列中的所有其他 token，从而捕捉长距离依赖——这是 LLM 能理解代码上下文的关键。

**训练三阶段范式**（slides 用同一个 "Write a for loop" 的例子贯穿三阶段，很能说明问题）：

- **Stage 1 自监督预训练**：在 Common Crawl、Wikipedia、StackExchange、公开 GitHub 仓库等数据上学习语言本身，规模达数千亿到万亿+ token（语言与代码混合）。此时模型只会"续写"——给它 "Write a for loop"，它会接着写出"……that could be used in a piece of code"这样一段讨论，而不是真的写代码。
- **Stage 2 监督微调（SFT）**：用数万到数十万组高质量的 prompt–response 对教模型听指令（如问 "what is the capital of Croatia" 答 "Zagreb is the capital"）。这时给它 "Write a for loop"，它会回应 "ok here's a for loop…" 并开始照做。
- **Stage 3 偏好对齐（preference tuning）**：目标是让输出符合人类偏好——helpfulness、correctness、readability。方法是收集同一 prompt 的成对输出、由人类标注比较（数万到数十万组），训练一个 reward model 来预测哪个输出更受偏好。经过这一阶段，模型对 "Write a for loop" 的回应直接变成干净可用的 `for idx in range(10):`。
- **推理模型（reasoning models）**是三阶段之上的扩展：加入 chain-of-thought 推理轨迹训练、整合工具使用、收集对推理步骤本身的人类偏好，并用强化学习让模型学会评估推理轨迹、在走不通时回溯（backtrack）。

**模型规模参考**（slides 给出的数字）：GPT-3 / Claude 3.5 Sonnet 约 175B 参数，LLaMA 3.1 为 405B，GPT-4 约 1.8T（据报道）。

**实践中的优势与局限**（slides 的 "In practice" 一页，是工程师用 LLM 前必须先有的预期）：

- 优势：专家级的代码补全、代码理解、代码修复；
- 局限一：**幻觉**——会生成不存在或已过时的 API；缓解手段是扎实的 context engineering（把真实代码和文档喂给它）；
- 局限二：**上下文窗口有限**——约 100–200K token，而且"并非所有 token 都同等有效"（讲义补充提醒：模型对窗口中间部分信息的关注度显著下降，即 "lost in the middle" 现象，不能把东西一股脑塞进 context 就指望好结果）；
- 局限三：**延迟**——每次请求数秒到数分钟，要据此规划和委派任务；
- 局限四：**成本**——最好的模型约输入 $1–3 / 百万 token、输出 $10+ / 百万 token。

### 周五讲：LLM Power Prompting 技术体系

周五讲的出发点：**prompt 是与 LLM 交互的"通用语言"（lingua franca），也是有效"编程" LLM 的手段**（slides 标注这一说法引自 Andrej Karpathy）。正如搜索引擎改变了人们获取信息的方式，prompting 在改变开发者与 AI 工具打交道的方式。它既是艺术也是科学：LLM 的黑盒性质让"LLM 耳语"带点魔法成分，但也有一批经实证验证、能稳定提升表现的技术。

**基础技术：**

- **Zero-shot prompting**：直接下指令，不给任何示例或支持信息。例："Write me a Rust for-loop that iterates over a list of strings, printing every value in an even index."
- **K-shot prompting（即 in-context learning）**：给 k 个示例再让它做，k 常取 1、3、5（有实证结果支持这几个数）。适合推理步骤不多的任务。它的关键价值是**把你的特定约定教给模型**：slides 的例子是"按我们仓库的命名规范写一个遍历字符串列表的 for loop"，并给出两个示例——示例中的变量名故意写成 `StRaRrAy`、`CaPiTaLiZeStR` 这类非常规大小写，演示如何用示例把代码库的命名规范传递给模型。
- **Chain-of-Thought（CoT）**：让模型展示推理步骤，适合有多个逻辑步骤的任务（编程、数学）。两种形式：multi-shot CoT（在示例里写出完整推理轨迹，如给"求列表最大值""判断回文数"两个带 Steps 的示例，再让模型判断一个数是否同时是完全平方数和完全立方数）；zero-shot CoT（不给示例，只在 prompt 里要求 "Think step by step about the subproblems before coding"，如求最长递增子序列时要求先逐步分析子问题、写出所考虑子数组的例子）。也可以要求把推理显式写在 `<reasoning>` 标签里。
- **Self-consistency**：对同一 prompt 多次采样（通常配合 CoT），对结果取多数票聚合。本质是一种模型集成（ensembling），用多条不同的推理路径来降低幻觉和错误答案。slides 的例子：给出一段 IndexError 的 traceback 问根因，prompt 5 次，取多数结果。

**高级技术：**

- **Tool use（工具使用）**：允许 LLM 把活推给它需要交互的外部系统。slides 称之为减少幻觉、实现 LLM 自主性最重要的 
...[truncated 2361 chars]