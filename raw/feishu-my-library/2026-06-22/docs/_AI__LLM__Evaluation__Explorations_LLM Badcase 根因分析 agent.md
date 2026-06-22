# [AI][LLM][Evaluation][Explorations]LLM Badcase 根因分析 agent

## 一、为什么做？

我们评估一部分工作是对于新 feature 的探索和挖掘，那在日常评估中会遇到很多 badcase，包括在最新测试集 customer service 中遇到很多 system prompt 有模糊和歧义的点，会导致 bot 无法按照 system prompt 内容，正确应对 user 给出的回答。

那我们评估就需要对于遇到的 badcase 其深层原因进行挖掘，但是这一任务要求评估人员：

1. 熟悉测试集内容 

2. 能正确分辨 badcase 

3. 能根据 badcase 提出合理的 system 改进方案 

4. 根据改进方案进行多轮验证实验 

5. 根据实验结果得出结论

其工作量和对于评估人员本身能力要求是很大的考验，且后续考虑将自研模型进行放量，其中测试过程会遇到大量的 badcase，此时需要大量的人工注入去进行 badcase 的深挖，但此动作无法保证每个评估人员质量的对齐，且如此量级的工作也需要大量的时间投入难以保证效率，所以**考虑使用 agent 来代替此链路工作**



## 二、制作思路

<whiteboard token="EeQSwe7i8hutpubyZ4gc1boan7f"></whiteboard>

将 badcase 处理流程拆分为 “评估 -> 分析 -> 实验 -> 结论” 四个阶段，每个阶段聚焦单一任务：限定为问题，再判断根因，在执行 prompt 修改，最后验证修复效果。这样可以降低单次决策的复杂度，减少模型在同一步骤中同时完成识别、推理、改写带来的不稳定性

节点之间通过结构化上下文透传进行协作，只传递当前阶段产出的关键信息，后续节点不需要重新理解完整对话，而是基于前置节点的结构化内容继续推进，从而减少无关上下文的干扰，也提升链路整体的稳定性和一致性

---

### 2.1 评估

