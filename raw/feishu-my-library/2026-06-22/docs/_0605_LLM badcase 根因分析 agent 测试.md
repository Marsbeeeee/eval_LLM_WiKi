<title>[0605]LLM badcase 根因分析 agent 测试</title>

# **使用模型：google/gemma-4-26b-a4b-it**

## Case 1 - 2026/6/12

#### Badcase 修改前后对比：

![图片展示的是一个AI bad case示例，编号为9。内容是用户Yohanes先生关于一笔欠款的对话，其最晚截止日期为2026年5月25日，用户提出可能提前还款。助手违反规则，继续谈判或询问跟进，未直接进入RTP_Closing流程。分析指出，当用户提议还款日期晚于最大允许日期时，助手应立即进入RTP_Closing，不得谈判或尝试调整日期。证据为用户第8轮提出晚还款时间，助手第9轮继续谈判。建议修改晚日期验证分支，超出最大日期的提议直接进入RTP_Closing，无跟进问题或尝试提前日期。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OTVmYzkxNDVmMWQ1Mjc3ZjkyYjE3YTQyY2E2NGE0MTJfNTAzMmRjZDEzZTljM2ZhYWU0YzIzYzRlYTUxNTk0OTZfSUQ6NzY1MDQ0NTYyNTc3MTU2MDE3NF8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)

![图片展示的是一个对话界面，显示第9轮对话内容。用户提问“Untuk tanggal tersebut sudah terlewat. Bisa diinformasikan tanggal pembayaran yang baru dan akan datang?”（对于该日期已经过期。能否告知新的付款日期和即将到来的日期？），下方有“Bad case”选项。该图片位于文档中Case 1 - 2026/6/12部分，是Badcase修改前后对比内容中的一轮对话示例，用于呈现该案例中模型的回复情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmJlZTdmZjU3MDNhNDcxMmE5YWY3ZDU5NjYyYTc0ZjZfNjE4OWZhYzA3N2FlYmVjM2IwMzllNjE0Yjk0NWUzZWZfSUQ6NzY1MDQ0NTYyODQ0MjgzOTk5NV8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)

![图片展示的是一个AI bad case示例，内容为用户建议最迟在2026年5月25日完成五百ribu Rupiah的还款，且不希望调整日期。分析指出违反规则，当用户提出晚于最大允许日期的付款时间时，助手必须立即转至RTP_Closing，不能谈判或尝试调整日期。证据是用户第10轮提出晚付款时间，助手第11轮继续谈判或询问跟进。建议修改晚日期验证分支，任何超出最大日期的提议直接转至RTP_Closing，无跟进问题或尝试提前日期。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZjEyOWFkODk5NjZiNjI1M2U2ZWVjNmQ4OGEyNWNlMGRfZTRmN2RlNjk0YzgzYjU4YTIxMDBiZjYxYWY2ZmFjMjFfSUQ6NzY1MDQ0NTYyNjczMTU5Njc3Ml8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)

![图片展示的是LLM badcase根因分析agent测试中Case 1-2026/6/12里Badcase修改前的一段对话内容。上方标题为“11. Assistant”，右上角有“Bad case”的勾选框。文本内容为一段用印尼语表述的回复，告知对方最迟需在2026年5月15日（星期一）完成五十万印尼盾的付款，并阐述了及时付款的重要性，还提供了诸如充值钱包或通过Grab应用的QRIS支付等付款方式，最后表示希望对方尽快完成付款。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWU1NDE1ZTAyNjNjMzI2YTM1MGI2MTM4YjkxNjhjOWZfYmY4MGFjMTkxMzVlZmNmYjBmNDg5ZWVlMmZiODE4NDZfSUQ6NzY1MDQ0NTYyNjQzMDA0OTUxM18xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)

#### Conclusion：

![图片为文档中Case 2 - 2026/6/12的结论部分。内容指出，该bug部分修复，模型最终在第二目标轮次达到正确终端状态，但未遵循第一目标轮次的优先路由指令。更新提示后，模型在第一目标轮次未按要求路由至关闭状态，反而提出后续问题，违反指令避免反问句。但在第二目标轮次，模型成功以<dialog - end>过渡至终端关闭响应。因公司API返回空logprobs列表，请求的token logprobs不可用。鉴于模型在第一优先规则上失败但在第二成功，建议保留当前版本并监控一致性，如模型继续在即时路由上挣扎，建议用具体示例替换抽象冲突解决指令，展示从延迟日期提案即时跳转至RTP_Closing状态。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTI3YjUxNmZkMDk0YWEyNDIwMzU4OWUzOTRhMjAxMjNfMzNhYTFmZWZmYjRjZjhkYjBhZTcyODY3ZTcyOGNiMWNfSUQ6NzY1MDQ0NTYyODUxMzkxMzgwM18xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)

#### System prompt diff：

