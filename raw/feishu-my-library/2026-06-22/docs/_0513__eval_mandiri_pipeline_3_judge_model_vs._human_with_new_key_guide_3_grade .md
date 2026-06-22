<title>[0513][eval]mandiri_pipeline_3_judge_model_vs._human_with_new_key_guide_3_grade </title>

# 总结

1. 当前主要痛点是“评分一致性和可解释性”：同一轮对话里，judge model 与 human、以及不同 judge model 之间对齐的差异性，尤其是 should 条件理解，对于中档分数评分起到决定性作用。
2. 评测链路还存在工程层面的准确性问题：如 model 名称识别错误、remark 仍带有已删除的 score constraint，说明配置变更后的端到端同步还不够干净。
3. 多个案例暴露了个别模型评判能力短板：包括语言合规检查被忽略（英文回答未被正确降分）、函数调用识别失败（实际调用却被判“未调用”）、以及对同理心/关键信息表达要求的标准不一致。



# 背景

- 这次工作的核心目标是：在 pipeline 和 key guide 更新完成后，利用高质量 remark + 多人类评分测试集，在 LAEP 平台跑通完整评测流程，拿到稳定且无错误的 judge model 分数矩阵和评语结果。
- **测试集：**id 508



# **卡点**

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=508&dialogueId=13441

当前对话Qwen 3.6 plus 模型生成，model名称识别错误。model名称内现在是不同model生成的content内容

模型生成的 remark 还包含我们 action 内已删除的 score constraint。

![图片展示了judge qwen_3.6-plus对模型回答的评价内容。模型在回复中用印尼语礼貌询问客户姓名，如“Baik Pak atau Ibu siapa?” 。remark指出模型在满足所有MUST条件方面的表现，有的回复虽优先询问姓名但存在信息泄露等问题，有的则因在未明确姓名和性别时使用“Bapak”称呼，违反了不得臆造或假定姓名、性别称谓的要求，qwen、gpt等因这些原因给出1分，与judge模型打分存在差异。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWYyMmRiMTMyM2RkYzg1OTNlNGU1ZGMyZDQwZjA0YmJfZTAwNzNmMjExZWU3YjBkYTMxZmYwMDhiYWVhMjNiODVfSUQ6NzYzOTI3MjAxOTEyOTU4NDgyMV8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)

**Turn 6：**

generate_gemma-4-26b-a4b-it_8 模型生成的回答，judge 模型打分差异大，qwen、gpt 给出1分的原因是认为模型在此处未确定客户姓名和称呼的情况下，假定客户姓名和性别。而 gemini 认为满足全部必须条件和 should 要求，与2个 human 评分一致。

<grid><column width-ratio="0.654803"><synced-source><img name="image.png" alt="图片展示的是对话系统评估平台界面，呈现了不同模型对“关于购买Samsung X26" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OTNiYmFlMDYzMTQzNTgzYzNiY2VlYWIxZTk5ODZmZDVfMjBjM2YzNTczNzRlNWI0ODE1N2Q4OGUzZDA5ZjE4OTVfSUQ6NzYzOTI3NTU5MjU1MDQ1MjE3Ml8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM" mime="image/png" scale="0.750000" src="LHFtbhfRToO61ZxKCN8ckbA3nJh"/><p></p></synced-source></column><column width-ratio="0.345197"><img name="image.png" alt="图片展示了judge gpt 5.1和judge gemini 3.6 pro preview对当前轮次对话的_ !*** !***" caption="&#xA;" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTQ2YTI2MjUwNGY1MjFmYmIyZWFkN2E4YTc2MzRkMmJfNmQ4NTExNWNkZDdhNDgyMzc3MTQzZDFmNjVjYzEzOTJfSUQ6NzYzOTI3NTc0MDE5ODExMjIwNV8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM" mime="image/png" scale="1.576674" src="NpdWbVljTor6H0xfaJycU7F8noc"/></column></grid>



https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=508&dialogueId=13442

**Turn 6：**

当前轮次 qwen 3.6 plus 出现严重错误，正确来讲当 judge model 应当在检测到当前回答为英语就应该给模型回答打1分，但是 qwen 模型完全忽略语言问题，只关注于文本内容是否满足要求，给出区别于 human 评分和其他 judge 模型评分的3分。

![图片展示 addCriterion addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTBlZThmYmMwMmQ1MjNjOGRhNDNmMzE0YWEzNTUzNDBfNzhhYjFmY2JkZWY1NzczNjJhYTVhMTE3ZWFhODAyZDlfSUQ6NzYzOTI4MjAwMzMwOTM4MjgzNF8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)

![图片展示的是对话系统中的一次交互界面。左侧显示assistant角色的对话内容为“我道歉，但我有点难理解你的请求。在我们继续之前，能知道你的名字吗？”，并有“该节点Language detection没有成功识别语言并转换，用户使用的是印尼语，但客服回复英文”的Remark，以及“此响应不用于学习”的Loss。右侧是生成的回答列表，其中“generate_gemma-4-26b-a4b-it_7”生成的回答与左侧一致。该图片与文档中“Turn 20”生成的回答及评分情况相关，直观呈现了该次交互的具体内容。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZGIzZmExMGU3OTc2MTNhN2FmNzBmMmE4MzJlMmVkOWVfNWViOTE4MzQ3MjE5OTRjM2UwOThkODgxZTBjYzU0NWNfSUQ6NzYzOTI4MTAxMjI2NjgzMTAyN18xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)