![图片展示的是Prompt Optimizer Agent界面，左侧为文件浏览区域，显示“test_customer”文件，可选择“Load sample”或“Raw JSON”。右侧是对话评估区域，呈现用户和助手的对话内容，每轮对话后有“Bad case”选项。界面中部有“system prompt”“Role Definition”“Working system prompt”“Rules to Follow”等输入框，以及“Prompt Versions”“Undo last version”“v0 Original”“download json”“generate badcase”按钮。该图与上下文“评估”部分相关，用于展示自动评估对话以判断每轮回复是否遵循system prompt的流程。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZDM3NjEyNzJlOTBlYTdkNmEwZGEzNmYwZGM0NTdiZDNfZTA2NmUwODkzN2I0NGZmNGI1Yjk4ODQyMzhiM2NmYzJfSUQ6NzY0NzQxMjk4MDU0MDA2Njc5Ml8xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

**核心动作**：首先由 agent 对上传的对话进行自动评估，判断每一轮 assistant 回复是否严格遵循当前 system prompt 。这里的评估标准不是泛泛地判断回复质量好不好，而是重点检查 assistant 是否违反了 prompt 中明确规定的流程步骤、分支条件、工具调用规则、事实约束等内容

**原因**：日常人工评估中，badcase 的判断容易受到评估人员经验差异影响。通过 agent 先进行统一标准的 compliance 检测，可以减少主观判断带来的不一致

**收益**：能够快速从大量对话中筛出真正值得分析的 badcase，保证在不同样本、不同评估人员操作下，保证初步的筛选是以统一标准进行的，避免不同评估人员对于 badcase 理解不统一的情况

---

### 2.2 分析

![图片展示了Prompt Optimizer Agent界面中badcase分析及其依据的内容。画面中显示了用户与助手的对话，助手在第11轮回复时被标记为bad case，其分析指出违反了规则2.2，即 addCriterionmatchCondition](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTM0YmQ3ZDIyYzcxYjUyYzU2MzBlYjI0Yjc0YjkyMjdfOTE3YWRkZDc1ODk5Yzk5MzMwZjBhMTBjMjRiNDQ1MDNfSUQ6NzY0NzQxNDcwNTg3NTc1MDEyMF8xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

![图片展示的是一个LMAI Evaluation Explorations LLM Badcase根因分析agent中“2.3实验”部分的实验界面。左侧为文件浏览区域，有“Browse files”按钮及右侧上方是“Trace List”，列出对话轮次、违反规则、证据及推荐等内容，如Turn 11违反规则2.2，证据是assistant未尝试说服Julia参加活动，推荐是assistant应尝试说服Julia参加活动。下方是对话示例，显示用户与assistant的对话记录。图片与上下文关系紧密，直观呈现了实验中badcase分析及system prompt修改建议的展示情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTU0NzY3NjE0YzA2NjM1NDI2OTM1NmQ0ZmQ3ZDhmZjVfYmU3ZmM1ZjkxMjEwYjc0OTk0MDcxNjg1N2M1YmYyYWNfSUQ6NzY0NzQxNTAzNjQ3MDA1Mzg2M18xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

**核心动作**：检测到 badcase 后，agent 会进一步分析该 badcase 出现的原因，包括：是哪一轮 assistant 回复出错、违反了 system prompt 的哪条规则、用户当前回复属于明确回答、模糊回答、拒绝、不确定还是答非所问，以及 system prompt 是否缺少对应处理分支

**原因**：仅仅知道 “这轮是 badcase” 是不够的，我们真正需要的是定位 prompt 为什么无法约束模型行为，或者模型为什么没有按照 prompt 执行，且根据当前对于 badcase 的分析，有什么针对 badcase 的更改建议

**收益**：分析结果可以直接转化成可执行的修改建议，首先降低了人工深挖 badcase 的成本，其次由于模型可以接收到的信息不仅仅是停留在表面的文本信息，它可以接触到其数据背后更深层的 meta 信息等等，避免只停留在表面的信息判断

---

### 2.3 实验

![图片展示的是一个对话系统界面，左侧为文件浏览区域，右侧是对话内容展示 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NjMyYjc2MGU1MzliYjJhZTFiMjg4ZTAwZWQyMzE4ZTBfMGQ2OTViYWYzZWRmNTEyYWJhMjk4NGRmM2IyYjExODFfSUQ6NzY0NzQxNzQ0NDE1NTkzNTk4NV8xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

![图片展示的是Prompt Optimizer Agent界面，呈现新prompt与前一版本prompt的差异。左侧有文件浏览、模型选择等选项，右侧上方显示“新prompt与前一版本prompt的diff”，下方有“v0 Original”和“v1.Apply”版本对比，突出显示v1.Apply的差异内容。下方还有“download json”和“generate badcase”按钮，以及“Trace List”区域。该图与上下文关系紧密，直观呈现了在获得badcase分析后，agent根据badevidence和recommendation修改当前system prompt的操作场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTU0MDhmYmM2NTIzOThkODk3MDJkYzc2NDQ4ZGZlYzBfMmYwNzhjYTJhOWI4MGQxMWEyMzJiYmYwNDhhZGM5ZTNfSUQ6NzY0NzQxNzg0NjYwNzQ4MjA4NF8xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

![图片展示了Prompt Optimizer Agent界面中改 addCriterion>](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTAzM2VlNWQyYTM4Mjg4NGM3NjkzYjVmZWI3YTI0NWNfZDgwZjMxOTU3ODBmMTEyMDFiZmVmMTdlYTgzNDdiODNfSUQ6NzY0NzQxODI1MjY1ODU3NjMzN18xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

**核心动作**：在获得 badcase 分析后，agent 会根据 badcase 的 evidence 和 recommendation 修改当前 system prompt。修改成功后，不会重跑整段对话，而是使用新的 system prompt 和当前上下文，只重新生成 badcase 对应的 assistant turn。生成后形成 Updated Conversation，并保留 prompt version、diff、rerun 记录和请求日志

**原因**：我们希望验证 “这次 prompt 修改是否真的解决了该 badcase”，因此需要一个可控实验环境，即除了 badcase 那那一轮对话会被修改，其余上下文均保持不变。如果每次都重跑整段对话，成本高、变量多，也不容易判断是哪次修改产生了效果

**收益**：实验过程更快、更可追踪，也能明确看到 prompt 修改前后模型行为的变化。通过 hash 校验和日志，还可以确认请求模型时使用的确实是修改后的 system prompt

---

### 2.4 结论

![图片展示了一个LLM Badcase根因分析agent的操作界面。左侧是“System Prompt”部分，呈现原始和更新的系统提示内容。右侧“Updated Conversation”区域显示修改后的对话内容，其中“Step 2”部分被亮色突出，写着“Highlight benefits (e.g., exclusive treatments, special discount) and affirm the value as a loyal customer to encourage attendance”等文字。箭头指向该突出部分。结合上下文，此图展示了agent在Apply完成后生成的conclusion，基于相关结果概括本轮badcase问题根因、对system prompt的具体修复及目标回复改善情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTc4MmE1Y2U1YzViZWJlYzQ2Y2QwYmRjYzUwNmRlNzRfZDM4MzIwYTFmY2Y1ZjA1MjZmMDRmY2U0ODdmNDgxOWJfSUQ6NzY0NzQ4NzEzMDY0MTU4MzI5OF8xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

**核心动作：** 每次 Apply 完成后，agent 会自动生成 conclusion，基于本轮 trace、prompt diff、targeted rerun 结果和可用诊断信息，概括本轮 badcase 的问题根因（如：meta 信息、token log prob）、本次对 system prompt 做了哪些具体修复、重新生成后的目标回复是否改善

**原因：** badcase 修复不是一次性的 prompt 改写，而是“发现问题 → 修改 prompt → 定向重跑 → 验证效果 → 再扫描”的闭环过程。单次 rerun 只能验证当前 badcase 对应轮次是否改善，不能天然保证整段 updated conversation 已经没有新的 badcase 产生，因此需要 conclusion 明确记录本轮修复效果，并结合下一步优化提示考虑是否进入下一轮扫描

**收益：** 每轮实验都会沉淀清晰结论，包括修改内容、改善证据和下一步建议，方便评估人员复盘，也能避免只看 prompt 和单轮回复表象做判断。对于大规模测试集，这种机制可以辅助人工去进行评估决策分析，且避免了人工智能看到表象的弊端



## 三、模型可信度验证

**模型缺陷：**  

在 prompt 优化链路中，模型可能存在**“表面执行但实际未生效**”的风险。

![图片展示了模型缺陷中prompt addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MmRhMTQ1MzkwMzhiMDdjZDViZWY0Yjg2NjRmMWUxY2ZfM2U5YjgyOWZlMmZhNmU1NjY2MzNhYmNkYzRlNjcyOGNfSUQ6NzY0NzQ3OTgyMzk1MDM1MTM0OF8xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

**验证方式：**  

为避免这类问题，我们在关键请求链路中加入了**日志和一致性校验**。每次 Apply 后，系统会记录本轮期望使用的 prompt hash，并在重新生成对话之前写入 preflight log，校验即将发送给模型的 system prompt 是否和当前 Working system prompt 一致；如果 hash 不匹配，请求会被阻断，不会发送到模型。

同时，本地会记录完整的请求审计日志，例如 “*company_api_preflight_system_prompt.log” 、“company_api_system_prompts.log” 、“company_api_requests.jsonl” 和“company_api_conclusion_dialog.jsonl”* 。

**这些日志可以用来确认：**prompt edit 是否真的产生了 diff，rerun 是否真的携带了修改后的 prompt，judge 是否使用了最新版 system prompt，conclusion 是否基于真实的 rerun 结果、诊断信息和 residual badcase 扫描结果生成。



## 四、总结

![图片展示了自动检测badcase修改system prompt Agent的优势与劣势。优势方面，有标准化流程降低经验依赖、自动承担重复工作提升人效、建立优化闭环加速模型迭代等；劣势方面，复杂业务场景仍依赖人工、分析结果受模型能力限制、无法识别评估标准缺陷。图片与上下文紧密相关，直观呈现了Agent在badcase处理中的优势和局限，为总结部分提供可视化支撑。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTFjOWZiZDg3MjdiNDdlMzIxZTEzZThhZDc2MTVmN2NfMGQ1NmQ5Y2JlMjU1ZjlhMTlkNGJmZWVkZGJlZWQ4NWNfSUQ6NzY0NzQ3OTcxOTA2ODE1OTE5NF8xNzgyMTIyNzc0OjE3ODIxMjYzNzRfVjM)

#### **优势**

1. **标准化评估流程，降低经验依赖，**将原本依赖个人经验的 badcase 挖掘流程标准化，通过统一的分析框架和处理链路，降低评估人员之间的认知差异，提升评估结果的一致性和稳定性
2. **自动承担重复性工作，提升人效**，Agent 并非用于替代人工决策，而是承担 badcase 筛选、原因分析、Prompt 修改建议、实验验证和结果初步总结等重复性工作，使评估人员能够将更多精力投入到复杂问题分析和最终决策中
3. **建立可持续优化闭环，加速模型迭代，**将 badcase 发现、分析、修复、验证和结论沉淀串联成完整闭环，使每次优化都可追踪、可复盘、可复用，持续积累 Prompt 优化经验，为后续模型能力迭代提供支撑

#### 劣势

1. **复杂业务场景仍依赖人工判断，**当前 Agent 更擅长处理规则违反、流程遵循等相对确定性的问题，但对于业务策略判断、用户真实意图理解以及存在多种合理答案的复杂场景，仍然难以完全替代人工决策
2. **分析结果受模型能力上限约束，**Agent 的分析质量依赖于底层模型能力。当面对复杂上下文或深层信息时，可能无法准确识别问题根因，从而影响后续原因分析、Prompt 修改建议及实验结论的准确性
3. **无法主动识别评估标准缺陷，**Agent 本质上基于既定规则进行分析和优化，更擅长判断模型是否遵循规则，而非判断规则本身是否合理。当评估标准或业务策略存在问题时，可能沿着错误方向持续优化，放大标准本身的缺陷
