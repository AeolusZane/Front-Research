# 自进化 AI 智能体综合综述：连接基础模型与终身主体系统的新范式（A Comprehensive Survey of Self-Evolving AI Agents: A New Paradigm Bridging Foundation Models and Lifelong Agentic Systems）

> arXiv: 2508.07407 ｜ Jinyuan Fang, Yanwen Peng, Xi Zhang 等（格拉斯哥大学、谢菲尔德大学、新加坡国立大学、剑桥大学等多机构） ｜ 2025

## 摘要（全文翻译）

大型语言模型（Large Language Models，LLMs）的最新进展激发了研究界对能够解决复杂现实任务的 AI 智能体的浓厚兴趣。然而，现有大多数智能体系统依赖手工配置，一旦部署便保持静态，难以适应动态演化的环境。为解决这一局限，近期研究探索了智能体进化技术，旨在基于交互数据与环境反馈自动提升智能体系统。这一新兴方向奠定了自进化 AI 智能体的基础，架起了基础模型的静态能力与终身主体系统所需持续适应性之间的桥梁。本综述对现有自进化主体系统技术进行了系统全面的回顾。具体而言，我们首先提出一个统一概念框架，抽象出自进化主体系统设计背后的反馈回路，该框架突出了四个核心组件：系统输入、智能体系统、环境和优化器，为理解和比较不同策略提供基础。基于该框架，我们系统梳理了针对智能体系统不同组件的大量自进化技术，包括基础模型、智能体提示词、记忆、工具、工作流以及智能体间通信机制。我们还调研了在生物医学、编程、金融等专业领域开发的领域特定进化策略，这些领域中智能体行为与优化目标与领域约束紧密耦合。此外，我们专门讨论了自进化主体系统的评估、安全与伦理考量，这些对于确保系统有效性与可靠性至关重要。本综述旨在为研究者和从业者提供对自进化 AI 智能体的系统性理解，为开发更具适应性、自主性的终身主体系统奠定基础。GitHub：https://github.com/EvoAgentX/Awesome-Self-Evolving-Agents

## 背景与动机

现有 AI 智能体系统——无论单智能体还是多智能体——普遍依赖人工设计的静态配置。现实世界中，用户意图会变化、任务需求会迁移、外部工具或信息源随时间更新，静态架构难以应对。例如，为客户服务的智能体需要处理新推出的产品和更新的公司政策；科研助手需要整合新发表的算法或新分析工具。手动重新配置既费时费力，又难以扩展。

本综述提出"**自进化 AI 智能体**（Self-Evolving AI Agents）"这一新范式，定义其为：**自主系统通过与环境交互持续、系统地优化内部组件，以适应变化的任务、情境和资源，同时保障安全并提升性能**。

作者将 LLM 中心学习的发展历程划分为四个递进范式：

- **MOP（模型离线预训练，Model Offline Pretraining）**：在大规模静态语料上预训练，部署后固定不变；
- **MOA（模型在线适应，Model Online Adaptation）**：通过监督微调（SFT）、LoRA、RLHF 等技术在部署后更新模型；
- **MAO（多智能体编排，Multi-Agent Orchestration）**：多个 LLM 智能体通过消息交换或辩论提示词协同解决复杂任务，不修改模型参数；
- **MASE（多智能体自进化，Multi-Agent Self-Evolving）**：智能体群体基于环境反馈和元奖励，持续精炼提示词、记忆、工具使用策略乃至拓扑结构，形成终身学习闭环。

受阿西莫夫机器人三定律启发，作者还提出了**自进化 AI 智能体三定律**：其一，Endure（安全适应）——任何修改必须保障安全稳定；其二，Excel（性能保全）——在满足第一条的前提下，必须保留或提升现有任务性能；其三，Evolve（自主进化）——在满足前两条的前提下，能够自主响应变化的任务、环境或资源，对内部组件进行优化。

## 方法（核心机制）

### 统一概念框架（MASE Framework）

论文提出四组件反馈回路框架，形成迭代优化闭环：

**1. 系统输入（System Inputs）**  
分为任务级优化（task-level）和实例级优化（instance-level）两类。任务级输入包含任务描述 T 和训练数据集 D_train；实例级输入则聚焦单个输入-输出对 (x, y)。当标注数据缺失时，可用 LLM 动态合成替代数据集。

**2. 智能体系统（Agent System）**  
可为单智能体或多智能体，包含以下可优化子组件：
- **基础模型（Foundation Model）**：作为核心推理引擎；
- **感知模块（Perception Module）**：处理文本、音频、视频等多模态输入；
- **规划模块（Planning Module）**：从线性任务分解（如 Chain-of-Thought）到动态规划（ReAct）、树/图结构规划（Tree-of-Thought、Graph-of-Thought）；
- **记忆模块（Memory Module）**：短期记忆（当前任务上下文）与长期记忆（跨任务积累知识），通过检索增强生成（RAG）整合；
- **工具使用（Tool Use）**：外部工具调用，如搜索引擎、代码解释器、浏览器自动化。

