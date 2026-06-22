# Benchmark 基础知识与跨语言评测

## 摘要

Benchmark 不是单纯题库，而是对真实产品能力的抽象：从现实问题或产品需求出发，定义要测的能力、成功标准、任务数据、评分方式和可靠性验证流程。来源：`raw/feishu-my-library/2026-06-22/docs/Benchmark 基础知识.md`

## Benchmark 组成

一套完整 benchmark 通常包含：

- 数据集或任务。
- 标准答案。
- 评测规则。
- 测试流程。

设计逻辑是：现实问题 / 产品需求 -> 定义能力 -> 定义成功标准 -> 设计任务与数据 -> 定义评分方式 -> 验证 benchmark 是否可靠。来源：`raw/feishu-my-library/2026-06-22/docs/Benchmark 基础知识.md`

## Failure Cases 是核心来源

`Benchmark 基础知识` 明确强调，benchmark 的第一来源是真实系统中的 failure cases。对于 AI coding agent，真实问题会包含长代码库、依赖、测试失败和模糊 issue；对于 LLM-as-a-Judge，则会关注 judge 偏好、长度偏差、顺序影响和高置信 case 稳定性。来源：`raw/feishu-my-library/2026-06-22/docs/Benchmark 基础知识.md`

## 跨语言 Judge Consistency

跨语言 judge consistency 测的是模型作为 judge 时，同一语义换成不同语言后是否还能稳定评分。该方向更接近 LLM-as-a-Judge、multilingual evaluator、cross-lingual evaluation consistency，而不是普通 QA accuracy。来源：`raw/feishu-my-library/2026-06-22/docs/Benchmark 基础知识_跨语言 Judge Consistency _ Multilingual Evaluation Reliability.md`

典型问题包括：

- 语言资源不均衡导致低资源语言理解细节、语气或隐含含义时更不稳定。
- 文化表达差异导致相同意图在不同语言中被 judge 赋予不同严重程度。
- 同一语义在英文、中文、印尼语等语言下出现评分漂移时，应归类为 scoring consistency 问题。

来源：`raw/feishu-my-library/2026-06-22/docs/Benchmark 基础知识_跨语言 Judge Consistency _ Multilingual Evaluation Reliability.md`

## 可复用结论

- Eval benchmark 应从真实 failure cases 反推任务，而不是先设计抽象题型。
- LLM-as-a-Judge benchmark 需要显式覆盖 judge 偏好、顺序影响、答案长度、长上下文稳定性和跨语言一致性。
- 跨语言 judge reliability 的评价对象不是模型是否回答正确，而是 judge 在语义等价输入下的评分一致性。

## 相关页面

- [Feishu My Library Ingest 2026-06-22](../evaluations/feishu-my-library-ingest-20260622.md)
- [Judge Pipeline 演进与自动化评估记录](../evaluations/judge-pipeline-evolution.md)
- [Key Guide + Judge 自动化评测](key-guide-judge-automation.md)

## 待确认

- `可借鉴benchmark信息收集`、`XNLI benchmark`、`XQuAD benchmark` 和 `XNLI和XQuAD真实例子` 尚未逐段归纳。
- 需要后续补充具体跨语言样本、评分表和一致性指标，才能形成可执行 benchmark spec。
