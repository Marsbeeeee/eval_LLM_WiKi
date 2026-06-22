<title>[0528] customer service 全量 pipline case 记录</title>

测试集 id 626



# Badcase

### System prompt 歧义

![图片展示的是Airudder LAF平台界面，左侧为对话记录，显示用户Juli在询问活动日期相关问题。右侧是LLaM Tool区域，有“Edit Panel”和“Review Panel”，其中“Edit Panel”中显示了用户问题及系统提示，如“Juli is asking for the event date...”等。下方“Review Panel”有“Review”按钮。画面右下角有“Submit”和“Reset”按钮。该图片与文档中关于系统prompt歧义的案例相关，展示了对话及系统提示情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDhiY2YwYzJlYWUzMDI3MjJjMDQ2NmNkMjRmZTNmNmZfYjU0OWVjZTY5YTI5NDA5N2MxNjhiZTkwZThjNzUxNGJfSUQ6NzY0NDgxMDA4NzY5Mjk3OTQxOV8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19840

Turn 8：

人工标准是希望模型在回答完 faq 之后应该跳回主线，但是这一点 system prompt 里面没有提到，所以说 key guide 会认为跳回主线是 should 那导致模型评分为 2 分，但这不能算是模型能力问题，所以不作为 badcase



![图片展示了Airudder LLaP系统中客服服务全量pipeline case记录界面。左侧是System Prompt，包含用户信息、业务场景、关键引导等；中间是LLM Tool，有“Continue to Next Step”按钮；右侧是Edit Panel，显示对话轮次、用户和AI的对话内容，如用户请求提供账户名，AI回复需提供手机号等。最右侧还有“Continue to Next Step”按钮。该图片与文档中关于System prompt歧义的讨论相关，直观呈现了系统](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzI0M2MyNDE5ZGFkYjM5ODRmNTlkZmVkODcxZjdmYTBfYzY1NTUyOWNiMjA4YjRiZmNiNWE3ODJjNTc2OTlkNzFfSUQ6NzY0NDgzODY3ODg0MTcwNzcyOV8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19781

Turn 6：

根据上文 “I can't provide it” system prompt 里面可以有两种解释，所以认为其有歧义



![图片 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWYzNDIzODZkMTBkYzU3MzU3ZmYwNmQ4OTRmNzA2Y2NfOWVlN2U0NmVlZTUzMmVlMzJkMTBjOTg2M2NhYTRlZWFfSUQ6NzY0NDg0MTk2MTI5NjMzNDAzN18xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19782  
Turn 6:

根据上文 “I can't provide it” system prompt 里面可以有两种解释，所以认为其有歧义



![图片展示了Airudder LADF系统中客服服务全量pipeline case记录界面。左侧是对话记录，显示了用户与助手的交互，如用户询问无法访问账户详情，助手要求提供手机号等。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmQ2OTY3NmVmODllODhjNWIzZGQ5OTMzYTQ4NGM0ZDBfMTYyZWI0OTk4OWI3MzgxMzkxNTk3YmEzNmQxZmM3ZDZfSUQ6NzY0NDg0MzA5NjU5Njk1ODQyOF8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19783

Turn 6:

根据上文 “I can't provide it” system prompt 里面可以有两种解释，所以认为其有歧义

当前直接导致 Turn 8, 10, 12，回答有歧义



### Remark 不清晰

![图片展示的是Airudder LADF平台界面，呈现了客服服务全量pipeline case记录中的一次对话。对话中，用户询问超市是否有促销活动，客服回复有，还询问用户是否是新用户。界面左侧有用户和客服的对话记录，右侧有LLM Tool编辑面板，显示了用户问题、客服回复、备注等信息。右下角有“Review”和“Submit”按钮。该图片与文档中关于客服服务全量pipeline case记录的上下文相关，直观呈现了对话场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjYxOTU2NWE4YmU2NDVjYjNjOWNiZWRkZGQ4MmUyZmZfNWNlOWIxYTNhOGVkODNhYjQzY2UyMTM1ODcyMmUwNjhfSUQ6NzY0NDg0NTU1MTg5MzEwNTg1M18xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19785

Turn 8:

当前轮次，remark 没有明确标注说，应该返回主线，导致 key guide 认为不可以提及其他步骤，所以模型为1分



### 模型 feature 标准的不确定性 （turn-aware）

![图片展示的是一个对话系统界面，呈现了系统prompt、LLM Tool、Edit Panel、User、Assistant、Agent等部分。左侧是系统prompt，包含Step 1 - Greeting and Introduction等内容。中间是LLM Tool，有“Next (L)lM Tool”按钮。右侧是Edit Panel，有“Next (E)dit Panel”按钮。下方是User、Assistant、Agent的对话记录，显示了用户与助手的交互。右下角是Agent的评价，显示“Not useful for learning”。该图与文档中“模型feature标准的不确定性（turn-aware）”内容相关，展示了对话系统中各组件及交互情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzE4NjFkMmQ3YzNjOTFhMzA3Mzg0MjdiMzliZGU4ZjhfMTE5ZjhkZjgxOTJjMzNlZjEwYzY4YzdhZjQwNmU1YWFfSUQ6NzY0NDgwOTM2NDgzMDA2MzU1OF8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19841

Turn 16：

当前也是，turn-aware 的不明确性，模型是将两步并为一步了，但是人工可能认为这边不应该两步并一步显示



![图片展示的是一个对话界面，左侧为系统提示，包含多个步骤，如询问用户是否能 addCriterion图片](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGZlZjNkMTQ5YTk0MWJjNWViZTE4OGVkM2M1ZmY2MzNfY2Y3ZGM0ZDlkM2FmYmU3NTdjMWYwNzJkMmE5ZWY3ZmJfSUQ6NzY0NDg2Mjg1NTM4OTUxNDcwNF8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19787

Turn 18：

Key guide 认为当前 turn 不应该进行下一步，人工觉得可以



### Key guide 引用不必要 system prompt 内容

![图片展示的是一个客服对话界面，左侧为系统提示，包含“System Prompt”等内容，右侧是对话记录。对话中，用户提到手机用于注册e-supermarket账户，客服询问是否能提供注册时使用的手机号，用户回复“能”，并表示已告知。界面右侧有“Edit Panel”等编辑选项，下方有“Response”输入框，显示客服回复“Sure，I can help you check your membership points...”，并有“Submit”按钮。该图片与文档中客服处理会员账户相关问题的上下文对应，展示了客服与用户对话及系统提示情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YWFkODM5YTFkM2YwODliYjg2NjUxMzUzODM0ZWJiZGFfZGYwMTVlZjM3Y2I5Zjg5Y2E0ZDFmY2M1MGNmMzM3MDRfSUQ6NzY0NDgzNzE0MjU0NDczMTM2MF8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19781

Turn 4：

Key guide 要求 “[MUST] The response MUST clearly ask the user to provide the mobile phone number used to register their e-supermarket account (or the phone number linked to their supermarket account).” 认为这里要表明自己身份，引用了 system prompt 内容



### Key guide 注意力过剩

![图片展示的是一个客服对话界面，左侧显示用户与客服的聊天记录，用户询问“Who is this agent?”，客服回复“Hi, I am customer service XiaoLan”。右侧是LLM Tool界面，显示用户提问“Can I call you your mobile phone number?”，客服回复“Of course, I can call you at your mobile phone number.”，并有“human”角色、内容、备注等信息。该图片与上下文紧密相关，直观呈现了客服与用户在特定场景下的对话及系统处理情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDVlZjYxNTI3YWFiNzkzMDhiY2ViZDQ2N2YxYTc2M2RfZjcwZTVmOWQ3OWZmY2YwOWZmNTQ1NTRjMmE4ZGRlMmJfSUQ6NzY0NDg1ODk2NTY4MDMxMTUwNl8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19785

Turn 12：

Key guide 提到 “[MUST] Clearly state that the user's account information cannot be located or checked without the registered mobile phone number. ” 模型注意力过剩认为一定要表达 “我不能获取信息” 这个意思



![图片展示的是一个客服服务界面，左侧为LLM Tool区域，显示“Assistant”和“User”对话，其中“Assistant”询问用户是否能提供手机号码，否则无法获取账户信息。右侧是LLM Panel，显示“Assistant”回复“Can you tell me your mobile phone number? Without it, I can't locate your account information.”。画面中还突出显示了“\[MUST\]Directly ask again for the mobile phone number used to register the e-supermarket account.”等关键指导内容。该图片与文档中关于客服服务中关键指导引用不必要system prompt内容的上下文相关，展示了实际客服对话场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OThhMjc4NGVmMWViZTk2NWUzNTQ4NGRlYTFkODlkNzRfZmMxMzJhZjhjYzI3NjUwNGJlYjU4MzIyYWQxNDgxMzlfSUQ6NzY0NDg1OTA0OTI2Mzk5MjAyNF8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19788

Turn 12

Key guide 认为需要明确表示 [MUST] Explicitly state that without the user's mobile phone number the system/assistant cannot locate or access the customer's account information.



![图片展示的是一个系统界面，界面左侧有多个对话相关内容，包括不同角色标识及对话语句。中间部分是“LLM Test”区域，有“Task info”等内容，显示用户询问如何提升优先级及相关回答示例。右侧还呈现了类似上下文等信息。该图片与文档中关于customer service全量pipline case记录相关，对应“Badcase”中“Key guide引用不必要system prompt内容”部分，通过展示界面内容辅助说明对话情况及问题所在。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTgwZTFkNTFkYWQzMTNjMzdkMTBiYTBjOTA1ODI3OGNfYzUzYWE4YTc1OWVhNmYyNmU3ZjI2NDRlY2Q1ZjI3ZTNfSUQ6NzY0NDg2MDY1NDQyNDU3NTE4N18xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19788

Turn 26：

Key guide 的 should 条件有点过于苛刻，且 remark 和 system prompt 没有明确要求要写出来，认为是 key guide 注意力过剩为主因



![图片展示的是Airudder LASP平台界面，呈现了对话记录、LLM Tool、Edit Panel等部分。对话记录中，User询问餐厅预定人数，Assistant回复“Sorry, I still have no ideas about the number”并建议稍后联系。LLM Tool显示了对话内容及生成的LLM Prompt。，Edit Panel有“Edit”按钮。界面右下角有“Reject”和“Accept”按钮。该图片与文档中关于模型注意力过剩的讨论相关，展示了对话中模型的回复情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MmI1ZjIyYWNhZGVmYjRiY2YxYmI2NDBhNGI2MzQ2MWRfYmUzMWYyZTUxZWU5MmRiNWYwNDRiN2QyN2Q5NzExMjNfSUQ6NzY0NDg4NDE0MTc5NjQ2MTc4M18xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

Turn 16：

Key guide 注意力过剩，认为一定要表达 “没明确人数不能确定预定”



![图片展示的是一个对话界面，左侧为用户与助手的的对话，中间是LLM Tool界面，右侧是助手回复及评论。用户询问“没明确人数不能确定预定”，助手回复“您有几位特别想预订的菜品吗？”，并有“Model generated 6.11min”提示。界面下方有“Review Nodes”“Reject”“Submit”等操作按钮。该图片与文档中关于Key guide注意力过剩，认为一定要表达“没明确人数不能确定预定”的内容相关，直观呈现了对话场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmViMjM0NzQ3NjhlN2RhMjZjYjVhYzM1MjMwNzVlMmVfOTUwZGMyOGNiN2M2ODkyNTkzYjRjY2RlNmE2ZmIwYTRfSUQ6NzY0NDg4NTAyNzIxMzU5MzgyMF8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)





### 模型注意力过剩

![图片中展示的是LLM Tool界面，左侧为对话记录，显示用户与助手的交流内容，如用户询问“我怎么才能”，助手回复“您可以在检查详情和组合时”。右侧是模型生成的对话，包含生成的prompt、LLM Tool的生成结果及模型生成的response。其中，LLM Tool生成的response中“没明确人数不能确定预定”这一关键信息被红色框突出显示。该图片与文档中“模型注意力 自动生成的response中没明确人数不能确定预定”这一关键信息被红色框突出显示。该图片与文档中“模型注意力过剩，认为一定要表达‘没明确人数不能确定预定’”的内容相关，直观上呈现了这一关键信息在LLM Tool界面中的位置。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Y2JjNTBjMjY2MDFhOWI1NDQ3ZDE1ZWNmM2FjNDA3NzJfODhmNGUxZDIxYmM3MWEzOGUzMGU2OWM1NjQyYmU3M2NfSUQ6NzY0NDg2MzIwMjQ4NDA3OTU3MF8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19789

Turn 8：

Judge 模型过度理解 key guide 内标准的意思，导致模型评分有不一致



![图片展示的是Airudder LASP平台界面，呈现了对话记录、LLM工具、Edit Panel、Metrics等板块。对话记录中显示了用户与AI助手的多轮对话，如用户询问补偿包裹的地址，AI助手回复需提供正确地址等。LLM Tool板块有“Assistant”和“Content”输入框，Edit Panel有“human”和“AI”输入框，Metrics板块有“Add Metrics”按钮。该图片与文档中关于模型注意力过剩、过度理解key guide内标准意思导致评分不一致等内容相关，直观呈现了对话场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZGViODgyYzJjNTJlYjc5MTYzMWRmMDVkYTE0YWY3YjNfMTZmMjc4NzM3OGVmZmY3MTM2OWMyZDE4NTNlNmViYTFfSUQ6NzY0NDg3MDI5NDU4NzAwMTgwM18xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=626&dialogueId=19792

Turn 22：

Gpt 注意力过剩，导致给出1分



![图片展示的是Anublader LLaMP平台界面，呈现了对话记录及模型评分情况。左侧是对话内容，显示了用户与模型的交互，如用户要求模型在平台上找到购买手机的客服并提供地址等。右侧是模型评分区域，有gpt、llm、gemin等模型的评分显示。画面中还突出显示了“human”评分，为红色。该图片与上下文紧密相关，直观呈现了文档中提到的GPT注意力过剩导致评分不一致等问题的对话场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTk1NWNkMmE1NTYyMDQ5ZjUxZWViMjNjNWNkNDNiZGVfY2QxN2M0MTU4NjIzYzgzZGVkNGEwN2RhZDQ5MTg5NmRfSUQ6NzY0NDg3MTc2NjQwNjMzNTY5NV8xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

Turn 18：

Gpt 注意力过剩，对于语言要表达出来太苛刻



![图片展示的是AnviLaser平台界面，呈现了LL对话数据的编辑与评估过程。左侧是对话内容，右侧有“LLM Tool”“Edit Panel”“Memo Panel”等板块。其中“Memo Panel”中显示了模型生成的对话内容，如“Certainly, for a private room reservation, we do have a minimum consumption requirement of 100 dollars. Would you like to proceed with this option?”。该图片与上下文紧密相关，直观呈现了文档中提到的GPT注意力过剩，对语言表达要求苛刻的情况，即模型在对话中过于关注细节，导致评分不一致等问题。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDRmMWZlNDg5N2FhMzdlMGJmYzkwNjYwNzlhOWE2ZDNfNzQ5MWMzMmZkNmQxYTIwNTk4MTIxYmQ5NDE4OGE5ZWVfSUQ6NzY0NDg3Mzg2NjQ0MDMzMDQ1M18xNzgyMTIyNzcyOjE3ODIxMjYzNzJfVjM)

Turn 18：

Gpt 注意力过剩，对于语言要表达出来太苛刻
