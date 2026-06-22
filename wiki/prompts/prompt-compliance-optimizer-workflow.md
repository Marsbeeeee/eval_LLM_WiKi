# Prompt Compliance Optimizer 工作流

## 摘要

`prompt-compliance-optimizer` 将 strict system prompt compliance badcase 的处理拆成“评估 -> 分析 -> 实验 -> 结论”，目标是让候选识别、修复假设、prompt/turn 实验和最终判断都可追踪、可审核、可复盘。

## 适用场景

- 用户回复必须进入指定流程分支。
- 某类场景必须输出 exact message。
- 触发条件要求停止当前协商。
- 某类状态必须调用或禁止调用工具。
- 日期、金额、身份、热线等事实不能编造或改写。

来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 四节点链路

### 评估

评估节点读取 system prompt、conversation turns、user trigger、assistant response 和 tool definitions，输出 candidate badcase 的 case id、source file、turn index、badcase category、violated rule、用户触发证据、assistant 违规证据、recommendation 和 scan round id。

可信度来自四类证据：原始对话可核对、prompt rule 可定位、badcase 分类可解释、case id 与 scan round 可追踪。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

### 分析

分析节点基于 scan 结果判断违反的是流程分支、exact message、工具调用还是终止条件，并确认用户输入是否触发明确规则、assistant 是未识别触发条件还是识别后走错分支。

分析输出应形成可验证修复假设，包括 root cause、patch target 和 verification cases。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

### 实验

实验节点只处理人工批准的 badcase。批准方式可以是批准全部候选、指定 case id、指定文件或范围。

apply 后应保留 Repair Plan、prompt hash 变化、prompt edit summary、rerun target turns、backend/model/request id、updated conversation 和 post-apply residual scan。真正证明修复成功的不是执行过 prompt edit 或 rerun，而是目标 turn 的可见行为变化与 residual scan 清零。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

### 结论

conclusion 固定收束为 Verification Verdict、Badcase Diagnosis And Backend Evidence、Next Action。当前结构强调 root-cause-first：先解释规则触发、assistant 走错分支或漏掉动作、修复为什么有效或为什么失败，再展示 request id、prompt hash、logprob 等深层信息。

logprob 只能作为诊断辅助信号，不能替代 updated conversation 和 residual scan 对修复结果的判断。来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 判定口径

- `verified fixed`: approved badcase 经 updated conversation 和 residual scan 验证通过。
- `partially fixed`: 部分 badcase 通过，部分仍有 residual。
- `not fixed` 或 `verification failure`: apply 后 residual scan 仍命中关键 rule violation。
- `unsupported case`: 缺少 ground truth、工具定义或业务事实，无法安全修复。

来源：`raw/reports/[0611]LLM badcase 根因分析 skill 推进报告.docx`

## 复用结论

- scan 阶段的目标是筛出可审核、可追踪候选，不直接证明已经修复。
- analysis 阶段的价值是把“回答错了”转为“规则触发 -> assistant 行为 -> violation 分类 -> 修复假设”。
- apply 阶段必须区分“执行了修复动作”和“修复被验证成功”。
- conclusion 应同时保留表层证据和深层 evidence，但最终修复判断以 updated conversation 与 residual scan 为准。

## 相关页面

- [0611 LLM badcase 根因分析 skill 推进报告](../evaluations/0611-llm-badcase-root-cause-skill-progress.md)
- [Strict System Prompt Compliance Badcase](../badcases/strict-system-prompt-compliance.md)

## 待确认

- 需要后续补充具体 batch review、updated conversation、residual scan 和 conclusion artifact，才能把本页方法论进一步映射到单个 case 的证据链。
