# AI 自进化与自我改进：经典论文中文翻译合集

> 本合集收录了 2003-2025 年间 AI 自进化（Self-Evolving AI）领域的 13 篇核心论文中文翻译，按研究主题分为 5 个方向，覆盖从自训练推理基础到递归自我改进的完整技术脉络。

## 分类索引

### 01-自训练与推理基础

LLM 自我改进的早期探索：如何让模型通过自身生成的数据来提升推理能力。

| 论文 | 年份 | 核心贡献 |
|------|------|---------|
| STaR: Bootstrapping Reasoning with Reasoning | 2022 | 用推理引导推理，让模型通过生成推理链来自我训练 |
| LLMs Can Self-Improve | 2022 | 证明 LLM 无需外部数据即可通过自我对弈提升能力 |
| Beyond Human Data: Scaling Self-Training (ReST-EM) | 2023 | 超越人类标注数据，用自训练规模化解决问题 |

### 02-反思与元学习

从"做"到"想"：让智能体具备反思失败、调整策略的元认知能力。

| 论文 | 年份 | 核心贡献 |
|------|------|---------|
| Reflexion: Verbal RL Agents | 2023 | 用语言反思替代传统强化学习，智能体从失败中学习 |
| Meta-Rewarding Language Models | 2024 | LLM 作为元裁判，通过自我评估实现对齐改进 |

### 03-开放式学习与科学发现

从封闭任务到开放世界：智能体在无限环境中持续学习和发现。

| 论文 | 年份 | 核心贡献 |
|------|------|---------|
| Voyager: Open-Ended Embodied Agent | 2023 | Minecraft 中的终身学习智能体，自主探索并积累技能库 |
| The AI Scientist (Sakana AI) | 2024 | 全自动科学发现系统，从假设到实验到论文一条龙 |
| AlphaEvolve (DeepMind) | 2025 | 面向科学与算法发现的编程智能体，进化式代码优化 |

### 04-自指智能体与递归自我改进

终极目标：让智能体改写自身代码，实现可证明的递归自我改进。

| 论文 | 年份 | 核心贡献 |
|------|------|---------|
| Gödel Machines (Schmidhuber) | 2003 | 奠基之作：可证明最优的自指自我改写系统 |
| ADAS: Automated Design of Agentic Systems | 2024 | 自动设计智能体系统，元搜索发现最优 Agent 架构（ICLR 2025） |
| Gödel Agent: Recursive Self-Improvement | 2024 | 现代版哥德尔机，基于 LLM 的递归自我改进框架 |
| Darwin Gödel Machine | 2025 | 进化算法 + 哥德尔机，开放式自我进化（ICLR 2026） |

### 05-综合综述

全景式梳理自进化 AI 智能体的研究现状与未来方向。

| 论文 | 年份 | 核心贡献 |
|------|------|---------|
| A Comprehensive Survey of Self-Evolving AI Agents | 2025 | 连接基础模型与终身主体系统的新范式综述 |

## 技术演进脉络

```
2003  Gödel Machine（理论奠基）
  │
  ├── 2022  STaR / LLMs Can Self-Improve（自训练实践）
  │     └── 2023  ReST-EM（规模化自训练）
  │
  ├── 2023  Reflexion（反思学习）/ Voyager（开放式学习）
  │     └── 2024  Meta-Rewarding（元奖励）
  │
  ├── 2024  AI Scientist / ADAS / Gödel Agent（系统级自我改进）
  │     └── 2025  AlphaEvolve / Darwin Gödel Machine（进化式自我改进）
  │
  └── 2025  Comprehensive Survey（领域综述）
```

---

*整理于 2026 年 5 月 31 日*
