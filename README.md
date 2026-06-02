# Agent Confidence Tracing

Capability-Aligned Confidence Tracing in Agentic AI

## 项目简介

大语言模型（LLM）在 Agent 场景中需要完成多步推理、工具调用与环境交互。然而，目前大多数关于模型置信度（Confidence）的研究主要聚焦于单轮问答任务，对于 Agent 在执行过程中的自我认知能力缺乏系统研究。

本项目基于 AgentBench OS Benchmark，对 Agent 执行流程进行改造，在每一步推理与工具调用过程中记录模型自评置信度（Self-Confidence），从而研究：

* 模型是否真正了解自己的能力边界
* 模型置信度是否能够预测任务成功率
* 多步任务中置信度如何动态变化
* Agent 是否存在系统性过度自信（Overconfidence）

---

## 核心贡献

### 1. Step-Level Confidence Tracing

在 Agent 原有执行流程中增加置信度追踪机制：


#### Original Agent
```text
Think
 ↓
Tool
 ↓
Observation
 ↓
Answer
```

#### Modified Agent
```text
Think
 ↓
Confidence Estimation
 ↓
Tool
 ↓
Observation
 ↓
Confidence Update
 ↓
Answer
```

模型在每一步都需要输出当前成功概率（0~1），从而形成完整的置信度轨迹（Confidence Trajectory）。

---

### 2. 构建 Agent 置信度评测框架

基于 AgentBench OS Tasks：

* Linux Sandbox Environment
* 144 个任务样本
* 7 类操作系统任务
* Docker 隔离执行环境

评测多个主流闭源模型：

* GPT-4.1-mini
* GPT-4o-mini
* Gemini 2.0 Flash
* Gemini 2.5 Flash
* Claude Haiku 4.5
* Claude Sonnet 4.5

---

### 3. 设计置信度评测指标

#### CDS (Confidence Discrimination Score)

衡量成功任务与失败任务之间的置信度区分能力。

```text
CDS > 0
模型在成功时更有信心
```

---

#### ECE (Expected Calibration Error)

衡量模型置信度与真实成功率之间的偏差。

```text
ECE 越小越好
```

---

#### CLES

衡量成功轨迹与失败轨迹之间的可分离程度。

```text
CLES 越远离 0.5 越好
```

---

## 实验结果

### Accuracy vs Confidence

| Model             | Accuracy | Avg Confidence | Overconfidence |
| ----------------- | -------- | -------------- | -------------- |
| GPT-4.1-mini      | 39.6%    | 92.2%          | +52.6pp        |
| GPT-4o-mini       | 36.1%    | 78.6%          | +42.5pp        |
| Gemini-2.0-Flash  | 31.9%    | 80.5%          | +48.6pp        |
| Claude-Haiku-4.5  | 25.7%    | 83.1%          | +57.4pp        |
| Gemini-2.5-Flash  | 19.4%    | 87.6%          | +68.2pp        |
| Claude-Sonnet-4.5 | 16.0%    | 82.9%          | +67.0pp        |

---

## 主要发现

### 所有模型均存在明显过度自信现象

模型报告的平均置信度远高于真实任务成功率。

大多数模型：

```text
真实成功率：20% ~ 40%
自评成功率：80% ~ 90%
```

平均高估幅度达到：

```text
40% ~ 60%
```

---

### 相对置信度仍然具有一定价值

虽然模型无法准确预测绝对成功率，但成功任务通常仍比失败任务拥有更高的置信度。

说明当前模型具有一定的：

* Self-Monitoring Ability
* Failure Awareness

但整体校准能力仍然较弱。

---

## 技术栈

* Python
* AgentBench
* Docker
* OpenAI API
* Anthropic API
* Gemini API
* Linux Sandbox

---

## 项目结构

```text
agent-confidence-tracing
│
├── prompts/          # Agent prompts
├── evaluation/       # Evaluation scripts
├── results/          # Experimental results
├── figures/          # Plots and visualizations
├── scripts/          # Utilities
├── data/             # Task data
└── README.md
```

---

## Future Work

* Confidence-aware Replanning
* Failure Detection
* Adaptive Tool Selection
* Capability-Aware Agents

---

## Author

Xiang Yu

M.S. in Computer Science

Stevens Institute of Technology
