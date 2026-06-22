<title>Benchmark 基础知识</title>

**benchmark通常会包含：**

- 数据集（questions/tasks）
- 标准答案（ground truth）
- 评测规则（metrics）
- 测试流程（evaluation protocol）

---

# 一个完整 benchmark 的设计逻辑

其实通常是：

```Plain Text
现实问题 / 产品需求
        ↓
定义想测的能力
        ↓
定义成功标准
        ↓
设计任务与数据
        ↓
定义评分方式
        ↓
验证 benchmark 是否可靠
```

---

# 一、首先：你为什么要测这个模型？

这是 benchmark 最源头的问题。

因为：

不同目标，

benchmark 完全不同。

例如：

<sheet sheet-id="5dtkRl" token="Dv63sGvUghzdAjtoJa8cFOhtnCf"></sheet>

所以：

> benchmark 本质上是“能力抽象”。

---

# 二、决定 benchmark 的核心信息有哪些

通常需要下面几类信息。

---

# 真实使用场景（最重要）

这是 benchmark 的根。

例如：

如果你做：

## AI coding agent

真实世界会遇到：

- 长代码库
- dependency
- test failure
- ambiguous issue

于是：

SWE-bench 才会设计：

- repo
- issue
- unit test

而不是选择题。

---

如果你做：

## LLM-as-a-Judge

真实问题是：

- Judge 会偏心吗
- 长答案会不会高分
- 顺序影响吗
- 高置信case稳定吗

于是：

benchmark 就会设计：

- swap answer order
- 不同长度
- disagreement analysis

---

所以：

## benchmark 的第一来源：

> 真实系统中的失败案例（failure cases）。

很多 benchmark 都是这么来的。

---

# 你想测“哪一种能力”

这是第二核心。

因为：

“模型能力”不是一个东西。

而是很多子能力。

例如：

<sheet sheet-id="xPhZkx" token="Dv63sGvUghzdAjtoJa8cFOhtnCf"></sheet>

---

很多 benchmark 失败原因：

就是：

> 测的能力不纯。

比如：

一个“数学benchmark”

结果其实在测：

- 阅读理解
- 长context
- prompt engineering

那就不可信。

---

# Ground Truth 从哪里来

这是非常关键的。

因为：

benchmark 必须知道：

> “什么是正确答案”。

否则没法评。

---

Ground truth 常见来源：

## 人工专家标注

例如：

- GPQA
- 医学benchmark

优点：

- 质量高

缺点：

- 贵
- 慢

---

## 程序可验证

例如：

HumanEval

直接：

```Python
assert func(x) == y
```

优点：

- 客观
- 自动化

这是现在最理想的 benchmark。

---

## 人类偏好投票

例如：

Chatbot Arena

ground truth：

> 用户更喜欢哪个回答。

---

## 历史真实数据

例如：

客服日志

真实bug修复

真实网页任务

---

# 模型实际会在哪些case翻车

这是现在 benchmark 非常强调的。

因为：

平均分没意义。

真正重要的是：

> failure mode。

例如：

LLM Judge：

可能：

- 偏爱长答案
- 对格式敏感
- 顺序敏感

那 benchmark 必须专门构造：

- adversarial cases
- swapped order cases
- verbosity perturbation

---

# 评测指标（metric）

同一个任务，

metric 不同，

结论完全不同。

例如：

代码任务：

<sheet sheet-id="OX1uwz" token="Dv63sGvUghzdAjtoJa8cFOhtnCf"></sheet>

---

Judge任务：

可能看：

- human agreement
- consistency
- variance
- calibration
- confidence

---

# Benchmark 是否容易被“刷”

这是现在大问题。

因为：

一旦 benchmark 出名：

模型可能：

- 训练见过
- prompt overfit
- 专门优化

导致：

> benchmark 分数 ≠ 真实能力。

---

所以现在很多 benchmark 会考虑：

## 动态更新

例如：

- LiveCodeBench
- Arena

---

# 七、你现在这个方向最关键的一点

你现在做的其实已经不是：

> “测模型能力”

而是：

# “测评测系统是否可信”

所以你更关注：

<sheet sheet-id="jj0F7r" token="Dv63sGvUghzdAjtoJa8cFOhtnCf"></sheet>

所以你的 benchmark / evaluation pipeline：

其实更接近：

## reliability benchmark

而不是传统：

## capability benchmark。

这个方向现在越来越重要。
