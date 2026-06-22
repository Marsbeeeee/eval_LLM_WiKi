# [0519/0521]Customer Service judge 模型 badcase 记录分析

## 总结：

可修复/不可修复比例：11/11

当前 badcase 记录分析包含 6 个生成模型：H200_01_fc_9010、google/gemma-4-31b-it、voyager-1.6-preview-run27m4a8b4-r3、qwen/qwen3.6-27b、qwen3.5-plus、gpt-4o-mini，3个 judge 模型

**nextstep：**

- 先修复当前测试出来一些可修复问题
- 然后拿一些新数据/旧数据再测试，可适当增加数据量，防止pipline中发生的数据损耗
- 重点关注有没有新问题产生和已经测试出来的问题是否得到改善
- 得到的数据可再建立表格，统计准召率



### 不可修复

- Gemini 在 criteria 指令遵循上的表现更好，但是对于语言的评测更倾向于直译的表达 - 风格偏好
- Gpt 总体表现比较稳定，但是 gpt 对于一些 key guide 提到的指令，会要求一定要表述出来 - 注意力过剩

### 可修复

- 其他 badcase 就是一些 remark 有微小错误，还有 system prompt 内容有歧义导致的，导致 key guide 理解错误，和 human 难以对齐
- 还有一些 human 和 judge 模型无法对齐的分数，主要是人类有自己的语义理解，但是可能目前模型无法达到这种程度
- Qwen 在一些对话中，虽然是遵循了 criteria 但是会误以为 should criteria 是 must  - 理解问题，Keyguide调整



## GPT认为一些 key guide 中提到的规则一定要在对话中表述出来

