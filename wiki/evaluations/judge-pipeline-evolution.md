# Judge Pipeline 演进与自动化评估记录

## 摘要

飞书文档库中的 judge pipeline 资料显示，评估工作从单轮模型 judge 对比，逐步扩展到 pipeline 评测、长上下文稳定性、key guide + judge 自动化、Customer Service badcase 分类和 Codex 辅助分析。来源：`raw/feishu-my-library/2026-06-22/tree.json`

## 0507 Judge 模型初步对比

`[0507]Judge模型初步对比` 对 qwen 与 deepseek 作为 judge 的能力做了初步观察：

- 自动化评估整体一致性较高，qwen 与 deepseek 在多数 case 上评分分布接近。
- 高分段和低分段样本具备自动过滤价值，可以减少人工初筛压力。
- 五分制难以区分高分段与低分段内部差异，因此提出三分制方向。
- 单一 judge 存在偏好差异：deepseek 更严格按 prompt/context 字面匹配，qwen 更倾向结合真实场景和语义合理性。
- 长上下文后段评分稳定性下降，可能来自回答模型注意力漂移，也可能来自评估模型上下文遗漏或语义不稳定。
- 工程型字段错误不应只交给语义 judge，例如字段名拼写错误可能语义可理解，但实际链路不可用。

来源：`raw/feishu-my-library/2026-06-22/docs/_0507_Judge模型初步对比（qwen 3.6 v.s. deepseek 3.2）- 副本.md`

## Pipeline 主题扩展

文档树显示，0507 之后该线索继续扩展到：

- mandiri pipeline 多模型对比。
- FC-Continuer 衔接词评估。
- Codex 拆解 fc-continuer 并评估工作流。
- Customer Service judge 模型 badcase 分析。
- key guide & judge 自动化尝试。
- Codex usage tracker 与任务归因。

来源：`raw/feishu-my-library/2026-06-22/tree.json`、`raw/feishu-my-library/2026-06-22/manifest.json`

## 与 Key Guide + Judge 的关系

已有 Wiki 页面记录了 0617 自动化尝试：先基于 system prompt、工具 schema、FAQ、业务流程和上下文生成 turn-level key guide，再由 judge 按 `[must]` / `[should]` 条款逐项打分并输出 evidence。来源：`wiki/concepts/key-guide-judge-automation.md`

0507 的单 judge 偏好问题说明，仅比较最终分数不足以支撑规模化评测；0617 的 key guide + judge 方案则把评价依据显式拆为可审查条款，能降低 judge 自由发挥空间。来源：`raw/feishu-my-library/2026-06-22/docs/_0507_Judge模型初步对比（qwen 3.6 v.s. deepseek 3.2）- 副本.md`、`wiki/concepts/key-guide-judge-automation.md`

## 可复用结论

- 单一 judge 输出可以用于初筛，但重要结论应结合人工复核、多模型 judge 或可追溯 key guide evidence。
- 长上下文评测需要单独监控后段 turn 的评分漂移，不能只看整体均值。
- 字段名、工具 schema、函数参数等工程约束应使用结构化校验或 schema check，不宜完全依赖语义 judge。
- 五分制如果无法稳定区分细粒度质量，可考虑压缩为三分制或 must/should/violation 分层。

## 相关页面

- [Feishu My Library Ingest 2026-06-22](feishu-my-library-ingest-20260622.md)
- [Benchmark 基础知识与跨语言评测](../concepts/benchmark-foundations.md)
- [Key Guide + Judge 自动化评测](../concepts/key-guide-judge-automation.md)
- [Customer Service Judge Badcase 记录](../badcases/customer-service-judge-badcases.md)

## 待确认

- 0509、0511、0513 等 pipeline 文档尚未逐篇抽取指标与实验结论。
- 电子表格节点当前只有元数据，尚未读取实际评分表、diff 表和模型指标表。
- 三分制的最终采用情况需要后续从更晚期文档确认；若文档未记录，则保持待确认。