![图片展示了对话系统在处理用户询问银行柜员转账相关问题时的对话示例。用户询问是否能转账给银行柜员，系统回复需提供姓名和银行信息，但用户未提供。系统指出所有必选条件均满足，但未提及姓名和银行信息，未完全满足should条件。上下文提到这是Turn 20的对话，voyager - 1.6 - preview_5生成的回答，human 1、2打分3分，3个Judge model打分均为2分，认为其没有满足部分should条件，而qwen、gpt和gemini则认为模型回答应明确转账对象是银行柜员。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWY2ZTM4ZmMxNDQ4NDRhM2ZhOGY1OWNlNzUxYmIwYzdfMDQxNWFjZGRkMGQ4MTQ2YzdkYzA4NTgxNzYwZTZiOTNfSUQ6NzYzOTI4MTEwMzYyMzQ1NzcyMF8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)



https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=508&dialogueId=13444

**Turn 20：**

voyager-1.6-preview_5 生成的回答，human 1，2打分3分，3个 Judge model 打分均为2分，认为其没有满足部分 should 条件，qwen 认为回答不够富有同理心，gpt 和 gemini 认为模型回答应该明确像 user 表述转账对象是银行柜员。

当前 turn 能很好展现人类评分和 judge model 评分差异，model 在得到明确should 规定之后可以更好的掌握2分的评判。

![图片展示的是对话系统评估界面中的一次对话示例。左侧“Content”区域显示了用户与机器人的对话内容，机器人回复“Baik, Ibu Darlen. Mohon maaf atas ketidaknyamanan yang Ibu alami terkait kartu yang hilang. Tentu, saya akan bantu hubungkan Anda dengan petugas kami untuk proses pemblokiran kartu Anda. Mohon tunggu sebentar ya, Ibu.” 。右侧“Agent”区域有评分栏，显示human_1、human_2、qwen、gpt、gemma等评分均为2分，gpt-4.1-mini_1和gemma-4-26b-a4b-it_7给出的回答，gpt 5.1都给出了1分的评分。底部“Tool Calls”区域有“transfer_to_human”工具调用信息。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Y2NmNzI2YjkzNzYzNTQ2YTMyZmNiN2Q3NzU4YTVlYWFfMWUzZWJkMjBiNGNjNzdjOGRhOTg1NTc4MWZjNGJjZGRfSUQ6NzYzOTI4NDc4MzM2MjI0Nzg2OV8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)

![图片展示了模型评估的相关内容。上方部分是judge q3_3 - 6 - plus下不同模型的评估备注，指出各模型满足MUST条件情况及在SHOULD条件（如同理心、安全保证等方面）存在的不足。下方部分是judge gpt 5.1下各模型的评估备注，重点提及对转人工动作的体现情况，有的模型只能部分满足SHOULD指引。这与上下文提到的gpt 5.1对回答的评分相关，其因回答只是口头调用转人工function但未真正调用而给出1分评分。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YzAxNWI0N2U3NzQwYzQ3MWIwZDI5ODMxNGMyNzkwOTJfYjM1OTY2ZTE4YTMzNTAxZDY1M2RhZWM2YzY3ZDdiZGJfSUQ6NzYzOTI4ODU5ODkxNTE0MDgyOF8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)



https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=508&dialogueId=13445

**Turn 8：**

当前轮次，gpt-4.1-mini_1 和 gemma-4-26b-a4b-it_7 给出的回答，gpt 5.1 都给出了1分的评分，因为其认为 assistant 给的回答只是口头上调用了转人工 function 但是并未真正的调用。但是事实是，回答有真正调用转人工 function 但目前不知道什么原因，gpt 5.1 不能检测出其是真正调用了 function。

<grid>
<column width-ratio="0.553049">
![图片展示的是一个对话评分界面，显示了human_1、human_2、qwen、gpt、gemi等参与者的评分情况，其中gpt和gemi的评分均为1分。界面右侧有Metrics（5）区域，显示了“Baik Ibu Febi. Mohon maaf sekali atas kejadian yang menimpa Ibu. Terkait kartu yang hilang, agar segera aman, saya akan bantu hubungkan Ibu ke petugas kami untuk melakukan pemblokiran kartu. Mohon tunggu sebentar ya, Bu.”的文本内容。该图片与上下文讨论的gpt-4.1-mini_1和gemma-4-26b-a4b-it_7给出的回答评分情况相关，说明gpt和gemi给出1分的原因。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZGViMThkMmE1MDZmNmQyOTVhOWI5OTdmYWQyMGE4NTdfMDU4ZjgzMWVmNTgwMTg1NmRiZmJkZDFjMDI5OWQ4ODdfSUQ6NzYzOTI5MDc0MTQ3MDQ5Nzc0OF8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)
</column>
<column width-ratio="0.446951">
![图片展示的是一个评分界面，左侧显示了human_1、human_2、qwen、gpt、gemma-4-26b-a4b-it_7等五个参与者及其评分，其中human_1、human_2、qwen评分均为3，gpt、gemma-4-26b-a4b-it_7评分均为1。右侧是Metrics部分，显示了“transfer_to_human”操作，其NAME为agent_name，TYPE为enum，VALUE为Banking...。该图片与文档中对gpt-4.1-mini_1和gemma-4-26b-a4b-it_7给出的回答评分1分，但其认为assistant未真正调用转人工function的讨论相关，展示了评分及操作信息。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDk2ZDg0OWVjYzMyYTNjMmE0NzY1ZWI3NTM1ZjFlOTZfYWMzNWFjNWEwNTlhYjQwN2RkMTI2OGM2NzYyYjI3ZDRfSUQ6NzYzOTI5MDg0NDczNDI2MjI0NV8xNzgyMTIyNzY3OjE3ODIxMjYzNjdfVjM)
</column>
</grid>