多智能体系统（MAS）额外涉及**拓扑结构**（层级式、集中式、去中心化）与**通信机制**（结构化输出、自然语言、标准化协议如 MCP、A2A、ANP）。

**3. 环境（Environment）**  
提供操作上下文并生成反馈信号。评估指标分为：有标注场景下的准确率、F1、成功率等；无标注场景下的 LLM-based 评估器生成代理指标。

**4. 优化器（Optimiser）**  
核心目标：A* = argmax_{A∈S} O(A; I)，在搜索空间 S 中找到最优智能体配置。由两个子组件定义：
- **搜索空间（Search Space）**：从提示词模板、工具选择到 LLM 参数、架构结构；
- **优化算法（Optimisation Algorithm）**：规则启发式、梯度下降、贝叶斯优化、蒙特卡洛树搜索（MCTS）、强化学习、进化策略、学习型策略。

### 单智能体优化分类

- **提示词优化（Prompt Optimisation）**：编辑式（TEMPERA、GRIPS）、生成式（OPRO、APE）、文本梯度（TextGrad）、进化式（EvoPrompt）；
- **记忆优化（Memory Optimisation）**：短期记忆压缩（Mem1）、长期记忆结构化（MemGPT、HippoRAG、A-Mem）；
- **工具优化（Tool Optimisation）**：训练式（ToolRL、SwiRL）、推理时优化（ReTool）、基于提示词的工具创建（CREATOR、LATM）；
- **基础模型优化（LLM Optimisation）**：通过 STaR、RLVR、GRPO 等强化推理与规划能力。

### 多智能体优化分类

- **工作流优化（Workflow Optimisation）**：AFlow、MermaidFlow、EvoFlow 等自动设计工作流拓扑；
- **拓扑优化（Topology Optimisation）**：DyLAN、MacNet、ADAS 等动态调整智能体结构；
- **通信优化（Communication Optimisation）**：优化智能体间信息共享协议。

### 领域特定进化

- **科学研究**（ChemAgent、LLM-RDF）；
- **生物医学**（MDAgents、MedAgentSim）；
- **编程**（SelfDebugging、OpenDevin）；
- **法律/金融**（LawLuo、FinCon、FinRobot）。

## 实验与结论

### 主要发现

综述覆盖 2023—2025 年数十个代表性系统，通过可视化分类树（图 2）呈现单智能体优化、多智能体优化、领域特定优化三大方向的演进脉络。

**评估挑战**：现有基准（如 HotpotQA、GSM8K、SWE-bench）未必能充分衡量终身学习与持续自进化能力；LLM-as-a-Judge 评估器引入偏差；跨任务泛化与分布外评估仍不足。

**安全与伦理**：自进化系统面临目标偏移（goal drift）、价值对齐失效、不可预测行为等风险，需引入安全适应（Safety Adaptation，即三定律第一条"Endure"）机制。G-Safeguard 等工作探索了多智能体场景下的安全护栏设计。

**未来方向**：
1. 构建专用于自进化能力的评估基准；
2. 推进终身学习与灾难性遗忘（catastrophic forgetting）防御；
3. 实现模型参数、提示词、记忆、工具的联合优化；
4. 探索多智能体拓扑的自动涌现；
5. 在真实动态环境（机器人、IoT、网络导航）中验证自进化效果；
6. 建立自进化 AI 的安全对齐理论框架。

## 关键术语对照表

| 英文 | 中文 |
|---|---|
| Self-Evolving AI Agent | 自进化 AI 智能体 |
| Multi-Agent Self-Evolving (MASE) | 多智能体自进化 |
| Lifelong Learning | 终身学习 |
| Foundation Model | 基础模型 |
| Prompt Optimisation | 提示词优化 |
| Retrieval-Augmented Generation (RAG) | 检索增强生成 |
| Reinforcement Learning from Human Feedback (RLHF) | 来自人类反馈的强化学习 |
| Tool Use | 工具使用 |
| Agent Topology | 智能体拓扑 |
| Agentic Workflow | 主体工作流 |
| Catastrophic Forgetting | 灾难性遗忘 |
| LLM-as-a-Judge | LLM 作为评判者 |
| Monte Carlo Tree Search (MCTS) | 蒙特卡洛树搜索 |
| Chain-of-Thought (CoT) | 思维链 |
| Multi-Agent System (MAS) | 多智能体系统 |
| Goal Drift | 目标偏移 |
| Model Context Protocol (MCP) | 模型上下文协议 |