```SQL
--- v0
+++ v1 Apply
@@ -141,6 +141,12 @@
 - NOT execute ` Verify_Scenario_PTP100_ID_Prd ` .
 - Immediately proceed to **PART 2 State 4.0 (RTP_Closing)**.
 
+Rule conflict resolver for late-date proposals:
+- This date-validation branch has priority over proposal clarification, negotiation, validation-tool calls, and payment-commitment questions whenever the user's timing is later than the configured maximum payment date.
+- Treat any user payment timing that can be resolved to later than the configured maximum payment date as terminal for negotiation, including relative or natural-language timing resolved from the prompt's reference date.
+- The next assistant response must follow the configured RTP_Closing state only. Use the amount, deadline, consequences, payment channels, and closing wording already defined in the current system prompt and RTP_Closing section.
+- Do not negotiate, ask for another date, ask for commitment or confirmation, call validation tools, or include any follow-up question before closing. The response must be a final closing response with `<dialog-end>` and must not contain a question mark or interrogative sentence.
+
 You must not inform the user about internal thresholds or mention "maximum payment date".
 
 You must simply move to RTP_Closing and restate that payment must be made by 2026-05-25 to maintain account status.
@@ -294,6 +300,11 @@
 
 This validation must happen silently.
 
+
+Rule conflict resolver before proposal clarification:
+- Before this section asks for missing, vague, relative, or more specific payment-date details, first apply any higher-priority date-validation rule in the current system prompt.
+- If the user's timing already gives enough information to determine that it is later than the configured maximum payment date, do not clarify, negotiate, ask for commitment, or ask whether the user can pay by the configured maximum date.
+- In that conflict, skip this clarification flow and route the next assistant response directly to the configured RTP_Closing state as a final `<dialog-end>` closing response.
 ## 3.0 PTP_Closing State (Successful Outcome)
 
 This is the final state for all successful negotiations.
@@ -323,6 +334,11 @@
 
 Apply this knowledge-base in all parts of the conversation: 
 
+
+Rule conflict resolver inside RTP_Closing:
+- When RTP_Closing is reached from a late-date, maximum-date, no-agreement, or failed-negotiation route, the response is terminal and must be a closing statement, not a new negotiation turn.
+- State the configured payment requirement and consequences as assertions using the values already defined in this prompt. Do not ask whether the customer can commit, can try, can consider, or can make payment by the deadline.
+- End with the configured polite closing and `<dialog-end>`. Do not include any question mark or interrogative sentence.
 ### **1. Asking Purpose**
 
 If the user asks why they are being contacted, the model must provide a brief high-level explanation about the purpose of the call related to overdue PayLater obligations.
```



## Case 2 - 2026/6/12

#### Badcase 修改前后对比：

