# Key Guide + Judge 自动化评测

## 摘要

Key Guide + Judge 自动化评测是一种两阶段质检方案：先基于 system prompt、工具 schema、FAQ、业务流程和当前上下文，为每个 assistant turn 生成局部 key guide；再让 judge 严格按 key guide 的 `[must]` / `[should]` 条款逐条打分并输出 evidence。

## 核心结构

### Key Guide

Key guide 应包含：

- `[content]`: 当前 turn 所处对话阶段、用户输入含义和 assistant 应执行的局部任务。
- `[must]`: 业务硬约束，例如固定话术、隐私边界、金额/日期、工具调用、必填参数、`<dialog-end>`。
- `[should]`: 体验性要求，例如礼貌专业、支持性语气、TTS 友好、简洁表达、积极倾听。

来源：`raw/reports/key_guide_results.docx`、`raw/reports/[0617] key guide & judge 自动化尝试.docx`

### Judge

Judge 输出应围绕 key guide 逐项检查：

- 当前上下文。
- Assistant response。
- Key guide。
- Must check。
- Should check。
- Score 与总评。

每条 must / should 需要给出 PASS 或 FAIL，并附 evidence。来源：`raw/reports/key_guide_judge_report.docx`

## 分数语义

- `1`: must 和 should 均满足。
- `0`: must 满足，但存在 should 层面的质量问题。
- `-1`: 存在 must 违反，属于业务关键要求失败。

来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`

## 可信度来源

- 规则来源可信：key guide 要求可回溯到 system prompt、step flow、FAQ、core rules、tool schema 或当前上下文。
- 上下文理解可信：key guide 能根据身份未确认、已确认但未 consent、已 consent、FAQ 插入、付款意图、已付款 claim 等状态调整约束。
- 评价粒度可信：judge 逐条检查 must / should，并为每项给出 evidence，便于人工抽检。
- 分数解释可信：score 1 / 0 / -1 与业务硬约束和体验软约束的层级一致。
- Badcase 定位可信：judge 能把问题定位到具体规则，例如积极倾听不足、参数抽取不完整、流程闭环缺失。

来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`

## 已验证样本

0617 验证中，2 条 BNPL pre-due reminder 对话共 12 个 assistant turn 完成评测，结果为 10 个 score 1、1 个 score 0、1 个 score -1、errors 0。人工复查评测错误数为 0。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`、`raw/reports/key_guide_judge_report.docx`

## 可复用结论

- 自动化 key guide 适合作为长 system prompt 到 turn-level eval rule 的中间层。
- `[must]` / `[should]` 分层有助于区分业务关键失败和体验质量不足。
- judge 必须受 key guide 约束，避免自行扩展规则或脱离上下文评价。
- evidence 输出是该方案能被人工审核和批量推广的关键条件。

## 风险与边界

- 当前样本量仍小，仅覆盖 2 条对话与 12 个 assistant turn。
- 进入规模化前，应补充 fraud halt、第三方接听、拒绝身份确认、静默、跑题、人工客服请求、财务困难、延期请求等边界场景。
- 批量运行早期应保留人工抽检，尤其关注 score 0 / -1、边界场景和 judge evidence 是否可复核。

## 相关页面

- [0617 Key Guide & Judge 自动化尝试](../evaluations/0617-key-guide-judge-automation.md)
- [Strict System Prompt Compliance Badcase](../badcases/strict-system-prompt-compliance.md)
- [Prompt Compliance Optimizer 工作流](../prompts/prompt-compliance-optimizer-workflow.md)

## 待确认

- 需要补充原始 JSONL、key guide prompt 和 markdown 版 key guide，才能验证 docx 摘要外的完整生成链路。
