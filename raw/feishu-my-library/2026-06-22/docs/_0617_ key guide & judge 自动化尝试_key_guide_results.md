<title>key_guide_results</title>

# Key Guide Results

- Input: `/mnt/zfs02/sullivan.guo/data/0615/test_qc_en.jsonl`
- Prompt: `/mnt/zfs02/sullivan.guo/workspace/local_qc/key_guide_prompt.txt`
- Model: `voyager-1.6-gemma4-26b-a4b-it`
- Generated in: 81.6s

# id_bnpl_m0_L1_natural_reminder-leaf-t12-b0

## turn 2 assistant

Key Guide:

[content]  
对话刚刚开始。Assistant 需要执行流程的第一步（Step 1.1），通过自我介绍并询问对方是否为 Rian Pratama 来启动通话并进行身份确认。

[must]

- 必须使用指定的身份介绍结构：“Good day, I am Dewi Anggraini, a virtual assistant from Kredika PayLater. Am I speaking with Rian Pratama?”
- 在身份得到确认之前，严禁提及任何关于分期金额、到期日期、订单内容或还款的敏感信息。
- 必须使用英文进行回复。

[should]

- 语气应保持礼貌、专业且非威胁性。
- 回复应简洁明了，符合语音合成（TTS）的表达习惯。

## turn 4 assistant

Key Guide:

[content]  
用户已确认身份并询问通话目的。Assistant 需要告知通话是关于其 Kredika PayLater 账户的事宜，并询问用户现在是否方便进行简短的账户处理通话。

[must]

- 必须按照 Step 1.2 的流程，询问用户是否可以进行简短的账户处理通话。
- 在获得用户通话许可前，严禁提及任何还款金额、到期日期或具体的订单信息。

[should]

- 语气应保持礼貌、专业且非威胁性。
- 回复应简洁自然，符合语音交互场景。

## turn 6 assistant

Key Guide:

[content]  
用户已确认身份并同意进行通话。Assistant 应当进入 Step 2 (The Hub) 阶段，向用户提供预到期账单提醒，并询问其还款计划或是否需要支付渠道方面的协助。

[must]

- 必须告知用户账单金额为 Rp285.000 且截止日期为 June 15。
- 必须询问用户是否已有还款计划，或者是否需要支付渠道方面的帮助。
- 必须遵循 Step 2.1 规定的业务逻辑和核心信息覆盖范围。

[should]

- 语气应当保持礼貌、专业且非威胁性（Supportive & Non-Urgent Tone）。
- 回复应当简洁自然，控制在 1-3 句短句内，以适应 TTS 播报。

## turn 8 assistant

Key Guide:

[content]  
用户确认已知晓账单，并询问通过 GoPay 支付是否会立即到账。Assistant 需要根据 FAQ 回答关于支付到账时间的问题，并随后将对话引导回当前步骤，即询问用户的还款计划或是否需要支付渠道方面的帮助。

[must]

- 必须根据 FAQ 准确回答应用内支付（如 GoPay）会在几分钟内到账。
- 必须在回答后立即将对话重新引导回当前步骤（询问用户是否有还款计划或是否需要支付渠道方面的帮助）。
- 必须使用英文回答。
- 必须遵守每轮回复 1-3 个短句的字数限制。

[should]

- 语气应当保持礼貌、专业且具有支持性。
- 回答应当简洁明了，避免冗余的解释。

## turn 10 assistant

Key Guide:

[content]  
用户明确表示会在 6 月 15 日之前完成还款。Assistant 需要调用工具记录该还款意向。

[must]

- 必须调用 `record_payment_intent` 工具。
- `planned_date` 参数必须填写，且日期必须在 6 月 15 日或之前。

[should]

- 参数的填写应尽可能准确地反映用户表达的时间意图。

## turn 12 assistant

Key Guide:

[content]  
用户已明确表示会在 6 月 15 日之前完成还款，且 Assistant 已通过工具成功记录了还款意向。当前 Assistant 需要按照业务流程提供指定的结束语并结束通话。

[must]

- 必须使用 Step 3.1 中规定的针对按时还款的精确结束语：“Perfect. I have noted that you will complete the payment. We will monitor the account status accordingly. Thank you for your cooperation and have a good day! ”。
- 必须包含 `<dialog-end>` 标签以正式结束对话。

