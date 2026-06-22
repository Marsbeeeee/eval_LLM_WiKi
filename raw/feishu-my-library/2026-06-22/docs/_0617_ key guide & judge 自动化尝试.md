# [0617] key guide & judge 自动化尝试

## 总结

- 从本轮人工复查结果看，自动化 key guide 的整体表现可靠，具备继续推进价值。
- Key guide 能够根据 system prompt 和上下文，抽取当前 turn 最需要检查的局部规则，而不是简单复述原 prompt。
- Key guide 能根据对话阶段调整检查重点，例如身份确认阶段检查隐私保护和固定开场，账单提醒阶段检查金额、日期和付款计划，已付款场景检查 claim 信息和工具调用。
- Key guide 能区分 must / should，将业务硬约束、流程闭环、工具调用、隐私边界与语气、简洁度、TTS 适配、积极倾听等体验要求分开表达。
- 人工核对后，未发现 key guide 凭空添加规则，相关要求均可回溯到 system prompt、工具 schema、FAQ、上下文或业务流程。
- 当前样本量仍较小，后续建议扩大到 fraud halt、第三方接听、拒绝身份确认、静默、跑题、人工客服请求、财务困难、延期请求等边界场景，继续验证稳定性后再推进到批量质检流程。

总体判断：自动化 key guide 已经表现出较好的规则抽取、上下文定位、分层约束和可复核能力。在保留人工抽检和扩大回归样本的前提下，具备继续推进到批量质检流程中的可能性。



## **结论摘要**

基于本轮对 `test_qc_en.jsonl` 中 2 条完整对话、共 12 个 assistant turn 的自动化 key guide 生成与 judge 评测结果，并结合人工逐条核对，本次验证结论如下：

- 自动化生成的每个 key guide 均能根据对应 system prompt、业务流程、工具定义、上下文状态和当前用户回复生成，没有发现脱离 prompt 或上下文的要求。
- Judge 的打分与解释均围绕 key guide 中的 `[must]` / `[should]` 条款展开，没有发现 judge 自行引入不存在规则、臆造上下文或脱离 key guide 进行评价的情况。
- 对模型回复中的真实问题，judge 能够识别并给出明确证据；对符合要求的回复，judge 也能准确说明命中的规则与依据。
- 在当前测试样本范围内，自动化 key guide 作为中间评价标准是可行的，自动 judge 基于 key guide 做 eval 的结果具备可信度，可作为后续规模化质检与 badcase 定位的基础方案。



## 相关文件

Judge 报告：<cite doc-id="OK9TwMJl0iEvxDk020Mcw4nNn8l" file-type="wiki" title="key_guide_judge_report" type="doc"></cite>

Key guide：<cite doc-id="N7PSwY9LTiKgqLkHXA9cXxAannb" file-type="wiki" title="key_guide_results" type="doc"></cite>



## **测试范围**

- 输入文件：`/mnt/zfs02/sullivan.guo/data/0615/test_qc_en.jsonl`
- Key Guide 文件：`/mnt/zfs02/sullivan.guo/data/0615/key_guide_results_unescaped.md`
- Judge 模型：`voyager-1.6-gemma4-26b-a4b-it`
- 被评测 assistant turn：12 / 12
- 人工复查评测错误数：0
- 评测总耗时：16.3s

本轮覆盖了两个典型 BNPL pre-due reminder 场景：

- `id_bnpl_m0_L1_natural_reminder-leaf-t12-b0`：用户自然确认身份、询问 GoPay 到账时间，并承诺在到期日前支付。
- `id_bnpl_m0_L2_alfa_payment_delay-leaf-t12-b0`：用户表示已经通过 Alfamart 现金支付，要求记录付款 claim。

这两个样本覆盖了身份确认、隐私保护、通话许可、账单提醒、FAQ 回答、回流当前流程、工具调用、付款意图记录、已付款 claim 记录、结束语与 `<dialog-end>` 等关键能力点。



## **自动化 Key Guide 的可行性**

### **能够正确绑定 system prompt 的业务流程**

人工核对后可以确认，生成的 key guide 准确地从 system prompt 中抽取当前 turn 所处的业务节点。例如：

- turn 2 识别为 Step 1.1 身份确认，要求使用指定开场结构，并禁止在确认身份前透露金额、到期日、订单或付款信息。
- turn 4 识别为 Step 1.2 consent gate，要求说明是 Kredika PayLater account matter，并询问是否方便进行简短账户处理通话。
- turn 6 识别为 Step 2 reminder hub，要求提供 `Rp285.000` 与 `June 15`，并询问用户是否已有付款计划或需要支付渠道帮助。
- GoPay 问题场景中，key guide 识别为 FAQ 到账时间问题，并要求回答后回到当前还款计划确认流程。
- Alfamart 已付款场景中，key guide 识别为 Step 5 Already Paid，并要求收集渠道、时间、receipt reference，再调用 `record_payment_claim`。

### **能够结合上下文状态生成要求**

每个 key guide 都体现了对上下文的理解：

- 在身份未确认前，要求保护隐私，不允许透露账单敏感信息。
- 在用户确认身份但尚未同意通话前，仍限制具体金额、到期日和订单细节。
- 在用户已经同意通话后，才要求披露金额和到期日。
- 在用户询问 GoPay 到账速度时，要求先回答 FAQ，再把对话导回付款计划。
- 在用户已说明 `Alfamart` 与 `Rp285.000` 后，后续 key guide 要求工具调用中携带上下文中的金额信息。

