<title>XNLI benchmark</title>

XNLI 本质上测的是：

> 模型是否能在“不同语言下保持一致的语义推理能力”。

它不是测：

- 翻译质量
- 对话生成
- 写作能力

而是测：

# “跨语言语义理解”

这和你们现在的 Judge consistency 问题其实非常接近。

---

# 一、XNLI 的核心任务是什么

XNLI 基于：

# NLI（Natural Language Inference）

自然语言推理。

---

它给模型两个句子：

---

## Premise（前提）

例如：

> A man is playing guitar on stage.

---

## Hypothesis（假设）

例如：

> A person is performing music.

---

然后模型要判断：

---

## 三分类

### Entailment（蕴含）

Hypothesis 能从 premise 推出来。

---

### Contradiction（矛盾）

Hypothesis 和 premise 冲突。

---

### Neutral（中立）

无法确定。

---

# 二、XNLI 为什么叫 “Cross-lingual”

因为：

# “同一个语义内容”

会被翻译成：

- 中文
- 法语
- 西语
- 印尼语
- 阿拉伯语
- etc

然后观察：

> 模型换语言后，  
> 推理能力是否下降。

---

# 三、XNLI 真正测的是什么

不是：

“会不会说这门语言”

而是：

# “语义关系判断是否稳定”

这是重点。

---

例如：

英文：

Premise:

> The boy is sleeping.

Hypothesis:

> The child is awake.

模型应该输出：

> contradiction

---

换成中文：

前提：

> 男孩正在睡觉。

假设：

> 那个孩子醒着。

理论上：

也必须还是：

> contradiction

---

如果：

英文判断正确，

但中文错了，

说明：

> 跨语言语义理解能力不稳定。

---

# 四、XNLI 的 benchmark 结构

它的数据结构其实非常简单。

---

## 输入

```JSON
{
  "premise": "...",
  "hypothesis": "...",
  "label": "entailment"
}
```

---

## 输出

模型预测：

- entailment
- contradiction
- neutral

---

## 最终指标

通常：

# Accuracy

即：

预测对了多少。

---

# 五、XNLI 最核心的设计思想（这个对你们很重要）

XNLI 的核心不是：

“多语言”

而是：

# “同一语义的跨语言稳定性”

这个思想和你们非常像。

---

你们现在的问题：

其实可以类比成：

---

## XNLI

同一语义：

换语言后：

是否还能正确判断 semantic relation。

---

## 你们

同一 conversation：

换语言后：

Judge 是否还能：

- 正确评分
- 正确识别 violation
- 正确理解 conversation state

---

# 六、XNLI 最值得你们借鉴的地方

我觉得有三个。

---

# Parallel multilingual data

即：

同一个 case：

多语言平行版本。

这是 XNLI 核心。

---

# Semantic equivalence

XNLI 很强调：

> 翻译后语义必须严格一致。

因为：

如果翻译不一致：

benchmark 会失效。

---

这对你们非常重要。

因为：

你们 Judge benchmark 最怕：

- 中文是强拒绝
- 西语变成弱拒绝

那 Judge score 就无法比较。

---

# Cross-language consistency analysis

XNLI 不只是看 overall accuracy。

还会看：

- 哪些语言掉点严重
- 哪些语言 transfer 差
- 低资源语言表现

---

你们也可以类似：

---

## 观察：

### 英文 Judge：

score稳定。

---

### 印尼语：

rule detection下降。

---

### 西语：

remark更模糊。

---

### 长上下文：

低资源语言更容易崩。

---

# 七、XNLI 和你们的区别（很关键）

XNLI：

是：

# “单步语义判断”

而你们：

更复杂。

因为你们涉及：

---

## 长上下文

multi-turn conversation。

---

## Rule grounding

MUST / SHOULD。

---

## Soft reasoning

不是三分类。

而是：

- score
- explanation
- policy interpretation

---

## Judge robustness

你们测的是 evaluator。

不是 answerer。

---

# 八、所以你们其实像：

# “XNLI + JudgeBench + MT-Bench”

的结合体。

---

# 九、如果你们后面真的做 benchmark

XNLI 最值得模仿的其实是：

# “同一case跨语言严格对齐”

这个 methodology。

---

例如：

你们完全可以做：

```JSON
{
  "case_id": 102,
  "language": "zh",
  "conversation": "...",
  "rubric": "...",
  "human_score": 4
}
```

然后：

```JSON
{
  "case_id": 102,
  "language": "es",
  "conversation": "...",
  "rubric": "...",
  "human_score": 4
}
```

重点：

# human intent 必须一致。

这样：

你们才能真正测：

> Judge 的跨语言稳定性。