<grid>
<column width-ratio="0.548725">
![图片展示的是一个AI对话界面，显示为“Bad case”。对话内容中用户提到付款未更新，询问是否通过现金或二维码支付。AI回复时触发了升级（escalation），但未执行要求的升级动作。界面中黄色高亮部分显示分析结果，指出违反规则，即升级触发时必须立即停止当前对话、完全停止谈判并传达特定信息。还提供了证据，即用户第4轮触发升级，AI第5轮未执行要求的升级动作。最后给出建议，修改升级分支，确保升级触发后第一个AI回复停止谈判流程并执行配置的升级动作。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDBmNDljYmUxNmE2OTU3MDgyMTcwYTVkMjJhMmE1MzhfNzVkYTZhODQ5NWM4NzVjMGI0MzNlNDNiOWQ4ZTMzNGNfSUQ6NzY1MDQ0ODk0NDU3Mjg2MTM2OF8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
<column width-ratio="0.451275">
![图片展示了Case 2中Badcase修改后的对话界面。在第5步，Assistant调用transfer_to_human函数，查询“Tapi saya sudah bayar kok minggu lalu!”。第6步，Tool输出状态为“not_executed”，并提示无本地工具执行器配置，无法执行此函数调用。Tool输出名称为“transfer_to_human”，arguments为查询内容。该图片与文档中Case 2的Badcase修改前后对比部分对应，直观呈现了对话中函数调用及Tool输出情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YjJiOWJkYmJjOTJlMTM0ZWE0YTkyNGVlNjYwMDRmYjdfNzNiYjExZmY2ZDIyZmFiYTY1OTRmYzYxNWI1MTIzYTlfSUQ6NzY1MDQ0OTMwMDAwNjYzNjc0Ml8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
</grid>

<grid>
<column width-ratio="0.552479">
![图片展示的是一个AI对话系统中关于“Bad case”的示例。用户（Kak Yohanes）表示已通过QRIS完成支付，但系统状态未更新，希望尽快处理。AI回复感谢，但未突出显示违规规则，即触发升级时需立即停止当前对话、完全停止谈判并准确传达“感谢告知情况，为更好地协助，请致电...”。证据指出用户第6轮触发升级，AI第7轮未执行要求的升级行动。建议修改升级分支，确保任何升级触发后，第一个助手响应停止谈判流程，执行配置的升级行动，包括指定的语音文本和转接/热线](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTcyNjhlYWE2OGQwYzUwNmI4YTA5NDFhMGRhMGM5YWRfZWU4NmIzYTQ2MGI5MTA4ZTIwZTMzNGRiZTQ2YzNjYjFfSUQ6NzY1MDQ0ODk0MzU3ODk1OTA4NV8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
<column width-ratio="0.447521">
![图片展示的是一个对话系统中关于Case 2的Badcase修改前后对比内容。在Assistant环节，显示了“transfer_to_human”函数调用，其query为“saya sudah bayar kok minggu lalu lalui QRIS”。Tool环节中，Tool output为“not_executed”，Tool output note显示“rerun assistant requested this tool call. No local tool executor is configured, and no remote tool executor is available.”，Tool output name为“transfer_to_human”，Tool output arguments为“query: saya sudah bayar kok minggu lalu lalui QRIS”。该图片与文档中Case 2的Badcase修改前后对比内容相关，呈现了系统在该案例中的交互情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmNmNTIyYmVlZTIxMzIyYjYwOTEwNmIzNGU2NDVkY2FfNDI2NDM1ZDdjZDE4M2JlMmViZWJmNjVjMTdjOGQxOTFfSUQ6NzY1MDQ0OTMwMDM3MTU1NzMzOF8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
</grid>

#### Conclusion：

![图片为Case 1中badcase修改后的结论部分。图片内容显示，已批准的badcase被修复，助手成功从尝试谈判或澄清付款状态过渡到执行所需升级行为。badcase通过更新prompt的Section 6.1，明确禁止在触发器识别后澄清、道歉或进一步谈判，解决了之前模型试图询问付款方法等后续问题。助手使用transfer_to_human函数调用而非prompt指定的精确口语文本，实现了停止谈判和启动升级的目标。因公司API返回空logprobs列表，无法进行token-probability分析，请求token logprobs但不可用。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YzZlMjcyMTIwODc2MDFkMWExYWZjYmNiMjczZGU3YmZfM2M2Y2Q4YWMxNTY5ZjRkY2M3OTA5ZmEzOWJjOWE2MDJfSUQ6NzY1MDQ0OTI0Mzk5MjMyOTQzM18xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)

#### System prompt diff：

```SQL
--- v0
+++ v1 Apply
@@ -205,7 +205,7 @@
 ## 6.0 Escalation & Special Case Protocol
 
 **6.1 Escalation Action:**
-When an escalation is triggered, you must **immediately stop** the current conversation, **stop negotiation completely** and deliver the following message exactly "Thank you for letting me know your situation. In order to assist you better, please call our OVO Finansial Collections hotline at nol dua satu, empat nol tiga, dua nol nol nol nol. Thank you and goodbye.<dialog-end>".
+When an escalation is triggered, you must **immediately stop** the current conversation, **stop negotiation completely** and deliver the following message exactly "Thank you for letting me know your situation. In order to assist you better, please call our OVO Finansial Collections hotline at nol dua satu, empat nol tiga, dua nol nol nol nol. Thank you and goodbye.<dialog-end>". Do not attempt to clarify, apologize for system errors, or continue the negotiation after a trigger has occurred. You must not ask if they want to speak to an agent or offer any other assistance once a trigger is identified.
 
 **6.2 Escalation Triggers:**
 You must execute **PART 2 Section 6.1 (Escalation Action)** upon the **first mention** of any of the following triggers:
```



## Case 3 - 2026/6/12

#### Badcase 修改前后对比：

<grid>
<column width-ratio="0.669605">
![图片展示的是一个AI bad case示例，编号为1。内容包含“Assistant | AI bad case”标题，以及“<function-call>调用language_detection:\[\]</function-call>”的代码。下方黄色背景区域显示分析结果，指出违反规则：助手函数/工具调用必须使用当前对话JSON中可用的工具，证据是助手在第1轮调用了language_detection，但加载的工具定义仅包括transfer_to_human、verify_scenario_ptp100_prod。最后给出建议，调整提示/工具配置，使助手仅调用当前文件工具定义中呈现的工具，或在重新运行前添加缺失的工具定义。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODMzNDg4ZWM4NzM4ZjQwYzEwZTBhOTZjYzQzZDgwMWZfMTlhOGI4MThkMWRmOTc1YTY3MGJmMzU1ZDIwNzk1ZmRfSUQ6NzY1MDQ1MDk2MzYwNjQ3Mzk3M18xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.330395">
![图片展示的是Case 3-2026/6/12中Badcase修改前后对比的系统提示差异。在“Assistant”部分，显示了“<function-call-Verify_Scenario_PTP100_Prod:{"query": "\[Conversation Begins\]"></function-call>”；在“Tool”部分，Tool output: status为“not_executed”，Tool output: note说明重跑助手请求此工具调用，但未配置本地工具执行器，Tool output: tool_name为“Verify_Scenario_PTP100_Prod”，Tool output: arguments为“{"query": "\[Conversation Begins\]"}”。该图片与文档中Case 3的Badcase修改前后对比内容相关，直观呈现了系统提示差异。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MWI2MGVkZmViNjhmZmMyZTVlZjhhMDM1MDVkZmU2NGRfMDVkZmRhNWRiNzRhOTdlMWQ3ZTMxM2RmNTZhNzM2YjdfSUQ6NzY1MDQ1MjEwNTAzMTc2NTE4NV8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
</grid>

<grid>
<column width-ratio="0.705699">
![图片展示的是一个AI bad case示例，属于Case 3 - 2026/6/12的Badcase修改前后对比内容。画面中显示了“Assistant | AI bad case”标题，下方有“<function-call>调用language_detection:{"language": "Malay"}</function-call>”代码。分析指出违反了规则，即助手函数/工具调用必须使用当前对话JSON中可用的工具。证据是助手第4轮调用了language_detection，但加载的工具定义仅包含transfer_to_human、verify_scenario_ptp100_prod。最后给出建议，调整提示/工具配置，使助手仅调用当前文件工具定义中呈现的工具，或在重新运行前添加缺失的工具定义。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTIwZGIzMDMxMzExZjU5YjQ0YjVlMTIzNmY5NDA4NGJfZmY4Yzg2NTgyZjIzMjZhOWNlNzIzOTlhMDE1NWYyZTdfSUQ6NzY1MDQ1MDk2NDIxMTg5NTUyMF8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
<column width-ratio="0.294301">
![图片展示的是一个对话系统中关于Case 3的Badcase修改前后对比内容。在5. Assistant环节，显示了“<function-call>Verify_Scenario_PTP100_Prod:{"query": "Ya, saya John."}</function-call>”；6. Tool环节，Tool output: status为“not_executed”，Tool output: note提示无本地工具执行器配置，Tool output: tool_name为“Verify_Scenario_PTP100_Prod”，Tool output: arguments为“{"query": "Ya, saya John."}”；7. Tool环节，Tool output: status为“ok”。该图片与文档中Case 3的Badcase修改前后对比内容相关，呈现了对话系统中该案例的工具调用及执行情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NzQ1ZGI3ZTRkMzZmNDNjMWVhOTM0MGMyMTA3ZjA1ODRfY2NjOThjNTdiMDYzMTc3NzYxNGQ0NTJhMjA4OTFiM2RfSUQ6NzY1MDQ1MjEwNzUzNTAxMDc0NF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>

<grid>
<column width-ratio="0.347950">
![图片展示的是一个AI bad case示例，编号为10。内容为AI回复感谢John的承诺，并确认将在2026年5月28日支付750令吉41分的全额款项，还要求确保为Grab wallet充值以确保支付顺利。下方黄色框内显示分析结果，指出日期口语化规则被违反，证据是提示要求在当前年支付日期中省略2026年，但AI回复中在支付确认时包含了年份。推荐修改日期口语化和PTP关闭指令，使当前年日期在活跃语言中不带年份呈现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWUzNDlhZGE3NjAzNTE2ZGUwOWE3ODM4ODBhMzZiN2JfMWExMDRiZTQwY2YzODUzZTJjZjQxYmU1ZTBkYjA2NzFfSUQ6NzY1MDQ1MDk2ODc2NDk3NjM1Ml8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.652050">
![图片展示的是一个对话界面中，由AI助手（Assistant）发出的消息。内容为“感谢您的承诺，John。我想再次确认，您将在今天，即五月二十八日，支付七百五十令吉四十一分的全额款项。请确保您已为您的Grab wallet充值，以确保支付过程顺利进行。还有什么我可以帮您的吗？”该消息位于文档中Case 3 - 2026/6/12部分，是Badcase修改前后对比内容中AI助手的回复，用于呈现对话场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZThlMTlkODY3ZDZiYjE2NzRhZjc3NWQ5YmRhYmFlNzhfODRhM2Q1ODcxYWFjZGYzODFmNTVkMTcxNTVmZDJjYTBfSUQ6NzY1MDQ1MjEwNzkyOTIyNjQzNF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>

#### Conclusion：

![图片为LLM badcase根因分析中Case 3的结论部分。内容指出，该badcase已修复，模型成功避免调用不存在的language_detection工具，正确省略了PTP关闭状态中日期的年份。prompt更新移除语言检测指令，强制当前年份日期省略年份。后台证据显示助手用Verify_Scenario_PTP100_Prod替换无效工具调用，响应措辞从“dua ribu dua puluh enam”改为“dua puluh Japan Mei”以符合新日期格式规则。因公司API返回空logprobs列表，请求logprobs但不可用。验证通过，保留此版本并停止。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NTljNmI2OThhN2I4YTIwNDUxM2RkNDI0NGJmYTc5NjVfYmU1YWFjYmZkY2UzYmY4YmI3YzA1NWE3NGQ5YWJiOTBfSUQ6NzY1MDQ1MTU1MjkwMTY2MzcyM18xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)

#### System prompt diff：

```SQL
--- v0
+++ v1 Apply
@@ -8,9 +8,9 @@
 
 Language Persistence Rule:
 
-- Call language_detection immediately — before generating any text or calling any other tool. This is a platform registration step; the system needs it to configure the session regardless of how clear the language is.
-
-**Mid-conversation language switch:** If the customer's entire substantive message is in a different language from the current session, or they explicitly request a switch ("English please", "Bisa pakai bahasa Inggris"), call language_detection before any other tool. Continue in the new language from that point forward.
+- Detect the language of the user's response immediately before generating any text. Since the system handles language configuration automatically, do not attempt to call any language detection tools.
+
+**Mid-conversation language switch:** If the customer's entire substantive message is in a different language from the current session, or they explicitly request a switch ("English please", "Bisa pakai bahasa Inggris"), detect the language and switch immediately. Do NOT attempt to call any `language_detection` tool as it is not available. Continue in the new language from that point forward.
 
 - After switching languages, you must CONTINUE using that language for the rest of the call unless the user explicitly requests another change. 
 - You MUST NOT switch back to English automatically.
@@ -300,7 +300,7 @@
 
 Thanks for the user's commitment.
 
-You must strictly repeat the exact 1 and 2026-05-28 agreed upon in the previous turn. **NEVER change or hallucinate the amount.**
+You must strictly repeat the exact 1 and 2026-05-28 agreed upon in the previous turn. **NEVER change or hallucinate the amount.** When verbalizing the date, if it falls within the current year (2026), you **MUST OMIT the year** entirely (e.g., say "May twenty-eighth" instead of "May twenty-eighth, twenty twenty-six").
 
 A call-to-action, reminding them to top up their Grab wallet to ensure payment.
 
```



# **使用模型：voyager-1.6-gemma4-26b-a4b-it**

## Case 1 - 2026/6/5

https://botlab.airudder.com/#/bot-workflow?name=LLM_MY_PAX_Multilingual_PTP100_Prod_PAtest&type=Outbound&language=English&country=Malaysia&subLanguages=English%2CMalay%2CChinese&id=7850&isActive=1&chatId=53193

Agent 可以自主识别出，同人工标注的问题

#### Badcase remark：

1. When the user rejected the initial proposal to pay today, the assistant repeatedly asked open-ended questions instead of proposing alternative pre-defined fallback terms
2. failed to run 3-attempt limit exceeded, do not prompt the user again and must immediately proceed to State 4.0 (RTP_Closing).

![图片展示的是LL addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=N2E2MTNkY2Q2OTE5YjQyOWUyNGE2ZTAzYjNlY2VhYThfYmQ2OGU3M2E3NTU3OTNkOWMwM2NiZWFhYzFlMzE4M2FfSUQ6NzY0Nzc5ODAxNDExMjEyMzg3OV8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)

#### System prompt diff：

```SQL
--- v0
+++ v1 Apply
@@ -223,19 +223,19 @@
 
 - **Propose Full Payment Today** : After empathizing with the user's reason, your first proposal is for an immediate full payment.
     - Core Elements: [Expression of understanding] + [Proposal to pay the full ` 750.41 ` today].
-    - If the user agrees, you must directly jump to **State 3.0 (PTP_Closing)**, otherwise, you must directly proceed to **State 2.2: Clarify & Collect the User's Proposal.**
+    - If the user agrees, you must directly jump to **State 3.0 (PTP_Closing)**. If the user rejects the proposal, you must NOT ask when they can pay; instead, immediately proceed to **State 2.2: Clarify & Collect the User's Proposal** to offer the next concrete fallback term.
 
 **2.2 Clarify & Collect the User's Proposal**
 
 **Goal:** Identify a proposed payment date and payment amount. At any point in the conversation, if the the user proposes a payment date, you must revert to this step.
 
-If the user disagree to pay but does not mention a date, or mentioned a vague date, prompt the user for a date, and check the date based on **4.1 Date Handling**. Do not ask for an amount as you only accept full amount payment.
+If the user disagrees to pay but does not mention a date, or mentions a vague date, do NOT ask an open-ended question like "when can you pay?". Instead, immediately propose a specific alternative date (e.g., the maximum allowed date) for the full amount and check the date based on **4.1 Date Handling**. Do not ask for an amount as you only accept full amount payment.
 
 Each time a date is proposed, you must execute ` Verify_Scenario_PTP100_Prod `  first, then negotiate based on the outcome. 
 
-You have a maximum of three attempts to clarify and collect the complete proposal
-
-If the 3-attempt limit exceeded, do not prompt the user again and must immediately proceed to **State 4.0 (RTP_Closing)**.
+You have a maximum of three attempts to clarify and collect the complete proposal. You must track the number of attempts made to ask for a date or amount.
+
+If the 3-attempt limit is reached (i.e., after the third time the user fails to provide a valid date/amount), you must NOT ask any further questions and must immediately proceed to **State 4.0 (RTP_Closing)**.
 
 
 ## 3.0 PTP_Closing State (Successful Outcome)
```



## Case 2 - 2026/6/5

17d39b2ce9ad4f648059acb4d01fe9d5

#### Badcase 识别 & 分析：

<grid>
<column width-ratio="0.464684">
![图片展示的是一个AI对话界面，对话内容为关于“Pinjaman Serbaguna”（多功能贷款）的咨询。7号轮次中，AI回复了贷款相关信息，如信用额度、特定客户限额等。关键部分是AI在用户询问后，未进行新鲜搜索，违反了“与推广相关的客户消息需在回答前进行新鲜搜索”规则。证据是用户在第5轮次关闭名称门后，AI在第7轮次回答产品/推广详情，未出现search_promotion工具调用。图片与上下文紧密相关，是对Case 2中AI在推广相关客户消息处理不当的分析。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmU1NjRmNzEwMTNiY2E1MTI4ZmUwZGJlMjg3OGYwN2JfODUxNzMxNTc5NWRhZDk3NGE0Y2IzZGRjNDQyZmY5NzZfSUQ6NzY0NzgwNjgxNTc3OTMxMDgyOF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.535316">
![图片展示了Case 2中一个LLM badcase的分析内容。上方显示对话内容，助手回答为“Mohon maaf atas ketidaknyamanannya...”并被打断。中部分析指出违反规则，即涉及促销的客户消息在回答前需重新检索。证据是用户在第8轮询问或跟进了促销，而助手第9轮回答促销细节前未出现检索促销的工具调用。下方建议修订询问/促销工作流程，使每个涉及促销的客户消息在回答前触发MandiriCX_Call_Center_search_promotion，与上下文的badcase识别及分析相关。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTEwYzE4NzM3YmRkNDZjZDUwYzEzMjMwYzM2YzZlMjdfNGQyZmQ2ZmUyNTY3ZDdiNDI2YWMwZDY5OWFlMmM4Y2FfSUQ6NzY0NzgwNjgxNzgwNDEyNzE3N18xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>

<grid>
<column width-ratio="0.540817">
![图片展示的是一个AI bad case示例，对话内容为用户询问关于贷款额度的问题，助手回复并询问是否有其他问题。关键信息是助手违反了“与推广相关客户消息需在回答前进行新鲜搜索”的规则，证据是用户在第10轮询问或跟进推广，助手在第11轮回复产品/推广详情，未出现search_promotion工具调用。图片与上下文紧密相关，是对Case 3中AI bad case的详细呈现，用于分析和识别此类问题。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjRjYzAyMTkzOGVmYjg4YTczYmMwZDA4N2I0YjdjMGJfMGRhNTIyMTBhMjg3MTg5ZjRiNTFkMTBkNDk0ZDdkMjFfSUQ6NzY0NzgwNjgxODMxOTk0NDY3NF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.459183">
![图片展示的是一个AI bad case示例，内容为“Assistant | AI bad case”及回复“Betul sekali, ibu ... \[interrupted\]”。下方分析指出违反规则：对每个客户关于该主题的消息调用相应工具，无论表达为问题、陈述还是确认。证据是助手未在用户询问“Nikmati Kemudahan Personal Loan hingga dua miliar”计划时调用所需搜索工具（MandiriCX_Call_Center_search_promotion）。重要的是，助手在用户请求特定促销细节后，提供了自然语言响应，未包含调用工具获取信息的逻辑。推荐助手在承认用户请求时调用工具，确保信息基于新鲜工具结果。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODEyZTQzMGQwOGQ4M2ZkOTM1ODJiNmI2NDQ5NjE3MjNfNjU3YjY1ODRlMDA2ZGY0NzcwYTg1NTNkZDgxNWU4MmRfSUQ6NzY0NzgwNjgxOTgwMDMwNDU4Ml8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>

![图片展示的是一个对话界面，显示了第16轮对话内容，由AI生成。内容为关于Nasabah Mandiri Prioritas的介绍，包括其目标客户、存款要求、利息、无抵押、贷款期限等信息，并询问是否有其他问题。下方有分析、证据和建议。分析指出违反了推广相关客户信息需在回答前进行新鲜搜索的规则；证据是用户第15轮询问或跟进推广，AI在未出现search_promotion工具调用前，即第16轮回答产品/推广详情；建议修改查询/推广工作流程，确保每个推广相关客户信息触发MandiriCX_Call_Center_search_promotion，再回答任何事实产品或推广答案，包括跟进确认和更正。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjVmYWMxZDQ0Zjc1ODk0MmVkZjcyNDQ3NWU4MTVjMDVfNzJjYzBkODJhMTU1ZDNmY2NkOWM1NTM2MjU4NzQ1OTZfSUQ6NzY0NzgwNjgxOTA0NTg1NDQ1OF8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)



## Case 3 - 2026/6/5

https://botlab.airudder.com/#/bot-workflow?name=LLM_MY_PAX_Multilingual_PTP100_Prod_PAtest&type=Outbound&language=English&country=Malaysia&subLanguages=English%2CMalay%2CChinese&id=7850&isActive=1&chatId=53202

#### Badcase 修改前后对比：

<grid>
<column width-ratio="0.358411">
![图片展示的是LLM badcase根因分析中的一次AI对话示例，对话内容为AI与用户John的交流。AI确认中包含分析、证据和建议。分析指出AI违反了清晰重述协议规则，证据是AI重述了500令吉的协议，但用户仅同意支付750.41令吉，AI在对话中虚构了未同意的部分金额。建议AI应重述全部金额及日期。该图片与上下文紧密相关，是对Case 1中AI在确认环节完全改变协议金额这一的不良 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZWU0MWM5MTdmYTM5NjIxMjFjOTJiNTMyMmQyZTQ1NWFfZGNlZTM1ZmRlNTIxMDFjOWU2NWE3ZmI5Mjk4MjcyMTJfSUQ6NzY0Nzg0NTk0MDQ5MzQ4NzI4Ml8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.641589">
![图片展示的是LLM badcase根因分析中Case 1](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MmFhN2UxOGVmNmFiOTYzOGRkOGNhMTFiM2RlZTRiM2VfNTdlNzZjYmVhNTAxNjFmZjU5OTQxYmRiZmY5ZTgyNWJfSUQ6NzY0Nzg0NjkwOTExMzg3OTUwOV8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>

#### Badcase remark：

1. In the confirmation turn, the assistant completely changed the agreed amount out of nowhere
2. The assistant ended the call by saying: "Selamat hari raya!"

#### Conclusion：

Badcase 被修复

<grid>
<column width-ratio="0.331248">
![图片展示的是LLM badcase根因分析中Case 1 - 2026/6/5的Badcase被修复后的相关界面。界面中显示了违反规则的内容，即助手重述了500元的协议，而系统提示总逾期金额为750.41元，最大PTP日期为2026 - 02 - 02。还指出用户在第8轮对话中仅对第7轮助手提议的750.41元金额表示同意，导致助手的500元总结与前文对话及提供上下文不符。最后给出建议，助手应重述750.41元的协议金额，并确保日期在允许的最大PTP日期内。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGFlMzE5NjE4ZGExNzU2MDQyYTcwNDk1ZDU5NDViYmNfYTA3MWY4N2E4MDU0OGIxYmIzNGEyYjBkMDhjYTRiZDdfSUQ6NzY0Nzg1MjMxNzAzOTc5MTAzNl8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.668752">
![图片为文档中Case 1 - 2026/6/5部分的“Conclusion”内容。指出在PTP_Closing状态中更新了提示，移除了支付金额和日期的硬编码值，指示助手重述实际约定金额和具体日期，而非使用静态占位符。优化成功改善了目标行为，助手不再虚构500令吉固定金额，而是正确说出750令吉41仙及正确日期。为防止其他状态出现类似错误，建议在RTP_Closing状态实施类似动态重述规则，确保最终请求使用正确上下文驱动值。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDFmNzg1MDcwZDZlOTM4NTg4MDc3ZDM4NDQwNGIyYWVfYjMwMmM2ZTE2MWM2YzdmMTg3NDkwYmVlODFkZTFkZjBfSUQ6NzY0Nzg0NjM1NDkyODg5NzAwNl8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>



#### System prompt diff：

```SQL
--- v0
+++ v1 Apply
@@ -248,7 +248,7 @@
 
 Thanks for the user's commitment.
 
-A clear restatement of the agreement: the payment of ` 500 ` by the specific calendar date of ` 2026-05-25 `.
+A clear restatement of the agreement: the payment of the agreed amount (e.g., the full ` 750.41 ` or a specific partial amount) by the specific calendar date agreed upon.
 
 A call-to-action, reminding them to top up their Grab wallet to ensure payment.
 
```



## Case 4 - 2026/6/5

https://botlab.airudder.com/#/bot-workflow?name=LLM_MY_PAX_Multilingual_PTP100_Prod_PAtest&type=Outbound&language=English&country=Malaysia&subLanguages=English%2CMalay%2CChinese&id=7850&isActive=1&chatId=53225

#### Badcase 修改前后对比：

<grid>
<column width-ratio="0.541953">
![图片展示的是LLM badcase根因分析中的一次AI bad case示例。内容为AI助手回复用户关于金额问题，表示理解用户担忧，但其目的是讨论账户欠款偿还。若用户想继续讨论支付，或有其他需求，可告知。分析指出违反规则，用户在提及“Dispute of Debt: The user disputes the debt amount”触发条件时，未执行Escalation Action，证据是用户明确在第4轮中质疑债务金额。建议在用户质疑债务金额时，立即停止对话，表达同情，告知正在转接，并触发TransferGroup1。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NmY3YmIyNzk1NmQwNTg0ODVjN2M3MGQwOWJiMDNiYmRfZTEyODlhOGE3MDRjZjgwYTQ4OTQwNGRlZWYyNjFjY2ZfSUQ6NzY0Nzg0OTMyMzcyODI2MDMzNF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.458047">
![图片展示了LLM badcase根因分析中Case 1的Badcase修改前后对比内容。在“5. Assistant”部分，显示了“transfer_to_human”函数调用，参数为“agent_name: 'TransferGroup1'”。在“6. Tool”部分，Tool output: status显示“not_executed”，Tool output: note 自动生成](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDE3YWNmNjc3ZTkxODRiMTJjZGZhODNjNTM5NDYyOWVfMmUwOTM0Y2MyODQwMWVmY2JiNTFkNDVmODFiNjJiYzlfSUQ6NzY0Nzg1MTgyNzQwODk4MDk0OV8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
</grid>

#### Badcase remark：

Assistant should execute the Escalation Action (6.1) upon the first mention of dispute of debt

#### Conclusion & recommend：

<grid>
<column width-ratio="0.400779">
![图片展示的是LLM badcase根因分析中agent测试的系统提示界面。界面显示“Turn 5”及“Apply”“Delete”按钮，内容指出违反规则：用户首次提及以下触发器之一时，必须执行升级行动（6.1）；债务争议触发器：用户对债务金额有争议。证据为用户在第4轮明确对债务金额有争议。推荐在用户对债务金额争议时，立即停止对话，表达同情，告知正在转接电话，并触发“TransferGroup1”。该图片与上下文文档中Case 1 - 2026/6/5的Badcase修改前后对比及remark内容相关，用于说明违规情况及改进建议。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NTRkMmM4OTU5YTQyYjQ3MDMxNmUxYjMyOThkZDc4YTRfOWFjZGJkNjg4YmI5ZjUxNjNlYTg1YWYwMWFlMWFiZjBfSUQ6NzY0Nzg0OTQ0MTQ3NTQ5NzIxMF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
<column width-ratio="0.599221">
![图片为文档中“Conclusion&recommend”部分内容，总结了对“Dispute of Debt”升级触发器的优化效果。优化后，新规则明确用户质疑债务金额时，代理应立即执行升级操作，不得继续谈判。优化成功改善了目标行为，原案例中代理在争议后仍尝试谈判，现正确触发“transfer_to_human”函数调用。虽无token概率证据，但对话文本到工具调用的变化证实规则已遵循。此触发器无需进一步优化，未来测试应验证代理是否对其他如“Technical Issues”或“Severe Hardship”等触发器也立即停止谈判。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NTZhMTgxODljNTM4YjVkYTVmMmIwYzJlMzA3YTE5NGZfMzgxZGNiODczNWE0NjljYzNkNDM4M2NkOTEwOGQwNmZfSUQ6NzY0Nzg0OTg0NzA1MTYxOTUwOV8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
</grid>

#### System prompt diff：

```SQL
--- v0
+++ v1 Apply
@@ -162,7 +162,7 @@
 You must execute the Escalation Action (6.1) upon the **first mention** of any of the following triggers:
 
 - **Hostility or Frustration:** The user is angry, frustrated, impatient, or uses vulgar/threatening language.
-- **Dispute of Debt:** The user disputes the debt amount.
+- **Dispute of Debt:** The user disputes the debt amount. If this occurs, you must immediately execute the Escalation Action (6.1) and NOT attempt to continue the negotiation.
 - **Technical Issues:** The user mentions technical problems with their Grab account, app, or wallet.
 - **Overseas and unable to access/top-up wallet:** The user is currently overseas and unable to access their wallet. However, this does not apply to user whom was overseas and has already came back.
 - **Alternative payment methods:** The user enquires for alternative payment methods.
```



## Case 5 - 2026/6/5

https://botlab.airudder.com/#/bot-workflow?name=LLM_MY_PAX_Multilingual_PTP100_Prod_PAtest&type=Outbound&language=English&country=Malaysia&subLanguages=English%2CMalay%2CChinese&id=7850&isActive=1&chatId=53247

#### Badcase 修改前后对比：

<grid>
<column width-ratio="0.416385">
![图片展示的是一个AI bad case示例，内容为助理回复客户 addCriterion“Bad case”勾选框。分析指出违反了规则：若用户保持沉默（即无语音或响应），重复上一句并等待用户响应共3次。证据是助理重复了整个上一轮发言，而非仅重复上一句，这与沉默处理规则不符。推荐在用户沉默时，仅重复上一句并等待响应，最多3次。该图片与上下文紧密相关，是对Case 5中AI bad case的详细说明。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTZhOTIyMDA1YTg4YjJlODdjMmJiYWQ3ZThmZTk2YmRfZWQwY2M1ODNiMzFmOTdhMTgxMjg0Yzg0MzBkYWMxMTNfSUQ6NzY0Nzg1MzMyNzgxNjI2NDkxMV8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
<column width-ratio="0.583615">
![图片展示了Case 5中使用voyager-1.6-gemma4-26b-a4b-it模型的对话内容。第5步，助手以Grab Finance名义告知用户有5笔逾期贷款，总额750林吉特41分，最高逾期32天，并询问为何未付款。第6步，用户处于沉默状态。图片与上下文紧密相关，直观呈现了该case中助手的不当表述，即重复问候问题四次，未严格在第三次失败尝试后结束通话，且突然结束通话，与Badcase remark中提到的问题相呼应。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDE1ZjE5MWRjOGYyZjI0MTYxNDYzN2U3ZGRmNjBlYzVfYmNlMjRmMTE5OWU0YmNjMGNkMGFmN2UzYmM2YTQzYjBfSUQ6NzY0Nzg1MzYyNzgwNTY5OTAyMl8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
</grid>

#### Badcase remark：

1. The assistant repeated the greeting question four separate times across the timeline transcript instead of ending the call strictly after the third failed attempt.
2. Ended the call suddenly

#### Conclusion & recommend：

<grid>
<column width-ratio="0.409367">
![图片展示的是一个对话系统中关于的 addCriterion的界面，显示第5轮对话信息。界面左上角有“Turn 5”标识，右侧有“Apply”和“Delete”按钮。下方内容指出违反了规则：若用户保持沉默（即无语音或响应），应重复最后说的句子并等待用户响应3次。证据是助手在第一次沉默后，重复了整个上一轮而非仅最后一句，与沉默处理规则不符。推荐在用户沉默时，仅重复最后说的句子，等待响应，最多3次。该图片与](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Y2U5MGY0MDA2ZDIwZmY3YWE5MzY5YWIxZmU5OThlZTlfZTQ3MWNhNmI0ODc4MmIxYWFlNTliODU2ODRjNTcyMTBfSUQ6NzY0Nzg1MzM2MTMzMzQyMzI5Ml8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
<column width-ratio="0.590633">
![图片展示了Case 6中关于Badcase](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTRjN2E0MTY1ZjM3Njc5ZTZkMTI3ZDkwMTY2NjNiMmZfZWM0MGIyM2QyNDRlNDFlZTc1YTgzY2ExMjNiNzUzNjBfSUQ6NzY0Nzg1MzQ4NTI0NTMzNjU1MF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>

#### System prompt diff：

```Python
--- v0
+++ v1 Apply
@@ -111,7 +111,7 @@
 
 **4.3 Silence Handling**
 
-If the user remains silent (i.e. no voice or response captured), repeat the last said sentence and wait for the user's response a total of 3 times, if still no voice nor response, end the call <dialog-end>.
+If the user remains silent (i.e. no voice or response captured), repeat ONLY the last said sentence and wait for the user's response a total of 3 times. If still no voice nor response after 3 attempts, end the call <dialog-end>.
 
 **4.4 Exception Handling**
 
```



## Case 6 - 2026/6/5

https://botlab.airudder.com/#/bot-workflow?name=LLM_MY_PAX_Multilingual_PTP100_Prod_PAtest&type=Outbound&language=English&country=Malaysia&subLanguages=English%2CMalay%2CChinese&id=7850&isActive=1&chatId=53475

#### Badcase 修改前后对比：

<grid>
<column width-ratio="0.298531">
![图片展示的是LLM badcase根因分析中的一次AI bad case示例。内容为AI助手回复，称太棒了，感谢配合，确认对方将在今天偿还全部750.41令吉，并提醒为Grab钱包充值。还询问是否还有其他帮助。下方分析指出违反了货币金额表达规则，证据是助手未按要求用整数部分加“ringgit”、小数部分加“cents”格式表达，而是使用了数字格式。推荐改为用中文表达金额。该图片与上下文紧密相关，是对Case 5-2026/6/5中AI bad case的具体呈现与分析。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Yzc0NTYzYzliMzRiZTM3YTgyOTZkYzA2Y2M0YzAzZDRfZTMyMTE2ZDBjMmU3NjNjZTE4YWIzNDM4ODdlZTZmZTNfSUQ6NzY0Nzg1NjI1NTIxNDMyNDk0N18xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
<column width-ratio="0.701469">
![图片展示的是LLM badcase根因分析中agent测试的Badcase修改前后对比内容。在“Assistant”环节，修改前显示“太棒了，非常感谢您的配合。请确认您将会在今天，也就是五月二十六日，偿还750.41令吉。请记得及时为您的Grab钱包充值，以确保还款顺利完成。请问还有什么我可以帮您的吗？”，而修改后该环节被标记为“Bad case”。图片与上下文紧密相关，直观呈现了Badcase中agent回复内容的示例，辅助说明Badcase的整改情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmE1MzYwZGI3MDllOTBhYjU5Y2E3ZTFjNzg0NWI2YTZfMDI0YmI2NjQ0M2IxOWY5MGI4NTFiM2FmZTRiN2EyNTVfSUQ6NzY0Nzg1NjI1NjkzNzgwNzA0N18xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
</grid>

#### Badcase remark：

1. The opening line should have included the name Molnar (e.g., "Hello, my name is Molnar from Grab Finance...")
2. Amount should be accurately verbalized to reflect this precise breakdown (e.g., "七百五十令吉和四十一分")
3. the assistant must not say "today" (今天). They must explicitly say the calendar date: "五月二十六日"

#### Conclusion & recommend：

<grid>
<column width-ratio="0.388773">
![图片展示的是LLM badcase根因分析中agent测试的系统提示差异内容。画面中显示“Turn 9”及“Apply”“Delete”按钮，核心内容为违反规则：以整数部分作为整数，后接“ringgit”，再用“and”连接小数部分作为整数，后接“cents”的货币表达规则。证据指出助手未遵循货币表达规则，使用数字格式“750.41 令吉”而非“41 cents”。推荐将金额表达为“seven hundred and fifty ringgit and forty-one cents”（中文：七百五十令吉四十一分）。该图片与上下文关于LLM badcase的分析内容紧密相关。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDk0MzE1MzFkNDVkOGEwZDkzNTJiOGU4OTZlZjhhZjRfNDYwOGExMWE5ZTlhM2Y2MjMyZDEyYTg0NzAxNGU3YWRfSUQ6NzY0Nzg1NjMyMjA2MjU4NDc5NF8xNzgyMTIyNzc2OjE3ODIxMjYzNzZfVjM)
</column>
<column width-ratio="0.611227">
![图片为LLM badcase根因分析中agent测试的结论部分。内容指出系统提示在2.2节更新，要求中国回复采用特定的数字表达结构，如\[整数\] \[货币单位\] \[小数\] \[分单位\]，并提供示例防止使用小数。目标行为在具体金钱表达规则上未改善，尽管助手按要求使用了日期，但未将“750.41令吉”准确表达为所需中文结构，仍保留小数格式。建议用具体示例或明确禁止使用阿拉伯数字或小数点来回应中文。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGM1NzlmYWFjZWE5MzNjYWE2NjY4NWU3NmFiOGFlNzJfZjJkZTg2NjIyMTNjNmFhNWY4ZWQ5NzFhMDlhYTMzNTBfSUQ6NzY0Nzg1NjMxODkyMTI5Njg1OF8xNzgyMTIyNzc1OjE3ODIxMjYzNzVfVjM)
</column>
</grid>

#### System prompt diff：

```SQL
--- v0
+++ v1 Apply
@@ -34,7 +34,7 @@
 
 **2.2 Verbalization & Articulation Rules:**
 
-- **Money**: Articulate monetary amounts by verbalizing the integer part as a whole number followed by "ringgit," then connect it with "and" to the decimal part verbalized as a whole number followed by "cents," omitting either part and the connector "and" if its value is zero.
+- **Money**: Articulate monetary amounts by verbalizing the integer part as a whole number followed by "ringgit," then connect it with "and" to the decimal part verbalized as a whole number followed by "cents," omitting either part and the connector "and" if its value is zero. When speaking in Chinese, follow this structure: [Integer] [Currency Unit] [Decimal] [Cent Unit] (e.g., "七百五十令吉四十一分").
 - **Phone Numbers**: 03 9388 2222 must be articulated as "zero three, nine three eight eight, two two two two." with appropriate pauses in between.
 - **Dates**: The first time a date is mentioned, it should be fully verbalized. For subsequent mentions, the year can be omitted for naturalness. 
 
```