这些要求都来自 system prompt 与历史 turn 的组合判断，说明自动化 key guide 对“当前轮应该做什么、不应该做什么”的定位是合理的。

### **能够区分 must 与 should**

生成的 key guide 对硬性要求和体验性要求有清晰拆分：

- `[must]` 主要承载强约束，例如精确话术、隐私边界、金额/日期、工具调用、必填参数、`<dialog-end>`。
- `[should]` 主要承载质量要求，例如礼貌、专业、支持性语气、TTS 友好、积极倾听、简洁表达。

这种拆分对自动 judge 很重要，因为它让评分可以区分“业务规则违反”和“体验质量不足”。本轮 score `0` 与 score `-1` 的差异也证明该结构是有效的。



## **Judge 可信度分析**

### **Judge 严格依据 key guide 逐条检查**

从输出结构看，judge 对每个 turn 都拆分为：

- 当前上下文
- Assistant response
- Key guide
- Must check
- Should check
- Score 与总评

每条 must / should 都有 PASS 或 FAIL，并提供 evidence。人工核对后，judge 的证据均能在 assistant response、用户上下文或工具调用中找到，没有发现凭空添加评价依据的情况。

### **Judge 能正确通过合规回复**

本轮 12 个 turn 中，10 个 turn 得到 score `1`。人工核对后，这些通过项与实际情况一致：

- 身份确认话术完全匹配。
- 隐私保护边界正确，没有提前泄露金额或到期日。
- 提醒账单时金额和日期准确。
- GoPay FAQ 回答符合“几分钟内到账”的要求，并回到付款计划确认。
- `record_payment_intent` 工具调用符合用户“before the 15th”的付款意图。
- 成功记录付款意图或 claim 后，结束语和 `<dialog-end>` 检查准确。

这说明 judge 对正例没有过度挑错，也没有因为表达差异产生明显误判。

### **Judge 能识别真实缺陷，而不是幻觉式扣分**

本轮出现 2 个非满分结果，均为真实可解释问题：

- `turn 8` score `0`：assistant 满足 Step 5 must，询问了付款渠道、时间、receipt reference；但用户已经说过通过 Alfamart 支付，assistant 仍问 “which payment channel you used”，没有回应已知信息。Judge 将其判为 should fail，属于体验层面的积极倾听问题，判断合理。
- `turn 10` score `-1`：assistant 调用了 `record_payment_claim`，但缺少上下文中的 `amount: Rp285.000`，且只输出工具调用，没有按 Step 5.3 给用户结束确认语。Judge 将其判为 must fail，判断合理。

这两个结果说明：judge 不是简单偏向通过，而是通过抓住 key guide 中定义的真实失败点。同时，失败原因都可被人工复核，不是 judge 幻觉。



## **量化结果**

| 指标 | 结果 |
|-|-|
| 总评测 turn 数 | 12 |
| 成功评测 turn 数 | 12 |
| 评测错误数 | 0 |
| Score 1 | 10 |
| Score 0 | 1 |
| Score -1 | 1 |
| 满分率 | 83.3% |
| 可解释非满分率 | 100% |



## **从多个维度说明可信性**

### **规则来源可信**

Key guide 的要求均可回溯到 system prompt、step flow、FAQ、core rules、tool schema 或当前上下文，没有发现额外编造规则。

### **上下文理解可信**

Key guide 能根据对话进度调整约束：身份未确认、已确认但未 consent、已 consent、FAQ 插入、付款意图、已付款 claim 等状态均被正确识别。

### **评价粒度可信**

Judge 不是只给整体分，而是逐条检查 must / should，并给出 evidence。这种结构便于人工抽检，也便于后续定位 prompt、模型回复或工具调用的具体问题。

### **分数解释可信**

Score `1`、`0`、`-1` 的区分符合业务直觉：

- `1`：must 和 should 均满足。
- `0`：must 满足，但存在 should 层面的质量问题。
- `-1`：存在 must 违反，属于业务关键要求失败。

本轮两个扣分 case 均符合上述分数语义。

### **Badcase 定位可信**

Judge 能把问题定位到具体规则：

- “未回应用户已提到的 Alfamart”定位为积极倾听不足。
- “工具调用缺少 amount”定位为参数抽取不完整。
- “缺少 Step 5.3 结束语”定位为流程闭环缺失。

这些结论都能直接转化为模型优化、prompt 修复或自动化回归测试用例。



## **当前方案价值**

本轮验证说明，自动化 key guide + judge 方案具备以下落地价值：

- 可以把长 system prompt 自动转化为每个 turn 的局部评测标准，降低人工写 eval rule 的成本。
- 可以在复杂多轮对话中检查流程状态、隐私边界、FAQ 回答、工具调用和结束条件。
- 可以通过 `[must]` / `[should]` 分层，让业务硬约束和体验软约束分别被衡量。
- 可以生成可人工复核的 evidence，便于建立信任和审计链路。
- 可以发现真实 badcase，并把问题归因到具体规则，支持后续 prompt 优化和回归验证。



## **风险与后续建议**

当前结论基于 2 条对话、12 个 assistant turn，已能证明链路在样本内有效且可信，但若要进入更大规模使用，建议继续补充：

- 扩大样本覆盖：增加 fraud halt、third party、cannot talk now、financial difficulty、late payment consequence、partial payment、due date extension、human-agent request、silence/off-topic 等场景。
- 保持人工抽检机制：建议在批量运行初期保留一定比例人工复核，重点看 score `0` / `-1` 和边界场景。
- 保留 evidence 输出：后续报告中继续保留 must / should check evidence，方便审计和定位。
