# 0611 LLM badcase 根因分析 skill 推进报告

## 摘要

该报告记录了 `prompt-compliance-optimizer` skill 从评估、分析、实验到结论的闭环设计，以及当前已跑通批量扫描、人工审核、prompt edit、targeted rerun、residual scan 和 conclusion 生成的工程状态。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 基本信息

- Source: `raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`
- Date: 2026-06-11（由文件标题 `[0611]` 推断，待确认）
- Task: system prompt compliance badcase 分析与修复 workflow 推进
- Related skill: `prompt-compliance-optimizer`
- Category: prompt compliance / badcase root cause analysis / workflow validation

## 关键事实

- skill 目标是把 system prompt compliance badcase 的处理链路标准化，覆盖客服类 LLM 测试中的流程分支、exact message、停止协商、工具调用、日期金额身份热线等事实约束。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`
- 当前 workflow 拆为四个节点：评估、分析、实验、结论，每个节点保留自己的证据来源。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`
- 已完成能力包括批量扫描 JSON conversation、识别 strict system-prompt-compliance 候选 badcase、生成 review 文件、保留 human review gate、批准后执行 prompt edit 与 targeted rerun、生成 updated conversation、执行 residual scan、生成 root-cause-first conclusion。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`
- 已验证工程状态包括批量扫描/修复流程跑通、conclusion 格式改为 root-cause-first、输出目录清理和稳定文件策略完成、Python compile check 和 skill validate 通过。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`
- 最新测试中，5 个 approved badcase 有 4 个 verified fixed，1 个 residual case 被定位为 branch-priority / routing failure。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 链路结论

该 skill 的核心价值是把 badcase 修复从“人工主观判断”推进为“基于规则、实验和证据链的闭环判断”。其中，scan 负责筛出可审核候选，analysis 形成可验证修复假设，apply 验证修复动作是否影响目标 turn，conclusion 通过 residual scan 与表层/深层证据给出下一步动作。

可复用方法论已沉淀到 [Prompt Compliance Optimizer 工作流](../prompts/prompt-compliance-optimizer-workflow.md)。

## 相关页面

- [Prompt Compliance Optimizer 工作流](../prompts/prompt-compliance-optimizer-workflow.md)
- [Strict System Prompt Compliance Badcase](../badcases/strict-system-prompt-compliance.md)

## 待确认

- 文件标题中的 `0611` 是否就是报告日期 `2026-06-11`。
- 报告中提到的具体 scan/review/apply/conclusion artifact 路径未随本材料提供，后续若补充原始 JSON 或输出文件，应继续 ingest 并互链。
