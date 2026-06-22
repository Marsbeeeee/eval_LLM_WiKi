# Feishu My Library Ingest 2026-06-22

## 摘要

本次 ingest 将飞书 `云文档 -> 我的文档库` 下的 57 个节点归档到 `raw/feishu-my-library/2026-06-22/`，其中 44 个 Docx 文档已导出为 Markdown，13 个电子表格节点暂以元数据形式记录。来源：`raw/feishu-my-library/2026-06-22/tree.json`、`raw/feishu-my-library/2026-06-22/manifest.json`

这批材料主要覆盖四条知识线：benchmark 设计与跨语言 judge reliability、LLM-as-Judge pipeline 演进、Customer Service judge badcase、Badcase 根因分析 agent / skill。

## 原始归档

- 节点树：`raw/feishu-my-library/2026-06-22/tree.json`
- 可读树：`raw/feishu-my-library/2026-06-22/tree.txt`
- 抓取清单：`raw/feishu-my-library/2026-06-22/manifest.json`
- Docx Markdown：`raw/feishu-my-library/2026-06-22/docs/`

## 主题分组

### Benchmark 与跨语言评测

`Benchmark 基础知识` 把 benchmark 定义为由数据集、标准答案、评测规则和测试流程组成的能力抽象，并强调 benchmark 的第一来源是真实系统中的 failure cases。来源：`raw/feishu-my-library/2026-06-22/docs/Benchmark 基础知识.md`

`跨语言 Judge Consistency / Multilingual Evaluation Reliability` 将问题定位为模型作为 judge 时，同一语义在不同语言中是否能稳定理解和评分，属于 LLM-as-a-Judge、multilingual evaluator、cross-lingual evaluation consistency 方向。来源：`raw/feishu-my-library/2026-06-22/docs/Benchmark 基础知识_跨语言 Judge Consistency _ Multilingual Evaluation Reliability.md`

相关页面：[Benchmark 基础知识与跨语言评测](../concepts/benchmark-foundations.md)

### Judge Pipeline 演进

`[0507]Judge模型初步对比` 记录了 qwen 与 deepseek 作为 judge 的初步对比：整体分布接近、极高/极低分可用于自动过滤，但单一 judge 存在模型偏好，五分制难以区分高分段和低分段内部差异，长上下文场景下稳定性下降。来源：`raw/feishu-my-library/2026-06-22/docs/_0507_Judge模型初步对比（qwen 3.6 v.s. deepseek 3.2）- 副本.md`

后续文档标题显示该线索继续扩展到 mandiri pipeline、FC-Continuer、Customer Service、key guide & judge 自动化、Codex usage tracker 等方向。来源：`raw/feishu-my-library/2026-06-22/tree.json`

相关页面：[Judge Pipeline 演进与自动化评估记录](judge-pipeline-evolution.md)

### Customer Service Judge Badcase

`[0528] customer service 全量 pipline case 记录` 以测试集 `626` 为核心，整理了 system prompt 歧义、remark 不清晰、turn-aware 标准不确定、key guide 引用不必要 system prompt 内容、key guide 注意力过剩、模型注意力过剩等 badcase 类型。来源：`raw/feishu-my-library/2026-06-22/docs/_0528_ customer service 全量 pipline case 记录.md`

相关页面：[Customer Service Judge Badcase 记录](../badcases/customer-service-judge-badcases.md)

### Badcase Agent / Skill

`[0611]LLM badcase 根因分析 skill 推进报告` 已与本地已有页面形成重叠，核心仍是将 strict system prompt compliance badcase 的处理标准化为“评估 -> 分析 -> 实验 -> 结论”链路。来源：`raw/feishu-my-library/2026-06-22/docs/_0611_LLM badcase 根因分析 skill 推进报告.md`、`wiki/prompts/prompt-compliance-optimizer-workflow.md`

相关页面：[Prompt Compliance Optimizer 工作流](../prompts/prompt-compliance-optimizer-workflow.md)

## 可复用结论

- 本文档库可以作为 eval_LLM_WiKi 的二级来源索引：先按主题沉淀结论，再按需要回到 `raw/feishu-my-library/2026-06-22/docs/` 深挖单篇文档。
- Judge 自动化评估的核心矛盾集中在三类：规则来源是否明确、judge 是否过度解释 key guide、长上下文与跨语言是否保持评分一致。
- Customer Service 类 case 已经积累出一组可复用 badcase taxonomy，可并入后续 strict prompt compliance 与 key guide judge 的评测设计。

## 待确认

- 13 个电子表格节点目前只归档了元数据，尚未抽取单元格内容。
- Docx 中的飞书图片和附件链接尚未独立下载为本地证据文件。
- 标题中的 `0507`、`0528`、`0611` 等日期当前主要按文档标题推断，需后续用文档元数据或人工确认。
- `Daily Report`、`Laep 测试链接`、`知识问答` 等泛主题文档尚未深度归纳。