![图片展示的是一个客服对话记录界面，记录编号为1，对话文件名为customer_service_2903_2_vicayi_hu_4.jsonl。关键信息包括：模型为gpt-4o-mini-2024-07-18，得分2（GPT 1 | Gemini 2），用户提问为“Sorry, your information dose not match with the order”，系统回复“Sorry, your information dose not match with the order. Goodbye!”。下方有“Key Guide (JSONLReview'review)”和“judge 3.6 plus”部分内容，其中“judge 3.6 plus”中Model为gpt-4o，其判定结果为“1”，判定理由“response does not mention the order holder explicity, so it fails the MUST 1 requiring explicit indication that the user's identity does not match Jane, though it does politely and the conversation and audit further processing”。该图片与上下文关于客服对话中关键规则表述的 自动生成](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmYxZTM4YzM2OGIwYTU3Zjc2NmU5N2MyMjczZDVhNzNfM2UxNGQ2NjAzNDk2NWE4NzAxMDQ0MjU5YmRmNzQyZWVfSUQ6NzY0MTQ2MzkyMjQ0MDQ1NzE4OF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

Case 1：

GPT 给1分的原因是，他认为 key guide 里面 “The assistant should clearly state that the user information does not match the order holder (Jane)” 应该在对话中强调 order holder 是 Jane，但在真实语境下其实不一定要表述出来



![图片展示的是一个客服对话记录界面，包含Source_File、User、User Prompt、System Prompt、Key Guide等内容。其中，User Prompt部分显示用户回复“我实际上不需要优惠券”，System Prompt部分有 GPT给出回复“Fine, we can offer a partial refund as a gesture of goodwill.”。Key Guide部分详细列出了MUST 1和MUST 2两条规则，MUST 1要求明确承认用户未使用优惠券并过渡到提供部分退款，MUST 2要求回复保持专业、同理心且不提前提供全额退款或结束对话。该图片与上下文关于GPT对key模型中关键指导原则在对话中表述要求的讨论相关。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODU2NDk2OThkNWViOTg1NWQwOGZiNDMxZGU1NDljODZfMTU2MDBlZjJkNTgwYmMwZTAwZThjN2YzYzNhYjJiMzRfSUQ6NzY0MTQ3MDUyNzI0NDI5MTAyNV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

Case 4：

Gpt 认为依旧要把 key guide 内提到的 partial refund 的详细内容表述给用户



![图片展示的是一个客服服务相关对话记录界面。上方显示Source_File、Turn_Index、Model、k_correct、Claim等信息，其中Claim为3。下方是用户和助手的对话内容，用户表示不需要成为会员，助手询问是否还有其他需求。右侧有系统提示、Evaluate内容、Key Guide、judge 3.6 -plus、judge 3.6、judge 3.1-pro addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MWU0ZjY5MjA3MjgxMWMwY2FhMDliNjg2M2Y1ZTZjMjFfNzY3OWY4MGNkYzNhMTFmMWJjZDUzZjhlMzJjZDgwYzRfSUQ6NzY0MTQ4MTQyNzE4OTU1MDA1NF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

Case 9：

Gpt 认为 MUST 1 要求先明确承接“用户不想成为会员”这件事，evaluate 里面回复是：That's perfectly fine!...  
它有礼貌、也问了后续帮助，但没有点明“不办会员”。



<grid>
<column width-ratio="0.679935">
![图片展示文档中“\[0519/0521\]Customer Service judge模型badcase记录分析”文档，展示了一段judge gpt - 5.1的评论。评论指出Model 1未明确承认或尊重用户在继续前不想成为会员，违反了MUST条件；Model 2虽满足条件，但仅轻微展示了温柔、礼貌的语气方向。该图片与上下文紧密相关，是对Turn 20生成对话中用户表示不想成为会员这一情况的评判反馈，体现了judge模型对对话规范性的判断标准。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDZjMjU1MGU0ZDM1MGFjYjdjYWIwMzM1ZWFlZTU5MjhfZTE0MWUxNjA3OTU1NmI3YzQ1MjMzYTJiODYyNGE2YzdfSUQ6NzY0MTkxNDgyNzkxNDU0NjE1MF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.320065">
![图片展示的是一个客服对话场景中的关键指导（Key guide）。背景为灰色，左侧有“key guide”标识，右侧是指导内容。指导指出，客服是电子超市会员客服，处理积分相关问题，此时用户在第6步已拒绝成为会员，流程要求询问用户是否需要其他帮助。指导明确要求客服必须明确承认或尊重用户不想成为会员，且应明确询问用户是否需要其他帮助，保持温和礼貌的语气，避免](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YmJiZjMxMzJiZjIzNGEyMTEzZTQyYWM5OTAyMThhZDhfYWQxYTgyMGY3YjEyN2ExNWI2MWFjNTY0YTQ1NTlkYjBfSUQ6NzY0MTkxNTA1NDg0NTQ2Mzc3Ml8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=534&dialogueId=13747

Turn 20：

两个生成对话，Key guide 有提到 “Clearly acknowledge or respect that the user is not willing to become a member before moving on”，gpt 读取到这一条 criteria 认为应该在对话中明确提到 user 表示不想成为会员



<grid>
<column width-ratio="0.416245">
![图片展示的是一个名为“key guide”的 - key guide”的内容，包含](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTJjYzc4ZTA4OTk4YzE5ZDQ4ZjBmZGYwYmFmZGYxMzhfZDcxYTNkMThmZTU0MDcwMzAxZDMwMzg4YzhjYTgzNmFfSUQ6NzY0MzcwMzEyNjc2OTgxNDQ3MF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.583755">
![图片展示的是一个](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzQwMThmMWU5M2JmZmViZTJkMzk3MDQ3OTkwYTdlMWZfYjY5MDA2ZTE3MzFlZWUzMGE4YjMzNDFlZDQ0ODQ4ZDVfSUQ6NzY0MzcwMzEyOTkwODk1NjM1OV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15900

Turn 14:

当前轮次，gpt 认为 "Certainly, I can help with that" and implying direct assistance, which conflicts with the requirement not to answer the inventory question and to only transfer. ”，这个打分理由就是模型自己语义理解的问题



## 不同 Judge 模型语义理解的偏差

![图片展示的是一个对话记录界面，对话ID为2，涉及用户与客服的交流。用户在第16轮次表示“that’s acceptable”，客服回复“我们很高兴解决了您的问题，祝您有个美好的一天”。下方有系统提示、关键指南、judge qwen、judge gpt -1.3 -pro -preview等信息，其中judge qwen给出3.6分，judge gpt -1.3 - -pro -preview给出5分，还标注了模型、版本、时间等。该图片与文档中对不同Judge模型语义理解偏差的分析上下文相关，用于呈现具体对话及评分情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZDFmYWM4MjhkMTY0OWU3ODY4MWQ1YTcwZDc4NDk5N2JfYjYwNWExYTE3YTU3MDEwMjNkZTVhYjgwNmQ5NzY5MjVfSUQ6NzY0MTQ2NTg1NTI5MzAwMDg5N18xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

Case 2：

当前有歧义的点是，生成的对话有提到 “I will apply the 10% ride voucher to your account. If you have any further questions or need assistance”，gpt 和 qwen 都认为这句话是开始了一个新的动作，但是 gemini 可能是通过整体语境判断认为这是礼貌结束语



![图片展示的是一个对话界面，包含human1、qwen、gpt、gemini四人评分及Metrics信息。human1给分3分，qwen和gpt给分1分，gemini给分2分。Metrics部分显示，系统回复“我看到该账号尚未注册，为了查询您的会员详情，请提供您在注册时使用的手机号码。”下方有“Translation”翻译内容。该图片与上下文紧密相关，是对文档中Case 2中human1、qwen、gpt、gemini对当前轮次对话评分及Metrics信息的呈现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDkxMzRmODkwMzgxNzM0MGQxNTlmZTE1ODY4ZDYxOTFfMjhlY2IzOTRmN2YwZmI0Zjc1YzM5ZjU3NjY3YWMwYzBfSUQ6NzY0MjIyMjczNDk5MTEzMzg5M18xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

<grid>
<column width-ratio="0.435033">
![图片展示的是一个对话系统中关于用户提供的手机号未注册的反馈记录。其中，generate_4模型指出这违反了必须明确说明之前提供的手机号未在超市系统中找到/未注册的条件，因为仅说明账户未注册而未提及提供的手机号状态。generate_5模型则认为所有必须条件已满足，且明确指出号码未注册，礼貌地再次询问。该图片与上下文紧密相关，是对当前对话轮次中用户提供的手机号未注册情况的模型反馈分析，体现了不同模型对语义表达的](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NmZhMGM5Y2MzMjYyMWIxNzQ2OWY5NTJlMmFkOGQ0ZDlfNDIwOWUxNGZjZDYyNDYyZGRhMmM2NzFkOGNlYTQ4ZjlfSUQ6NzY0MjIyMjczNTU4Njc0MTQ1OF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.564967">
![图片展示了对话中的一段关键信息。在Turn addCriterion\]Customer Service judge模型badcase记录分析\[heading2\]不同Judge模型语义理解的偏差\[content\]\[block_sep\]Case 2：\[block_sep\]当前有歧义的点是，生成的对话有提到“I will apply the 10% ride voucher to your account.If you have any further questions or need assistance”，gpt和qwen都认为这句话是开始了一个新的动作，但是gemini可能是通过整体语境判断认为这是礼貌结束语\[block_sep\]\[block_sep\]\[block_sep\]<qa:image></qa>\[block_sep\]https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15898\[block_sep\]Turn 8：\[block_sep\]当前轮次human给了3分，qwen和gpt认为在语义表达上其实是不够确切的，认为之前没在系统里找到用户提供的手机号，和确认该手机号未注册账户是有一定程度上的不同。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDVlMGJjN2ZmZDdjYzEwN2FlOWFlYjQyZWU3NjY5MWVfYTBhOWEyYTI3N2MxNDJlNDhlODVmMTEyOTM0ODQ0ZTVfSUQ6NzY0MjIyMjczNzUyODEzMDUyN18xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15898

Turn 8：

当前轮次 human 给了3分，qwen 和 gpt 认为在语义表达上其实是不够确切的，认为之前没在系统里找到用户提供的手机号，和确认该手机号未注册账户是有一定程度上的不同。而 gemini 认为该回答满足所有必须条件，只是再对话中缺乏安慰和道歉的语言



![图片展示的是一个对话交流界面，左侧显示human1、qwen、gpt、gemini的评分，分别为3、2、2、1。右侧是gemini的回复，内容为“我理解您的顾虑。但是，如果没有您的手机号码或账户名称，我无法找到您的账户信息来查询您的会员积分。如果您有任何其他问题或需要其他帮助，请告诉我。否则，祝您度过美好的一天 addCriterion>](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MGNiYjJmNzQwY2Q4ZGNhNGU1NDc3MTQ2Mzc0YTFiNzlfMzAyMTE4MzViZTJjNzFkMjg3MmU3ZWQwNmRkNmVlN2RfSUQ6NzY0MjI1Mjg5NjQyMzcwOTY0Nl8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15901

Turn 16：

当前轮次由于我们标注的过程中发现说，system prompt 里面只写明说 end the conversation 其实是不够明确的，然后导致说，key guide 认为需要干净利落的结束对话，而不加其他多余的东西。然后 qwen 和 gpt 认为对话结束的不够干净，gemini 认为当前对话继续了工作流，问用户还需不需要其他帮助



## human 评分和 Judge 模型评分差异大

![图片展示的是一个对话界面，呈现了不同模型（qwen、gpt、gemini）的评分情况。qwen、gpt、human1的评分均为2 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OWNkMjlkYTc5YjQ3MzljMjFkZGE0YzMyOGJlNzQ3ZmNfNzZmNjA0MzgxOTMwNjRlZWYxMDA0NTFmZDJiZmE3NDBfSUQ6NzY0MTkxNjI5MTE5Njg5ODI3Nl8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=534&dialogueId=13747

Turn 10：

当前轮次，human评分给出1分，可能原因是认为模型生成对话，提及与当前对话无关的问题对整个对话语境有较大的影响，但是模型一致认为当前对话没完全遵行 key guide 提出的 “SHOULD] Keep the response concise and focused on addressing the identity question without adding unrelated process steps.” 但对于对话重点没有影响，所以给出 2 分



![图片展示了一个对话评分界面。左侧有qwen、gpt、gemini三个模型评分框，均为2分，下方human1评分为1分。右侧是对话内容，其中一段文字为“ Yes, the supermarket currently has discounts on fruits every day. You can view the specific details in our app.”。图片与上文对应，呈现了Turn 10轮次中human评分和Judge模型评分差异大的具体打分情况及对话内容，体现了human认为模型生成对话提及无关问题影响语境而模型认为对对话重点无影响的分歧。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NTE3YTRkNTZhMzQyZGJhMDI3NGI2MmNkZjc2ZGY5M2FfYmRlYzY5NTdkYTlmZWIwZmMyOTBjY2M4YjVkMmZiZmFfSUQ6NzY0MTkxNzUwNTQ1OTcxOTM2OV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=534&dialogueId=13747

Turn 8：

当前轮次，human评分给出1分，可能原因是认为模型生成对话，提及与当前对话无关的问题对整个对话语境有较大的影响，但是模型一致认为当前对话没完全遵行 key guide 提出的 “The response SHOULD avoid unnecessary steps such as asking for phone/account details or calling tools, and may optionally offer further help related to promotions or member points.” 但对于对话重点无明显影响



<grid>
<column width-ratio="0.416245">
![图片展示了key guide内容，背景为白色。左上角有“KG”标识，下方是“CONTEXT”部分内容，说明用户询问产品库存，超出会员积分流程，需转至其他客服。右侧有“key guide”字样。下方“EXPECTED”部分要求以礼貌、温和的英语回复，使用指定用语转接，不直接回答库存问题或使用工具，应表明用户需解决其他问题。该图片与上下文紧密相关，是对话中模型需遵循的指导原则。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTc1YzA2Y2E4MWU0NDg0NDZhZThkYzViY2MzNDRkZTlfZTAwN2EzZDRlZTkxNmQ4ODg2ODE0YWJkMDc3MDQ1OThfSUQ6NzY0MjI0MzYyNzU3NTY3NjA5OV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.583755">
![图片展示的是一个对话界面，显示了模型生成v](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NmQ4OWYyNmU4NmQ4NjkyYTg0YzBjNDNiYzY5ZTJmMjNfMzk4ZjViYjEzYzc5ZTU0MmVkMmE0OTk2NmMwMTA1NDFfSUQ6NzY0MjI0MzYyNzEwODY0OTkxMl8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15900

Turn 14:

当前轮次，key guide 里面有明确要求 ‘include the key phrase "Transferring to other customer service, please wait" or an equivalent that clearly conveys the same meaning.’ 所以 gemini 和 qwen 都给出了3分，但这也是因为一方面可能 remark 没有强限制要输出固定话术，再加上 key guide 生成模型没有对于强制话术完全理解，并非完全 judge 模型能力问题。所以 human 评分为1，但是 qwen 和 gemini 给3分



![图片展示的是一个对话界面，左侧显示有有有human1、qwen、gemini等评分，其中human1评1分，qwen评3分，gemini评2分。右侧是对话内容，显示“More than welcome to bring a friend. Can the assistant tell her to bring a friend, and we encourage it.”下方是Metrics，显示“是的，您绝对可以，而且我们鼓励您这样做。”并有评分52。该图片与上下文分析human评分和Judge模型评分差异大的内容相关，呈现了评分及对话内容，辅助说明评分情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzY1NTU1M2JlYTllY2E2MjdiZjUxY2JiNTJkNzM0NmRfMzJmZjgzZDZjYmFjNjVhN2VjMjUyNDNlZjg0NjllOGNfSUQ6NzY0MjI2NDg1MTgwMDY1Njg2OF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15909

Turn 6：

当前回答，对于 judge 模型来讲是基本符合 key guide 给出的多条 criteria，但是对于 human 来讲可能有点太简略，不适合作为当前场景的对话



## Gemini 独特语言风格偏好

<grid>
<column width-ratio="0.439917">
![图片展示的是“generate_gpt-4o-mini_2”任务的对话内容界面。对话中，用户以“Hello! I'm David, the customer service representative of e-supermarket. I'm happy to help you today. How can I assist you?”开场，表达自己是e-supermarket客服代表，乐意帮助用户，并询问需求。下方有“Translation”区域，显示了中文翻译。该图片与文档中Gemini独特语言风格偏好分析上下文相关，用于说明Gemini客服代表的开场白风格。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OWM3ZTIzOWVmYzAwMjUxMjU1ODkyYjdhMWQ3YzgwYjJfMDQyNTFkMDk3OGNlMGM2NzI2Y2E5M2U1ZTVjOTczODVfSUQ6NzY0MjIyNDg3NDgxOTE3NzY3Ml8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.560083">
![图片展示的是 addCriterion()函数的代码示例。代码中定义了`](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmU1MDJjYTkxMGNlZTE3MWE1YjFhNDY0ZmY2YjAyMzRfOGY2MDI2NzM0N2JmMzVlNmQxY2Y3NTEzM2ZmZWI1YmVfSUQ6NzY0MjIyNzAwODA4NjI2NDc4Nl8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15898

Turn 2：

当前轮次中 gemini 的 remark 有写到 “The response violates the MUST condition to explicitly convey willingness/happiness to help the user. ”，然后我去对比了当前轮次 gemini 觉得是满分的回答，发现其中明显不一样的点是满分回答有明确表达 “很高心今天为您服务”，所以我认为这个是gemini自己的语言风格偏好



<grid>
<column width-ratio="0.476073">
![图片展示了Gemini 3.1 - pro - preview对一个对话轮次的judge结果。Model 1 - 6对回复的Remark依次为：满足所有MUST条件；违反MUST条件；违反MUST条件；满足所有MUST条件；满足所有MUST条件；满足所有MUST条件。其中Model 3和Model 5的 -](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGJiYjRmNjEyZTI0YTc1NTUzNmExODljNDZhMTlmYjJfNTVjODdiM2Q5NjQ2NGU4OTNlZTMxYTEzMzdmZWQzZDZfSUQ6NzY0MjI1NjU0Nzg1ODgxMjExNV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.523927">
![图片展示了一个对话评分界面。左侧显示human1、qwen、gpt、gemini等评分选项，其中gemini评分1。右侧是对话内容，用户询问是否能为5人预订12月17日6点的私人房间，有龙虾、牛排和鱼。gemini回复确认预订，还表示会安排生日惊喜。下方有Metrics（4）区域，显示评分结果，其中“Perfect”评分82。该图片与文档中对Gemini语言风格偏好的分析相关，用于说明其在对话中表达方式的偏好。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGZiMDdkNjQxNzRlZmY2OGViMjkxOWM1MWVjYTljYzVfNjQxMDE2MzIxZDMyYWY5OGQ0YzZjMWRmYjI0N2FmZWNfSUQ6NzY0MjI1NjY0ODMxMjIyODgzMF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15907

Turn 28：

当前对话为，gemini 认为对话内容没有很明确的去表明用户的预定已经被确认了，而是用了另一种 gemini 认为不是明确表达的方式，所以给出了1分



<grid>
<column width-ratio="0.545406">
![图片展示的是Gemini addCriterion中的一段对话内容。对话中Gemini询问用户是否可以带朋友参加活动，表示欢迎他们加入，并询问用户是否计划参加活动。该图片与文档中Gemini独特语言风格偏好相关，用于说明Gemini在对话中存在明确表达“很高心今天为您服务”的语言 自动生成的文本内容，以及在某些轮次中对用户预定确认表达方式的偏好，如认为对话内容未明确表明预定被确认而给出1分的情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MGVkMjNlZWNjZTlmMDY1MWViNWJhNGQzMTM1ZDJmMjBfMjY0ZmNjOWI3NjdlMzljZmZmODQzN2M0OTdiZGM3ZDBfSUQ6NzY0MjI2MjExOTU1MjE5MTcwM18xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.454594">
![图片展示的是一个对话界面中的一段文本内容。上方显示“H200_01_fc_9010@C0”及对话轮次“5”。文本框内有蓝色边框突出显示的英文对话内容，为“Absolutely, Julia. You are more than welcome to bring a friend along, and we’d be delighted to have them join us. Now, to help us with our planning, may I confirm if you’re planning to attend the event?”。下方有“Remark”和“Translation”区域，但未显示具体内容。该图片与文档中Gemini独特的对话分析上下文相关，可能是Gemini给出1分评价的对话示例。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZDZiYjI2OTQyNDY5ZTQwZWRiNTJhNDUxZGNmODM3NzFfNjkyOWNiZDM4NWY3MDQzMzhiMDQyMzM5NTlhZTNjZmFfSUQ6NzY0MjI2MjE4OTQ5MTE2MjMyMl8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15909

Turn 6：

Gemini 都认为 “fails to clearly convey encouragement using language aligning with the FAQ (e.g., 'we encourage it' or equivalent), instead saying 'we'd be delighted to have them join us'. ”，认为对话内没有明确清晰直译的表达出来 ‘我们鼓励’ 这个意思，感觉 gemini 可能更倾向于，一些直译的表达



## Qwen 模型对 key guide 理解错误

![图片展示 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YzQ2MmQzMzVjMDYyYzQ2OGZhYjVlZjM3ODVlYThiN2FfMGVlMDA1NTI3MzcwZTQwZjQ4ZjhjMTgxYTdkY2U3NDhfSUQ6NzY0MjIyNjI2MzU1Mzc1NjM1NV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15898

Turn 2：

这一轮次的回答，如果根据 key guide 的规则打分，其实 gpt 和 gemini 的打分是没有问题的，因为 key guide 里面是 “[SHOULD] The response SHOULD keep the content brief and limited to the introduction, avoiding premature questions about phone number or account details in this step.” 那这一条不满足，就是2分。然后qwen认为 “ it explicitly asks for the mobile phone number.” 是违反了must，但是其实must未提及这一条，这条应该是should。然后过早提及其他信息，在human看来也是比较大的错误，所以给出1分



<grid>
<column width-ratio="0.486161">
![图片展示的是一个对话界面，上方显示“Completions (2)”，下方有“generate_gpt-4o-mini_1”和“generate_qwen3.5-plus_”两个生成对话。其中“generate_gpt-4o-mini_”生成的对话内容为“Can you hear me? If so, please let me me addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTkyZTFhOGQzMTcyYmI4NGRkZjViMjcxYjgyMzQ1N2VfMWNkNGQ3NGNlMjlhZTAwZTk5NGNlMTI5ZjgxNDI3N2NfSUQ6NzY0MjIzMzIyOTQ0MjY4MjA2NV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.513839">
![图片展示的是一个对话生成界面模型的界面，模型为generate_qwen3.5-plus_3。界面中显示了4个评价指标（Metrics），内容为“Can you hear me? Would you like to participate in the VIP lottery activity? You just need to provide a number between 1 and 99.”，并有复制和删除图标。界面右上角有红色的“×”关闭按钮，以及绿色的“√”和黑色的“×”按钮。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzQ2NjBjYjYzYTcyNzhkNjdkOTc4NmJmYTUzNTNlN2JfNzc0YTdjMDAxZTQwNjM2ZTY1OThiMzNmMWEzZGIzZThfSUQ6NzY0MjIzMzIyNjI3OTU3MDYxMV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=534&dialogueId=13748  
Turn 18：

两个生成对话，qwen 给了1分认为其违反 “MUST condition to avoid progressing into membership workflow steps” 但是这个 rule 在生成的 key guide 内是属于 should 条件，所以 gemini、gpt 给2分是更加合理的



<grid>
<column width-ratio="0.627151">
![图片展示了一个对话评分界面及部分对话内容。上方有human1、qwen、gpt、gemini的评分框，其中human1和qwen评分为1分，gemini评分为2分。下方对话内容是ABC Beauty Salon员工向Julia致电，跟进美容活动邀请事宜。结合上下文，该图片用于说明在Turn 18的两个生成对话中，qwen因对生成的key guide内“MUST condition to avoid progressing into membership workflow steps”规则理解错误给出1分，而gemini、gpt给2分更合理。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NjNmMjcyMGE5YTdkYjFiYzlhNDE2MmFhODZhMWI0MzJfZmQ0NjY4Nzc5Y2I4OWZmZDdlMDMwZGNkMjBiNGMxMmVfSUQ6NzY0MjI1ODk1MDE1NDgwMDMxN18xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.372849">
![图片中展示的是Qwen模型对key guide理解错误的记录分析。图片呈现了多个模型对同一对话的评分及备注，如generate_gpt-4.1-mini、generate_qwen-3.6-max等等模型均认为违反了MUST条件，避免在该步骤讨论事件细节或询问是否参加，因对话中明确有明确询问Julia是否参加等。该图片与上下文紧密相关，是对文档中提及的Qwen模型对key guide理解错误情况的具体呈现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZjRjMTc2MmJjN2U0YWM5YTI3ZGI5ZjdiMDZkODdjMDlfZDk2MGUzM2RhYTVjMjM4YzMxYTJiOGVhNTgwZDYxZjdfSUQ6NzY0MjI1OTE4MzE2NTY4OTAyOV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15908

Turn 2：

当前 qwen 认为，模型不应该提及其他的问题违反了 must 条件，所以给出了1分。但其实在 key guide 中 “ [SHOULD] The response SHOULD avoid discussing event details” 所以 gemini 的评分更加合理，而不是直接打1分



## GPT 模型对 key guide 理解错误

<grid>
<column width-ratio="0.627151">
![图片展示的是一个对话评分界面，包含human1、qwen、gpt、gemini四组评分，其中gemini评分2分。下方是对话内容，由human1发起，内容为ABC Beauty Salon员工Julia的问候及对即将举行的美容活动的跟进，希望Julia能参加。右侧有“Add New Metric”按钮，下方是Metrics部分，显示了4个评分指标。该图片与文档中GPT模型对key guide理解错误的内容相关，用于呈现对话评分及关键信息。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YjUzZWJlNGE0NzcxZTE5NWY3ZDAwYTQ1M2U4NGY3MTVfOWU1NTk3NDQ5NjI2M2NiMDZkNTQwN2VlM2JkZDZiN2RfSUQ6NzY0MjI2MDE2NjI0Mjk5NTM5OV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.372849">
![图片展示了Qwen模型对key guide理解错误的记录。其中，generate_gpt-4.1-mini、generate_qwen-3.6-max、generate_gemini-2.0-flash等模型均认为响应违反了MUST条件，避免讨论事件细节或](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDY0NzJmY2ZmM2VhZjNjNWQyYjUwZDE3MDFkZjAxZDZfMmI5MTU5OWVhOGFlYzc3ZjRkZGZkYjJkMjM5ZTBkZTBfSUQ6NzY0MjI2MDE2NzAxODY5NTY0Nl8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15908

Turn 2：

当前 gpt 认为，模型不应该提及其他的问题违反了 must 条件，所以给出了1分。但其实在 key guide 中 “ [SHOULD] The response SHOULD avoid discussing event details” 所以 gemini 的评分更加合理，而不是直接打1分



## Remark 错误导致 key guide 错误

![图片展示的是一个对话系统界面，呈现了不同工具的评分情况及对话内容。左侧显示“Tool”下有“human1”“gpt”“qwen”“gemin”“assistant”等工具，其中“assistant”被选中。右侧是对话内容，当前轮次系统提示用户可通过手机应用查看最新信息，询问用户是否提供手机号码。下方“Remark”部分指出用户询问是否有近期促销，需告知超市每天有水果折扣等信息，需用户提供手机号码或账户名。该图片与文档中对当前轮次评分有歧义、remark错误导致key guide错误的分析内容相关。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTA4NmRiZDM0MmJhNzU1ODdlYmY2MTU2ODVjNTJiZDVfYzQ1YWJjOGM5NWQyMWViOWQ3MjE4M2VmZTU1ZDdjMjRfSUQ6NzY0MjI0NzAxOTI3MDMxMTEyNl8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15901

Turn 8：

当前轮次，核对过 system prompt 和上下文发现，当前评分有歧义不算是 Judge 模型能力问题。当前轮次 remark 错误写成要询问用户的账户名称而不是询问客户的手机号码，所以导致 key guide 里面有 “ask the user to provide their supermarket account name (not phone number)”，那当前对话因为问了可以提供账户名称吗？所以gpt 认为其满足must 条件，给了2分



<grid>
<column width-ratio="0.602083">
![图片展示的是一个客服对话界面，左侧为assistant的对话内容，显示其身份为客服David，ID为003，可联系应用验证身份，并询问用户是否能提供账户名称而非手机号码。右侧为human-annotation，内容是客服David询问用户是否能提供账户名称，还提供了英文翻译。下方是voyaer-1.6 - preview - run27m4a8...的工具调用结果。图片与上下文紧密相关，呈现了客服在轮次10时的对话内容及人工标注情况，辅助分析当前轮次remark错误导致key guide错误的问题。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjMxYTY2ZmZhYTQxMjQ0ZWZmNjIxNzgwNGQyYzM5NGNfYjIwMzBlYWQ4MjQxNDhhZDc5MTk3ZTI2MjE5YmIyYzhfSUQ6NzY0MjI1MDI0NDE5ODg3ODQyOF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
<column width-ratio="0.397917">
![图片展示了一段对话及评分界面。左侧是human1、qwen、gpt、gemini的评分情况，其中human1评分为3，qwen、gpt、gemini均为1。右侧是对话内容，显示用户询问账户信息，gpt回复需提供手机号码以验证身份，还提及会员积分查询需手机号。下方是Metrics部分，显示gpt的翻译内容。该](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=M2JhNWU5ZWYyNjQzOTYxNzc2M2Y0YmRhY2M5M2U5NDRfZGU1Y2NjMmIxZjc3ZGMwZWQxNGI4Y2U5YWIxODAzYzJfSUQ6NzY0MjI1MDkzODEyMDYwNDg2OV8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)
</column>
</grid>

Turn 10：

依旧是 remark 和 gt 冲突，但考虑到上下文 assistant 已经问过两遍手机号但未获得回答，认为当前轮次 gt 正确，remark 依旧是有问题，导致 human 评分受 remark 影响错判，但 key guide 受 system prompt 和 remark 指引还是生成了正确标准，judge 评分未受影响。



![图片展示的是一个对话系统评估界面。左侧显示了human1、qwen、gpt、gemini四个模型的评分情况，其中human1、qwen、gpt评分均为3，gemini评分1。右侧是对话内容，用户询问是否参加活动，系统回复“我明白您还不确定您的时间安排。一旦您决定参加，请随时给我们回电，届时我们将乐意为您提供帮助，感谢您的时间，祝您有美好的一天。”下方有“Remark”和“Translation”区域，分别标注了评分和翻译内容。该图片与文档中对当前对话remark有误导致key guide错误的分析上下文相关，直观呈现了相关对话内容及评分情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NzI4OWRmYmE4MzFlNWIyMDBmOWQ3YmY1ZmYyMThjMmVfMjMxN2NiZDNjZWQyZWM0MGVlZTRlODA0NmEzM2I4OWRfSUQ6NzY0MzcwNDU5ODczMzM5MzA5NF8xNzgyMTIyNzcwOjE3ODIxMjYzNzBfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=545&dialogueId=15910

Turn 12：

当前对话 remark 有误，上一轮 user 说的是不确定会不会出席，但是这边 remark 写的是用户不确定什么时候出席，导致说 key guide 生成内容有误，但就当前 key guide 来讲 gpt 和 gemini 认为其对话里面 “一旦您决定参加” 是弱化了，应该确定参加时间这个要求，所以给出了低分，而 qwen 则因为已经检测到对话中包含 assistant 理解用户还不确定时间，所以认为是满足所有 criteria
