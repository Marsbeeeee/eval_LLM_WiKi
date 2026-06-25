# Customer Service Judge Badcase 记录

## 摘要

`[0528] customer service 全量 pipline case 记录` 基于测试集 `626`，整理了 Customer Service judge 评测中的多类 badcase：system prompt 歧义、remark 不清晰、turn-aware 标准不确定、key guide 引用不必要 system prompt 内容、key guide 注意力过剩和模型注意力过剩。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

## 基本信息

- Source: `raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`
- Dataset: `626`
- Task: Customer Service judge / pipeline badcase analysis
- Category: judge reliability, key guide quality, prompt ambiguity, turn-aware evaluation
- Severity: Medium（按文档呈现，主要影响评测可信度和 badcase 归因，而非直接证明线上模型严重故障）

## Badcase 分类

### System Prompt 歧义

文档记录了多个 `I can't provide it` 相关 case。由于 system prompt 对该表达可有两种解释，导致评测标准本身存在歧义。例如 `dialogueId=19781`、`19782`、`19783` 的 Turn 6 被标记为 system prompt 歧义，其中 `19783` 还直接影响了 Turn 8、10、12 的回答判断。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

另一个 `dialogueId=19840` Turn 8 中，人工期望模型回答 FAQ 后回到主线，但 system prompt 未明确要求该动作，因此 key guide 将“跳回主线”作为 should 造成模型 2 分；文档判断这不应算模型能力 badcase。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

### Remark 不清晰

`dialogueId=19785` Turn 8 中，remark 没有明确标注应该返回主线，导致 key guide 认为不可以提及其他步骤，模型被评为 1 分。该问题更接近标注或 remark 质量问题，而不是模型能力问题。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

### Turn-Aware 标准不确定

`dialogueId=19841` Turn 16 中，模型将两步合并为一步，但人工可能认为不应合并。`dialogueId=19787` Turn 18 中，key guide 认为当前 turn 不应进入下一步，而人工认为可以。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

这类 case 说明 turn-aware 评测需要更明确地定义“何时允许推进流程”和“何时必须停留当前步骤”。

### Key Guide 引用不必要 System Prompt 内容

`dialogueId=19781` Turn 4 中，key guide 要求 response 必须询问用户提供注册手机号，同时还引用了 system prompt 中身份表达相关内容。文档将其归为 key guide 引用不必要 system prompt 内容。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

### Key Guide 注意力过剩

多个 case 显示 key guide 对语言表达过于苛刻，例如要求必须明确表达“没有手机号无法定位账户信息”或“没明确人数不能确定预定”，但 remark 或 system prompt 未必明确要求逐字表达。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

### 模型注意力过剩

文档也记录了 judge 或生成模型对 key guide 含义过度理解，导致评分不一致或给出过低分的情况。例如 `dialogueId=19789` Turn 8 被描述为 judge 模型过度理解 key guide 标准，`dialogueId=19792` Turn 22 被描述为 GPT 注意力过剩导致给出 1 分。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

## 影响范围

这些 badcase 会影响 Customer Service 自动化评测的可信度，尤其是：

- 把 prompt / remark / key guide 的歧义误判为模型能力问题。
- 让 judge 对 should 条款或表达方式过度苛刻。
- 在 turn-aware 多轮流程中错误判断是否应推进下一步。
- 在 pipeline 放量后扩大人工复核成本。

## 修复建议

- 在 key guide 生成前先区分 prompt 约束、remark 约束和人工主观期望。
- 对“返回主线”“是否推进下一步”“是否必须逐字表达”建立显式 turn-aware 判定规则。
- 将 system prompt 歧义和 remark 不清晰单独归类，不计入模型能力 badcase。
- 对 score 低但来源于 key guide 注意力过剩的 case，优先修正 key guide prompt 或标注规范，而不是直接惩罚被评模型。

## 相关页面

- [Feishu My Library Ingest 2026-06-22](../evaluations/feishu-my-library-ingest-20260622.md)
- [Judge Pipeline 演进与自动化评估记录](../evaluations/judge-pipeline-evolution.md)
- [Key Guide + Judge 自动化评测](../concepts/key-guide-judge-automation.md)
- [Strict System Prompt Compliance Badcase](strict-system-prompt-compliance.md)

## 待确认

- 文档中的图片附件尚未下载为本地文件，当前仅保留 Markdown 中的飞书图片链接。
- `Customer_service_all_data_LLM_Judge`、`custoemr_service_auto_remark_pipline_all` 等电子表格内容尚未抽取。

## 维护口径

- 对该页已有 case，优先按原文记录归因：文档标为 system prompt 歧义、remark 不清晰、key guide 注意力过剩或 turn-aware 标准不确定的，不默认记为模型能力 badcase。
- 后续只有当原文未明确说明归因，或不同文档对同一 case 给出冲突判断时，才放入待确认。
