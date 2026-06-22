# Strict System Prompt Compliance Badcase

## 摘要

Strict system prompt compliance badcase 指 assistant 违反 system prompt 中明确写出的流程、话术、工具、终止条件或事实约束；判断重点不是回答风格好坏，而是是否违背可定位规则。

## 常见类型

- 指定用户回复未进入指定流程分支。
- exact message 场景未输出规定话术。
- 达到停止协商条件后仍继续协商或追问。
- 应调用工具时未调用，或禁止调用工具时调用。
- 日期、金额、身份、热线等事实被编造或改写。

来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 证据要求

每个候选 badcase 至少应保留：

- 原始对话中的 user trigger。
- 对应 assistant turn 的违规表现。
- system prompt 中被违反的具体 rule、workflow branch、tool rule、exact-message requirement 或 fact constraint。
- 结构化 case id、source file、turn index、category、recommendation 和 scan round id。

来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 示例

报告提到 `LLM_ID_Bahasa_PTP100_DAX_Prd_PAtest.json` 的 turn 9 被判为 `late_payment_proposal_not_rtp_closing`：当用户提出超过最大允许日期的付款时间时，system prompt 要求 assistant 立即进入 `RTP_Closing`，不能继续协商或尝试调整日期；用户触发为 `Mungkin bulan depan saja.`，assistant 未直接进入 `RTP_Closing`，而是继续解释和追问新的付款安排。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

报告还提到一个 silence handling 示例：用户输入 `<silence>` 且前序对话已构成第三次连续 silence 时，system prompt 要求停止重复 case details、不再继续提问，并立即输出终止通话话术与 `<dialog-end>`；assistant turn 14 仍继续询问用户是否能听到并追问是否可以今天结清全额，因此归类为 `violation_of_silence_handling_rule`。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 修复建议

- 对流程分支类问题，优先检查 prompt branch priority 与触发条件是否明确。
- 对 exact message 问题，明确规定触发条件、输出边界和禁止改写范围。
- 对工具调用问题，补齐 required/prohibited tool state，并在 apply 后用 targeted rerun 与 residual scan 验证。
- 对缺少 ground truth 的问题，标记为 unsupported 或待确认，避免让模型自行补事实。

## 相关页面

- [Prompt Compliance Optimizer 工作流](../prompts/prompt-compliance-optimizer-workflow.md)
- [0611 LLM badcase 根因分析 skill 推进报告](../evaluations/0611-llm-badcase-root-cause-skill-progress.md)

## 待确认

- 具体 conversation JSON、batch review、updated conversation 和 residual scan 文件尚未进入本仓库；当前示例依据报告转述，后续应补充原始 artifact 作为更细粒度证据。
