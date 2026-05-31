# 元奖励语言模型：基于 LLM 元裁判的自我改进对齐（Meta-Rewarding Language Models: Self-Improving Alignment with LLM-as-a-Meta-Judge）

> arXiv: 2407.19594 ｜ Tianhao Wu, Weizhe Yuan, Olga Golovneva 等 / Meta FAIR & UC Berkeley & NYU ｜ 2024

## 摘要（全文翻译）

大语言模型（LLMs）正在许多领域迅速超越人类的知识水平。传统上改进这些模型依赖高昂的人工数据，而近期的自我奖励机制（Yuan 等人，2024c）表明，LLM 可以通过判断自身回复来自我改进，而无需依赖人工标注者。然而，现有方法主要聚焦于改进模型回复质量，而忽视了提升判断能力，导致在迭代训练中迅速出现饱和。为解决这一问题，我们在自我改进流程中引入了一个新颖的**元奖励（Meta-Rewarding）**步骤——让模型对自己的判断进行再次评判，并利用该反馈精炼其判断能力。令人惊喜的是，这一无监督方法不仅提升了模型的判断能力，也改善了其遵循指令的能力：Llama-3-8B-Instruct 在 AlpacaEval 2 上的胜率从 22.9% 提升至 39.4%，在 Arena-Hard 上从 20.6% 提升至 29.1%。这些结果有力地表明，在无人工监督的情况下实现模型自我改进具有巨大潜力。

## 背景与动机

LLM 的指令调优（instruction tuning）和基于人类反馈的强化学习（RLHF）极度依赖昂贵的人工标注数据，且人类能力天花板也限制了监督信号的质量。"超级对齐（Super Alignment）"挑战指出：当 AI 能力超越人类判断时，如何为其提供有效反馈。

现有自我奖励（Self-Rewarding）方法让模型同时扮演**演员**（生成回复）和**裁判**（评估回复），以迭代方式自我提升。然而，该方法存在一个关键缺陷：**它只训练了演员，从未训练裁判**。若裁判能力不随迭代提升，奖励信号质量将迅速饱和，甚至引发奖励欺骗（reward hacking），导致演员过拟合一个越来越不准确的裁判。

本文的核心洞见：**裁判和演员同等重要，必须同步提升。**

## 方法（核心机制）

元奖励机制在自我奖励框架基础上，引入第三个角色——**元裁判（Meta-Judge）**，同样由同一个模型扮演。

### 三角色迭代训练

整个流程形成一个闭环：

**1. 演员数据创建（Actor Data Creation）**

- 对每个提示 x，从当前模型采样 K=7 个不同回复 {y₁, ..., yₖ}；
- 对每个回复，生成 N=112 条裁判评分，取平均分作为最终奖励；
- 引入**长度控制（Length-Control）机制**：定义质量分位参数 ρ，在最高分附近区间内选择**最短的**回复作为 chosen，在最低分附近区间内选择**最长的**作为 rejected，以对抗裁判偏好长回复的系统性偏差；
- 用 DPO（直接偏好优化）在该偏好对上训练演员。

**2. 裁判数据创建（Judge Data Creation）**

- 选出裁判对某回复打分**方差最大**的样本（即裁判最不确定的情况）；
- 对同一回复的 N 条判断两两配对，使用 LLM-as-a-Meta-Judge 提示（见论文图2）让元裁判比较哪条判断更准确；
- 为缓解**位置偏差（Positional Bias）**，交换两条判断的顺序各提示一次，用加权 Elo 评分汇总比较结果；
- 选出 Elo 最高和最低的判断对作为裁判的 chosen/rejected，同样用 DPO 训练裁判能力；
- 对所选判断额外过滤掉过长的项，以防止裁判偏好导致判断文本也越来越冗长。

### 迭代过程

- **Iter 1 & 2**：同时训练演员和裁判（使用元裁判反馈）；
- **Iter 3 & 4**：仅训练演员（因为此时发现元裁判在 Iter 2 后出现严重的分数偏差，继续训练裁判效果不佳）。

### 元裁判提示设计

提示要求模型：给定原始问题、一条模型回复及两条对该回复的判断，按照固定的 5 分评分标准，分析并选出哪条判断更准确，最后以"Winner: [Judgement A | Judgement B]"格式输出。

## 实验与结论

**实验设置：** 从 Llama-3-8B-Instruct 出发，先对 EFT（评估微调）数据集进行 SFT 预热，再进行 4 轮元奖励迭代，每轮使用 5000 条 Llama-2-70B-Chat 生成的多样化提示。

**演员能力评估（指令遵循）：**

| 基准 | 种子模型 | SFT | Self-Rewarding+LC Iter4 | Meta-Rewarding Iter4 |
|---|---|---|---|---|
| AlpacaEval 2 LC 胜率 | 22.9% | 25.5% | 35.5% | **39.4%** |
| Arena-Hard | 20.6% | 24.2% | 27.3% | **29.1%** |

- AlpacaEval 2 最终胜率（39.4%）超越 GPT-4-0314，接近 Claude Opus；
- Arena-Hard 提升 +8.5%；
- MT-Bench Turn 1 分数大幅提升（8.319 → 8.738），Turn 2 几乎无损失；
- 在 AlpacaEval 18 个类别中 17 个类别均有改进。

**裁判能力评估（奖励建模）：**

以 GPT-4 判断为参照，元奖励模型相较 Self-Rewarding 基线，在 GPT-4 选定对的一致率提升超过 6%，在自选对（Self-Chosen Pairs）的无平局一致率提升最高 +12.34%（Iter 2），证明元裁判机制确实改善了裁判准确性。

**局限性：**
- 5 分制评分系统容易产生大量平局，需要多次采样取均值才能区分；
- 随训练推进，裁判趋于给出极高分数（均值从 4.1 升至 4.7+），区分能力下降；
- 元裁判在 Iter 2 后出现严重的位置偏差和分数偏差（97.7% 的判断偏向更高分），导致后续迭代无法继续进行裁判训练；
- 对非自生成回复（如人工回复）的判断泛化能力有限。

## 关键术语对照表

| 英文 | 中文 |
|---|---|
| Meta-Rewarding | 元奖励 |
| Self-Rewarding | 自我奖励 |
| LLM-as-a-Judge | 以 LLM 为裁判 |
| LLM-as-a-Meta-Judge | 以 LLM 为元裁判 |
| Meta-Judge | 元裁判 |
| Actor | 演员（生成回复的角色）|
| Judge | 裁判（评估回复的角色）|
| DPO（Direct Preference Optimization）| 直接偏好优化 |
| RLHF（Reinforcement Learning from Human Feedback）| 人类反馈强化学习 |
| Length-Controlled Win Rate (LC Win Rate) | 长度控制胜率 |
| Length Bias | 长度偏差 |
| Positional Bias | 位置偏差 |
| Score Bias | 分数偏差 |
| Reward Hacking | 奖励欺骗 |
| Super Alignment | 超级对齐 |
| Elo Score | Elo 评分 |
| Preference Optimization | 偏好优化 |
| Iterative DPO | 迭代直接偏好优化 |
| AlpacaEval 2 | AlpacaEval 2 基准 |
| Arena-Hard | Arena-Hard 基准 |
