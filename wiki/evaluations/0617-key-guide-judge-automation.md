# 0617 Key Guide & Judge 自动化尝试

## 摘要

本轮验证基于 `test_qc_en.jsonl` 中 2 条 BNPL pre-due reminder 对话、共 12 个 assistant turn，先自动生成 turn-level key guide，再用 judge 按 key guide 检查 assistant response。人工复查认为自动化 key guide 整体可靠，judge 结果具备可信度，可作为后续规模化质检与 badcase 定位的基础方案。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`

## 基本信息

- Source: `raw/reports/[0617] key guide & judge 自动化尝试.docx`
- Supporting source: `raw/reports/key_guide_results.docx`
- Supporting source: `raw/reports/key_guide_judge_report.docx`
- Date: 2026-06-17（由文件标题 `[0617]` 推断，待确认）
- Input: `/mnt/zfs02/sullivan.guo/data/0615/test_qc_en.jsonl`
- Key Guide: `/mnt/zfs02/sullivan.guo/data/0615/key_guide_results_unescaped.md`
- Prompt: `/mnt/zfs02/sullivan.guo/workspace/local_qc/key_guide_prompt.txt`
- Model / Judge model: `voyager-1.6-gemma4-26b-a4b-it`
- URL: `http://192.168.101.15:9898`

## 测试范围

- 被评测 assistant turn: 12 / 12。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`
- 人工复查评测错误数: 0。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`
- Judge 报告耗时: 16.3s。来源：`raw/reports/key_guide_judge_report.docx`
- Key guide 生成耗时: 81.6s。来源：`raw/reports/key_guide_results.docx`
- 覆盖场景：
  - `id_bnpl_m0_L1_natural_reminder-leaf-t12-b0`: 用户自然确认身份、询问 GoPay 到账时间，并承诺到期日前支付。
  - `id_bnpl_m0_L2_alfa_payment_delay-leaf-t12-b0`: 用户表示已通过 Alfamart 现金支付，要求记录付款 claim。

## 量化结果

- 总评测 turn 数: 12。来源：`raw/reports/key_guide_judge_report.docx`
- Score 1: 10。来源：`raw/reports/key_guide_judge_report.docx`
- Score 0: 1。来源：`raw/reports/key_guide_judge_report.docx`
- Score -1: 1。来源：`raw/reports/key_guide_judge_report.docx`
- Errors: 0。来源：`raw/reports/key_guide_judge_report.docx`
- 满分率: 83.3%。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`
- 可解释非满分率: 100%。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`

## 关键事实

- 自动化 key guide 能根据 system prompt、业务流程、工具定义、上下文状态和当前用户回复生成 turn-level 局部评测标准，人工复查未发现脱离 prompt 或上下文的要求。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`
- key guide 能按对话阶段调整检查重点：身份确认阶段检查隐私保护和固定开场，账单提醒阶段检查金额、日期和付款计划，已付款场景检查 claim 信息和工具调用。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`
- key guide 将业务硬约束、流程闭环、工具调用、隐私边界写入 `[must]`，将语气、简洁度、TTS 适配、积极倾听等体验要求写入 `[should]`。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`
- judge 的打分与解释围绕 key guide 中的 `[must]` / `[should]` 条款展开，人工复查未发现 judge 自行引入不存在规则、臆造上下文或脱离 key guide 评价。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`
- judge 对 10 个合规 turn 给出 score 1，对 2 个非满分 turn 给出了可人工复核的失败证据。来源：`raw/reports/key_guide_judge_report.docx`

## 非满分 Case

### turn 8 | Score 0

在 `id_bnpl_m0_L2_alfa_payment_delay-leaf-t12-b0` 的 turn 8，用户已说明通过 Alfamart 支付，但 assistant 仍询问使用了哪个支付渠道。judge 判断 must 满足，因为 assistant 启动了 Step 5 Already Paid 流程并询问支付渠道、时间和 receipt reference；但 should 失败，因为没有回应用户已提到的 Alfamart 信息，属于积极倾听不足。来源：`raw/reports/key_guide_judge_report.docx`

### turn 10 | Score -1

在同一对话的 turn 10，assistant 调用了 `record_payment_claim`，但缺少上下文中的 `amount: Rp285.000`，且只输出工具调用，没有按 Step 5.3 给用户结束确认语。judge 将其判为 must fail，属于参数抽取不完整与流程闭环缺失。来源：`raw/reports/[0617] key guide & judge 自动化尝试.docx`、`raw/reports/key_guide_judge_report.docx`

## 结论

本轮结果支持一个可复用判断：自动化 key guide 可以把长 system prompt 转化为每个 assistant turn 的局部评价标准；judge 基于该标准逐条检查 must/should 并给出 evidence，有助于降低人工写 eval rule 的成本，并把 badcase 定位到具体规则、参数或流程闭环。

方法论已沉淀到 [Key Guide + Judge 自动化评测](../concepts/key-guide-judge-automation.md)。

## 后续建议

- 扩大样本覆盖到 fraud halt、第三方接听、拒绝身份确认、静默、跑题、人工客服请求、财务困难、延期请求、partial payment、cannot talk now 等边界场景。
- 批量运行初期保留人工抽检，重点检查 score 0 / -1 和边界场景。
- 持续保留 must / should check evidence，方便审计、定位和回归。

## 相关页面

- [Key Guide + Judge 自动化评测](../concepts/key-guide-judge-automation.md)
- [Strict System Prompt Compliance Badcase](../badcases/strict-system-prompt-compliance.md)
- [Prompt Compliance Optimizer 工作流](../prompts/prompt-compliance-optimizer-workflow.md)

## 待确认

- 文件标题中的 `0617` 是否就是报告日期 `2026-06-17`。
- 本仓库只归档了 docx 报告，尚未归档原始 `test_qc_en.jsonl`、`key_guide_results_unescaped.md` 和 `key_guide_prompt.txt`。