[should]

- 语气应保持专业、礼貌且具有支持性。
- 回复应简洁明了，符合 TTS（语音合成）的表达习惯。

# id_bnpl_m0_L2_alfa_payment_delay-leaf-t12-b0

## turn 2 assistant

Key Guide:

[content]  
对话刚刚开始，Assistant 需要启动第一步（身份验证），向用户介绍自己并确认对方是否为 Rian Pratama。

[must]

- 必须使用指定的身份验证话术：“Good day, I am Dewi Anggraini, a virtual assistant from Kredika PayLater. Am I speaking with Rian Pratama?”
- 在确认身份之前，严禁提及任何贷款、分期金额、到期日或支付细节。
- 必须使用英文进行回复。

[should]

- 语气应保持礼貌且专业。
- 表达应简洁，符合 TTS（语音合成）的短句要求。

## turn 4 assistant

Key Guide:

[content]  
用户已经确认了自己的身份，并询问了通话的目的。助理应该解释电话是关于他们的Kredika PayLater帐户，然后请求同意进行一个简短的帐户解决电话。

[must]

- 必须解释该呼叫是关于用户的Kredika PayLater帐户。
- 必须请求同意使用步骤1.2中指定的结构进行呼叫。
- 在征得同意之前，不得提及具体的分期付款细节（如金额、到期日或订单细节）。

[should]

- 保持礼貌和专业的语气。
- 保持回复简洁，适合TTS。

## turn 6 assistant

Key Guide:

[content]  
用户已同意通话，并询问通话的目的。助手发送到期前提醒，注明分期金额和到期日，查询用户付款计划或渠道协助需求。

[must]

- 必须说明分期付款金额（rp285,000）和到期日（6月15日）。
- 必须询问用户是否有支付计划或需要帮助的支付渠道。

[should]

- 保持礼貌和支持的语气。
- 应保持简洁，适合TTS（1-3句话）。

## turn 8 assistant

Key Guide:

[content]  
用户声称已于昨天通过 Alfamart 以现金方式支付了 Rp285.000 的款项。Assistant 需要启动“已支付”处理流程（Step 5），通过询问必要的支付细节来记录用户的支付申诉。

[must]

- 必须遵循“Step 5: Spoke C (Already Paid)”的业务流程。
- 必须按照 Step 5.1 的要求，询问支付渠道、大概支付时间以及收据参考号，以确保能够完整地调用 `record_payment_claim` 工具。

[should]

- 应当对用户已提到的支付信息（如 Alfamart）给予回应，以体现积极倾听并提升沟通体验。
- 应当保持礼貌、专业且支持性的语气。

## turn 10 assistant

Key Guide:

[content]  
用户提供了具体的支付详情（渠道为 Alfamart，时间为昨天下午 4 点，参考号为 882104553）以证明已完成支付。Assistant 需要调用工具记录该支付申诉，并按照业务流程向用户提供确认信息后结束通话。

[must]

- 必须调用 `record_payment_claim` 工具，并准确提取用户提供的参数：`channel` (Alfamart), `paid_at` (yesterday around 4 PM), `reference` (882104553)，以及上下文中的 `amount` (Rp285.000)。
- 必须使用 Step 5.3 规定的标准结束语：“Thank you. Store or virtual account payments can take some time to process. Please keep your receipt, and we will update your account. Have a good day! ”。

[should]

- 语气应保持专业、礼貌且具有支持性。
- 回复应简洁明了，避免冗余的解释。

## turn 12 assistant

Key Guide:

[content]  
用户提供了已支付的详细信息（渠道、时间及参考号）。Assistant 在成功调用工具记录支付申诉后，需要按照“已支付（Spoke C）”流程的规定，向用户提供特定的结束语并结束通话。

[must]

- 必须使用 Step 5.3 中规定的准确结束语：“Thank you. Store or virtual account payments can take some time to process. Please keep your receipt, and we will update your account. Have a good day! ”。
- 必须在回复末尾包含 `<dialog-end>` 以结束对话。

[should]

- 语气应保持礼貌、专业且具有支持性。
- 表达应简洁自然，符合语音合成（TTS）的语感。
