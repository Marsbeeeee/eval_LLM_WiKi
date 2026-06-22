<title>Turn aware测试集 Judge 模型Diff对比 副本</title>

# 结论

两个模型在 judge 能力上几乎没有差异，都能够实现 judge 的流程，验证了 laep 在线化 judge pipeline 的可行性

两者的差异和胜负手主要在一些细节性的偏好上

1. ds 更加追求细节，会做出差异化打分。qwen 在保证要求无误的情况下就会给出 5 分。
2. qwen 对于固定话术的要求更加严格，如 `remark` 被写死为 "two roasted potatoes"，导致模型回答不一致就被扣分，甚至大小写上的差异都会视为瑕疵

---

# Badcase（匿名化）

<table><colgroup><col/><col/><col/></colgroup><thead><tr><th>index &amp; link</th><th>analysis</th><th>screenshot</th></tr></thead><tbody><tr><td>chat-demo-dialog_6_T10<br/>https://test-laep.airudder.com/#/projects/annotation/779/dialogue/15229?project_name=judge_compare_2</td><td>用户前文并未询问关于隐私安全的问题，是后文 t-11 提出的。<br/>从 action 设定来看未传入后文，需进一步了解模型为什么读取了后文的内容</td><td><grid><column width-ratio="0.430245"><img name="image.png" alt="图片展示了Turn aware测试集Judge模型Diff对比中的一篇Badcase分析。左侧是上下文内容，包含用户确认订单、电话号码验证成功等信息，以及用户对隐私和数据安全的担忧。右侧是分析结果，模型1 - 5得分分别为1 - 4分，模型1得分1分，指出其回复未解决用户对隐私和数据安全的担忧；模型2 - 5得分2 - 4分，指出回复虽有改善但未完全解决担忧。图片与上下文紧密相关，是对上下文所述问题的模型评分及分析。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTg3MWJmYjVlZGEyYTE1NmY0OTk4NmNlNTQzYzMyYmZfNGFkOWQ1YTRjY2MzYTZlZDE1NGE2NDMyMDYxM2VkYjJfSUQ6NzYzOTIzNDU2NDQzMDQ4MjM5OV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="0.550113" src="Ma90bPTpgo3QIQx0Gzfc9tgPnKc"/></column><column width-ratio="0.569755"><img name="image.png" alt="图片展示了一个LLM测试界面。左侧是对话列表，显示多轮对话内容，每轮对话有不同颜色标识。右侧上方有“LLM Test”字样，下方有输入框和一些设置选项，如“Name”“Prompt”等，还有“Submit”按钮。输入框内有示例文本“Sure, it&#39;s 86-123-4567.”等。下方有提示“Text input is disabled for this mode now”。该图片对应文档中“screenshot”一列内容，是对“chat-demo-dialog_9_T16”案例分析所涉及的测试界面截图，辅助说明Judge llm检测问题相关场景。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmY3ZWRkZGFiZGNiZWE0YWM1OGNiMGYyNGMxZjRiNmJfMjM2NTc1MjQwZWY5ODU1NmIxODg5NTExN2FhMDgzMTBfSUQ6NzYzOTIzNDU2NTU5NjMzNTA4MV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="0.381600" src="BFdcbDcqJoOyiFx166Xc7tGin2c"/></column></grid></td></tr><tr><td>chat-demo-dialog_6_T12<br/>https://test-laep.airudder.com/#/projects/annotation/779/dialogue/15229?project_name=judge_compare_2</td><td>remark error，这里prompt 里面并没有提到用户未确认订单项目、数量、特殊要求、电话号码和送货地址的情况，体感上模型优先回答对于隐私安全问题的担忧无误</td><td><img name="image.png" alt="图片展示了聊天机器人对话示例，左侧为系统提示，包含背景信息、规则等；中间是用户输入的对话内容，如“我想订一份鸡肉意面”等；右侧是LLM工具的对话界面，显示了模型1、模型2的回复及评分，如模型1回复“您的订单已成功提交，预计将在10分钟内送达”，模型2回复“您的订单已成功提交，预计将在10分钟内送达”，并有评分栏显示评分情况。该图片与上下文介绍的聊天机器人对话内容相关，直观呈现了对话示例。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Y2MwZTkzNDMyNDMyZjc3Yjk0OGQ3ZTg4NzQ3MGRkOTBfMDA4MDZiY2NjN2IzN2Y3ZmU3NTM3ZjBiYzQ3OGU1MTJfSUQ6NzYzOTIzNDU2MjkzNzMxMDE2Ml8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="1.000000" src="XXFebBoDmo5TT4xByF3cZEESnLh"/></td></tr><tr><td>chat-demo-dialog_7_T2<br/>https://test-laep.airudder.com/#/projects/annotation/779/dialogue/15230?project_name=judge_compare_2</td><td>deepseek error，remark 里面明确提到了这一步可以一次性收集三个信息，deepseek 以“同时询问颜色、价格范围和主要功能”为理由给了低分，和 remark 不符</td><td><grid><column width-ratio="0.500000"><img name="image.png" alt="图片展示了Google翻译界面，左侧为用户与助手的对话，右侧是翻译结果。用户询问手机型号，助手回复了多款手机型号及价格等信息。界面右上角有“翻译”“复制”“翻译为”等选项。该图片与文档中“Badcase（匿名化）”部分上下文相关，是对文档中提及的“chat-demo-dialog_9_T16”对话示例的具体呈现，展示了对话内容及翻译结果。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjA2M2VlMjAxODhiZmM1NmQ3NGU5NjAyNDU4Y2YyM2RfNjEzZTRlNjg4MzlmZDRmZjdjMjA5YTFlMmRjZDQ1MThfSUQ6NzYzOTIzNDU2MzIzMDk2MDU4OV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="0.380208" src="IRSIb0mVDoui8kxGONnc9ZVEnYg"/></column><column width-ratio="0.500000"><img name="image.png" alt="图片展示了Turn aware测试集的Judge模型Diff对比中的一段对话示例。左侧为系统提示，包含背景信息、沟通原则等；中间是LLM Tool界面，显示了用户与AI的对话内容；右侧是Review Notes部分，有红色框标注的对话结果，如“Hi there! I&#39;m Ray from TechWeb&#39;s smartphone sales team...”等。该图片与文档中对Badcase（匿名化）的分析上下文相关，直观呈现了对话内容及关键信息。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YmQwNTlmNjM0MTAyNWNkNTY1NTcwODBjZWRiOWM4ZmZfMDMxNmJjMzc5OTVkMjFmNDUzMGE5Yjc3Njk1MDg0YTFfSUQ6NzYzOTIzNDU2NDk3NTU0NTMxNV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="0.380208" src="NUXGbaUf8oYiznxAnLtcuwtUnjc"/></column></grid></td></tr><tr><td>chat-demo-dialog_9_T16<br/>https://test-laep.airudder.com/#/projects/annotation/779/dialogue/15233?project_name=judge_compare_2</td><td>Judge llm在模型语义理解上过于苛刻，对于用户的关键词没有语义转换表达的空间。</td><td><grid><column width-ratio="0.418274"><img name="image.png" alt="图片展示的是Judge模型对chat - demo - dialog_9_T16的评分及分析。模型1 - 4的评分均为2，评分理由分别为参数格式错误、参数格式不匹配、参数格式问题、特殊要求与用户喜好不符等。图片与上下文紧密相关，是对文档中提到的Judge llm检测出字段命名问题、符号错误等具体问题的分析说明，直观呈现了模型对对话内容的反馈。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmIxODM0Yjg2MTNhOWYxNzBlMDI5ZTJlMzhjZjU0NDNfODA1MDU1NDg3YWUyZDM1OTU1NGNkNmNjNGEwOTU5MWRfSUQ6NzYzOTIzNDU2NDA2OTkzNjEwMV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="1.414729" src="E0CibuiiWoZYrUxEIQ7c2WJznah"/></column><column width-ratio="0.581726"><img name="image.png" alt="图片展示的是deepseek-v3.2-guide对chat-demo-dialog_9_T16的分析结果。分析指出所有响应均为正确格式和参数的函数调用，A响应简化了项目描述，B、C、D响应准确捕捉订单详情，E响应虽准确但使用了大写字母，无turn-aware错误。还给出了各响应的分数、最佳至最差顺序、label2model信息等。该图片与上下文紧密相关，是对文档中提到的箭头指向的Judge llm检测出符号错误等异常情况的分析依据。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Mjc3ODE5N2FjZDA2OTkwNGJlODk1MzdmMzM0NWQ2NTJfZGFmNjcyZjcxMzE3N2VlN2MyZWY4ZDA4YzVhYWFlMjBfSUQ6NzYzOTIzNDU2Mzg1NTg5NTQ4Ml8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="0.648915" src="MeGkbSKuxoD9sgxWz1ycR0dWnGd"/></column></grid></td></tr><tr><td>chat-demo-dialog_9_T16<br/>https://test-laep.airudder.com/#/projects/annotation/779/dialogue/15233?project_name=judge_compare_2</td><td>deepseek-v3.2-guide认为run27m3a1、run27m3a2、run27m3a1-r2和voyage-1.5未使用口语（“36美元”，“40分钟”），如果没有额外模型输出规范，在真实场景中应该被认为允许的。<br/>Judge llm（qwen）认为run27m3a2回答语言过于冗长，voyage-1.5则是语言在当前场景下不够正式。而Judge llm对于run27m3a1和run27m3a1-r2两个模型跑出来包含关键字段和精简的回答打分为5分。</td><td><grid><column width-ratio="0.558578"><img name="image.png" alt="图片展示的是deepseek-v3.2-guide对chat-demo-dialog_9_T16的分析结果。分析指出，Response A完全遵守所有规则，使用口语词汇，流畅呈现价格和时间，并询问接受。B、C、D、E违反口语规则，使用数字而非口语词汇，是关键遗漏。其余正确提供信息并询问接受，无转意识错误。还给出了各选项的分数、最佳至最差顺序及label2model对应模型等信息。该图片与文档中对Judge模型Diff对比的Badcase分析上下文相关，是对其中chat-demo-dialog_9_T16案例的模型分析结果呈现。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NGNhOGI2OTEzNjk2MzM3OWEwYTEyODgxMjA2ZTc4NzJfZjdlMjAyOWMwMWEwOGJiZmFkYzQyNzk5YjIwNWVlZjZfSUQ6NzYzOTIzNDU2NDYyNzYxNDY2MF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="1.437008" src="EnYqbxxLco5vszxtTdTcboHwnQ6"/></column><column width-ratio="0.441422"><img name="image.png" alt="图片展示的是Judge模型对四个模型（model_1、model_2、model_3、model_4）的评分及分析。model_1和model_3评分5，分别指出其专业且明确说明总价和预计送达时间，还要求用户确认，满足所有标准；model_2和model_4评分2，model_2因包含非必要订单详情和用户信息而冗长，但满足主要标准；model_4因以“Great!”开头，显得不正式，违反专业英语要求。该图片与文档中对Judge模型的测试及分析内容相关，呈现了模型评分及反馈。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGQ2OWIxMWNiOGIzZWM1NjliYjJjNzRkZjY2NWM4YjBfZmIwMWU4NjBkNmM1NWI2NDcxNzc1ZGI2NTk5NmQzYTVfSUQ6NzYzOTIzNDU2NDYxNjczNTY3Nl8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="1.414729" src="C2H2bTIb4oM4x6xPsQUcs5Awnzc"/></column></grid></td></tr><tr><td>chat-demo-dialog_9_T16<br/>https://test-laep.airudder.com/#/projects/annotation/779/dialogue/15233?project_name=judge_compare_2</td><td>Judge llm 检测出字段命名问题，special_requirement被命名为specia_reqirement，deepseek-v3.2-guide但检测出符号错误。但在工程层面Judge llm认为需纠正字段名为行为，可能有风险，后续可以进一步测试。<br/>tools 单词拼错了，业务上单词修正可以通过，工程上需要确保模型不能自己纠正拼错的单词</td><td><grid><column width-ratio="0.466830"><img name="image.png" alt="图片展示的是Judge模型对deepseek-v3.2-guide的分析结果。分析指出所有响应均为面向系统的函数调用，正确调用generate_order函数并提取历史中所需参数。B、C、D、E响应完美，参数准确、格式正确且无额外内容。A响应有轻微瑕疵，&#39;specia_requirements&#39;值使用了波浪引号，可能在某些系统中导致解析问题，但意义正确，因此得分稍低。B/E和C/D内容相同得分相同，最佳至最差排序为B&gt;C&gt;D&gt;E&gt;A，且标注模型标注为gt，工具模型标注分别为run27m3a1@C0、run27m3a2-r2@C0、run27m3a2@C0、voyager-1.5@C0。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDAzYzFiNjY0ZmQwNTkyOTQxMDlhZDJmZTg0YzRiMDhfNzA3YThjODg1MGFiNTMyNGY1Mjk5NDE2NjdhMjdmNWNfSUQ6NzYzOTIzNDU2MjI1ODE2MDYwOF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="1.425781" src="Vvuqb3kKuoB7T4xMtKAcASnjnrS"/></column><column width-ratio="0.533170"><img name="image.png" alt="图片展示的是Judge模型对四个模型（model_1至model_4）的评分及备注。各模型得分均为2，备注指出其在参数命名正确性方面存在问题，如model_1和model_2的“specia_requirements”拼写错误，model_3和model_4使用了错误的“specia_requirements”键。该图片与文档中对Judge模型进行Diff对比的内容相关，用于说明模型在参数命名正确性方面的表现。" href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OWQ2ZDI0OWVhYjE1ZGZmM2RhNTYxZmYxY2U1MDVlMTlfZDBhMzI3NGVkMzE4ZTU0MDUyOTE3ODliNmIxMWViMTVfSUQ6NzYzOTIzNDU2NDcyNDAzNDUzNF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM" mime="image/png" scale="1.390476" src="X1ymb2Suyo5Fb9x9GHgc3haSnmZ"/></column></grid></td></tr></tbody></table>

# Badcase（qwen 未匿名化）

## judge 模型偏好差异（qwen 未匿名化）

## voyager 在固定话术内插入了衔接词，ds 认为不可行，打2分，qwen 认为可以接受，打4分

<grid>
<column width-ratio="0.636126">
![图片展示的是一个模型测试相关界面。界面左侧是系统提示内容，右侧分栏显示不同模型的回复。其中，右侧偏下部分有“voyager - LLM02”模型的回复，内容为“Great! Your information has been successfully received... ”。结合上文，图片与voyager在固定话术内插入衔接词，ds认为不可行打2分、qwen认为可接受打4分的讨论相关，直观呈现了voyager模型的回复内容，便于对比不同模型表现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OTVmOTVjYzVjYjExNzIxYTExYzQ3OTI4NzQyNTg0Y2FfMzZmYzNjMTY2NTZhMjgxNWVkOTY5ZDg2YjIwMDRhYjVfSUQ6NzYzOTIzNDU2MzIyMjcwMzA0N18xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.188299">
![图片展示](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWIzZWEyNTU2N2FiZWFkMjkwN2M1YzZjMTczYjQzODFfNTg1ODMzN2NlNDIxNDViZDkxMzg5OGQ2NTUzMGU1NzFfSUQ6NzYzOTIzNDU2MzIzMTAwOTc0MV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.175575">
![图片展示的是Turn aware测试集中Judge模型对不同模型生成的订单确认回复的评分及备注。评分均为5分，备注指出各模型回复均满足评价标准，如确认订单提交、提及确认邮件、提供支持联系信息、表达感谢等，但generate_qwen3.6-plus_5模型缺少明确表示对话结束的过渡语句，略有不足。该图片与文档中“judge模型偏好差异”部分内容相关，用于说明不同模型在订单确认回复方面的表现及评分情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OTQwYWQwOGU5Njc2OWM5NDZmZDc0ZmJjM2JjODhhMWZfMjgxZTg0MDE5YmRiNTQzMmMxYjljMzczN2I1YzJiNThfSUQ6NzYzOTIzNDU2NTY1OTM2NDMwNl8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## run27m3a2-r2 使用了针对用户对话的承接话术，语言表现更好，ds 给5分，其余模型4分。qwen 全部都给了5分

<grid>
<column width-ratio="0.544671">
![图片image_id对应文档中“judge模型偏好差异（qwen未匿名化）”部分。图片展示的是LLM Tool界面，左侧为对话历史，显示用户询问购买Tecno V200手机时需提供个人信息。中间是LLM Tool界面，显示模型回答及评分。右侧是模型回答 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YjQ5MWQxNzc0NTFlMmZjNGE1YzU1YjY0ZTRlOWE4OTRfNTdlY2JmNjYxOWE3YjA5NzBkYzA4ODI1MWYxMmI0NGZfSUQ6NzYzOTIzNDU2NjMwMDkyODk5N18xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.286908">
![图片展示的是deepseek-v3.2模型对Turn aware测试集的Judge模型Diff对比结果。分析部分指出所有响应正确承认购买意愿并要求在一次对话中提供三件所需信息，C因个性化提及小猫表现突出得5分，B和D因对手机特定的赞美缺乏个性化得4分，A和E为基本但正确的，得3分。评分部分显示各模型得分，C为5分，B、D、A、E分别为4、4、3、3分，最佳模型为C，最差为E。该图片与文档中Judge模型偏好差异的上下文相关，用于呈现模型评分情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Mzg3ODg2NTYwNmVlYzc5MzRlNTdmZWI5ZDgxNjAzYjBfZGY5MmVlMzQyM2ZiYjYxNjk3OTMzYmZlMzkxYmJiNmZfSUQ6NzYzOTIzNDU2MzQxOTY4NzkxMF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.168421">
![图片展示的是Judge模型对不同模型评分及备注的内容。其中，run27m3a1@C0、run27m3a2@C0、run27m3a2-r2@C0评分均为5，备注指出这些模型正确认可用户选择并一次性请求所有必要信息，完全满足评价标准。voyager-1.5@C0评分3，备注说明其虽一次性请求所有必要信息并用“Excellent choice!”认可用户选择，但未明确命名手机型号或确认用户具体选择，导致认可力度较弱。该图片与上下文关于Judge模型偏好差异的内容相关，呈现了不同模型的评分及对应备注。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MGRlMGI4N2YwMmUwZGEzOTI0YzMyNDg5NmE5ODJkZTlfZDUyNmE5ZjQ5YmJmZmNmNTk3YjQxYzIyMDhiMWRkYTNfSUQ6NzYzOTIzNDU2MzgwNTYyOTQwNl8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## 同上，run27m3a2-r2 使用了针对用户对话的承接话术，语言表现更好，ds 给5分，其余模型4分。qwen 全部都给了5分

<grid>
<column width-ratio="0.564427">
![图片展示了Turn addCriterion addCriterionLLM Tool平台界面，呈现了对话流程 addCriterion流程。左侧是对话历史，显示用户与系统的交互，如用户询问“Hello, I am an order specialist of QuickBite Delivery, with employee ID 08898. I’ll get your order in right now. What would you like to order today?”等。右侧是LLM Tool界面，有“Review Notes”“Branch addCriterionBranch”“Phone”“Apply”等按钮，下方是“Completions”区域，显示了不同模型的回复，如“run27m33 r2CO”模型回复“Hello! I understand you’re hungry and ready to order. I am an order specialist of QuickBite Delivery, with employee ID 08898. What would you like to order today?”。该图与文档中对不同模型回复的对比分析相关。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NGM1YTI5NzNkMjlmZmFlNTY5ZjQxMDZiMjhlMzllODdfOTg3ODBiYmJjYmVlZDcyOWYxYjliMDRiMDlhOTFmMTNfSUQ6NzYzOTIzNDU2NTM1NzI5MjUyMV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.218278">
![图片展示的是一个关于模型评分的分析结果。分析指出所有响应均符合Step 1要求，包含问候语、固定短语自我介绍及询问。A和C响应添加了富有同理心的表达，B、D、E响应虽正确但未明确承认用户饥饿，整体仍严格遵守人设，无转意识错误。评分方面，A、C得5分，B、D、E得4分，最佳至最差为A、C、B、D、E。该图片与文档中对Judge模型评分的上下文对应，直观呈现了模型评分依据及结果。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YWZjMjY1Zjg3MWJkNjcxMDVmYjA5YjkwMzYwYWQ1YmNfYjhiNzFmMWE4NTUxODJjNWQ3YzE1YzlhYWI4YjEwMGJfSUQ6NzYzOTIzNDU2MzQzNjM2NjgxNV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.217295">
![图片展示的是Judge模型对不同模型在Turn aware测试集中的评分及备注。评分均为5分，备注指出各模型均符合要求，如run27m3a1@C0模型助手正确介绍自己并礼貌询问用户点餐，run27m3a2@C0模型完全符合要求，run27m3a2-r2@C0模型虽有额外上下文但符合要求，voyager-1.5@C0模型明确身份并礼貌询问。图片与上下文紧密相关，是对文档中Judge模型对不同模型评分及备注的直观呈现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OWMwMzQ2OGJiOGYzOGIyYTU3ZmY2OGUzZjIwMGYzMmVfY2JkZDUxODk5OTk5ZWEyZWJiZWNiMTVjMWFhMjI5MmNfSUQ6NzYzOTIzNDU2NjI3MTUzNjA2NF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## run27m3a1 使用了陈述句结尾，ds 认为不够友善，给4分。qwen 给了5分。其余模型评分一致

<grid>
<column width-ratio="0.571549">
![图片展示了一个LLM工具界面，左侧是系统流程相关内容，右侧有多个文本框区域。其中右侧偏下的文本框内有一段英文回复，内容大致为愿意帮助用户查看在线菜单并推荐最受欢迎菜品，还表示若用户有需求可告知。结合上下文，此图可能与不同模型对回复内容的评分差异相关，用于说明在特定场景下如voyager在固定话术内插入衔接词等情况时，ds和qwen等模型的不同评分表现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODdmNDhlZjgwNjUwZTFjNmNjY2M5NWVkYTNlYzg2YWZfOTU0YmY2NGFjY2MzMDJlYzQ4YzM0Y2FmMzEwM2I4ZGJfSUQ6NzYzOTIzNDU2Mzk3MzM2ODc3NV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.241566">
![图片展示的是deepseek-v3.2的分析结果。分析指出，Response E最符合原则，正确遵循步骤1.2，使用会话式开场，以问题结尾，保持专业友好。B、C、D有轻微局限，承认用户并推荐菜单，但以陈述结尾，略减会话动量。A基础，仅提供所需信息，机械，缺乏承认，以陈述结尾，不具吸引力。还列出了各模型的得分、最佳至最差顺序及对应模型。该图片与文档中对模型评分差异的分析上下文相关，用于说明模型评分依据。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NzU1MmExNGM0ZDczZDU1ZDgxYmM1OTg0YjU4NTBlMmFfYTY4OGUyOWI3ODA1MWQzYzk3MzBhNzVjNTU3MmI2M2ZfSUQ6NzYzOTIzNDU2NDA3ODIyNjQwOV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.186885">
![图片展示了不同 addCriterion模型对不同模型的回答评分及备注。其中，run27m3a1@C0得分5分，备注为该回答明确引导用户查看在线菜单查看热门菜品并礼貌鼓励进一步帮助，完全符合专业和乐于助人的基调；run22](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTRmYTVmMDVmNjRkODRjMjYwNzE3ZWQ3M2RkNTRkODFfMTBmYWUzMWIyNmUxYmFkZmZiMDNjOGI2Y2FiOTU5YzFfSUQ6NzYzOTIzNDU2NTk2OTcxMDAzNF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## 对于固定话术，qwen 会严格遵守，甚至大小写上的差异都会视为瑕疵

<grid>
<column width-ratio="0.709855">
![图片展示了多轮 addCriterion模型在处理特定对话场景时的界面及对话内容。左侧为系统提示，要求确保信息准确，包括注册电话号码等。中间是LLM Tool界面，显示对话历史及模型选择。右侧是Review Panel，呈现模型1、2、3的回复，其中模型1回复“很抱歉，提供的信息与记录不符，请再次提供与您的QuickRide账户关联的电话号码”，并有“Translation”按钮。该图与文档中“](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDE4NDAyOWY0ODFmODg5NGY4ZmY4N2U4NTQ5NWNiYWZfODNlMTg2OWI4N2QxYTA5ZGY0YzFlZDNhYWRkNjkyOGRfSUQ6NzYzOTIzNDU2MjE5NDg4NTU5Ml8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.290145">
![图片展示的是Judge模型对不同模型的回答进行评分及评论的内容。Model_1得5分，评论指出其明确使用了“提供的信息与我们的记录不符”这一关键短语，且礼貌要求用户提供电话号码，完全遵循标准程序和评估标准。Model_2、Model_3、Model_4均得4分，评论指出这些模型虽符合核心标准，但对关键短语的表达不够明确，Model_4的表达还被前文礼貌道歉所遮挡。该图片与文档中Judge模型对模型回答评分的上下文对应，呈现了具体评分及评论。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OWI3ZmE2MjU4MjAxYTdmMWM4Zjc1YTgwNDVhZTBlN2FfMTA2NDYyZjYwYWM1ZmY2OGQ0MDllMTI3MThhOGU2ZDFfSUQ6NzYzOTIzNDU2NDYyNzYzMTA0NF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>



## ds loss case（qwen 未匿名化）

## ds 以回答太冗长为理由给 run27m3a2-r2 打分4分，但 run27m3a1 其实更加冗长

<grid>
<column width-ratio="0.501622">
![图片展示了Turn aware测试集Judge模型Diff对比中ds loss case的对话示例。左侧为对话界面，显示了用户与模型的多轮对话，右侧是编辑面板，呈现了模型的输出内容，包括用户订单信息、总价格、预计送达时间等。图片中红色框突出显示了模型输出的“Please confirm if this is correct, and let me know if you’d like to proceed with the order.”这一关键信息。该图片与上下文紧密相关，直观呈现了ds以回答太冗长为理由给run27m3a2-r2打分4分，但run27m3a1更加冗长的ds loss case情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWM1ZGZjNGY3OWQ2ZDRmMDFiZjFjYmY1Y2M5YmJhN2RfYmM3YTU0YTIzMWFlNGQzZDcwNGQ4Nzk5ZTc2YmFhMTFfSUQ6NzYzOTIzNDU2Mjk3OTU0ODExMF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.203049">
![图片展示的是run addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZmI5NjE1NWE2OWMwMDdlMjM1ZGVjMjNhNzUxYjEzMDRfNjFhOGU5ODE1YjljN2E1MjU1ZmNjY2UyZDQ0YjljY2JfSUQ6NzYzOTIzNDU2MjM1NDMxODI3OV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.295330">
![图片展示的是deepseek-v3.2 - guide对一组回答的分析结果。分析指出所有回答均以承认耐心、正确说明价格（$3.4）和预估交货时间（14:35）为过渡，且要求接受，符合turn - aware要求。评分方面，\[A\]和\[A\]因简洁、地道且完美遵循指令获5分，\[C\]包含完整总结及直接接受问题但稍冗长获4分，\[B\]有完整总结但接受提示不直接获4分，\[D\]虽有关键信息但总结不完整获3分。\[E\]为voyager - 1.5，\[B\]为run27m3a1，\[C\]为run27m3a2 - r2，\[D\]为run27m3a2，\[E\]为voyager - 1.5。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NGQ1ZGE4NzQ1NDk3NDczZmUzZjBjNzQ4OTk0MDBhM2NfMWI1NTk5MGRlNzA5ZjRhNzFlNWQ0NWJkMWI5YWZkZmJfSUQ6NzYzOTIzNDU2MjQzNDE3MzkwMl8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## ds 认为 run27m3a1 的提问方式“不够直接”，过于细节，看不出有什么问题

<grid>
<column width-ratio="0.593136">
![图片展示了Turn aware测试集Judge模型Diff对比中ds loss case的对话示例。左侧为系统界面，显示“Great! The payment link has been sent to 555-123-4567. Could you please confirm if the payment was successful?”等信息。右侧是LUM Tool界面，呈现了不同模型的对话内容，如run27m3a1和run27m3a2-r2的回复，其中run27m3a1回复“Certainly, I’ll get the payment link to the phone number we have on file, 555-123-4567. Could you please confirm if the payment has been completed?”。该图片与上下文提到的ds以回答太冗长为理由给run27m3a2-r2打分4分，但run27m3a1更加冗长的内容相关。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Y2U0OWRhZjRlZWE3YTk2ZjVjMTBlNzIxYTEzZDA4NTlfZmQwYWM1Yjg5NjU1ODAxZThiYzUxYWM2N2FjZWQwODZfSUQ6NzYzOTIzNDU2NjEwODAwNzM4NF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.406864">
![图片展示的是deepseek-v3.2模型对一段文本的分析结果。分析指出文本完全遵循指令，与参考回复完全匹配，且包含正确信息和促进对话的问题。评分方面，A得5分，E得5分，B、C得4分，D得2分。最佳至最差为A、E、B、C、D。还列出了各模型的评分及对应模型，如A为gt模型，B为run27m3a1@C0等。该图片与上下文关于模型评分及差异的内容相关，直观呈现了模型评分情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Zjg5NzQyZmQ0NDQxZGFhMmRkYjQxZjA3YzAzZWM1NmFfNGFjZjc5YjQ5ZmNlZmYzMGI1ZTBjMmJmZTEzN2Y1NmNfSUQ6NzYzOTIzNDU2NDQzMDUxNTE2N18xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## ds 最高分仅给出 4 分（同回答 qwen 给了 5 分），但是没有给出扣一分的理由

<grid>
<column width-ratio="0.654153">
![图片展示了Turn aware测试集Judge模型Diff对比中的一次Badcase示例，qwen未匿名化。左侧为系统消息，中间是用户与模型的对话界面，显示了不同模型的对话内容及评分。右侧是模型评分界面，显示了模型名称、评分、对话轮数等信息。画面中突出显示了“1.1. 生成成功，当前为第几轮对话？”这一问题，以及模型的回复内容。该图片与上下文紧密相关，直观呈现了Badcase中模型的对话情况及评分标准。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YTRjNzYxMmI4Y2Q4OWZkZDM1ZDUxZDljODg5NGMxNzhfNzRkYWI0OTE2NTFkNTFiMDM3YTYwOGNhMmIyN2RiZTFfSUQ6NzYzOTIzNDU2NDg1ODIzNTg2MF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.345847">
![图片展示的是deepseek-v3.2的分析结果。分析指出所有响应均为面向用户的无混合类型消息，B、D、E响应严格遵守原则，A响应虽有机械感，但遵循基本指令，C响应部分遵守。评分方面，A得3分，B、D、E得4分，C得2分。最佳至最差为B、D、E、A、C。还列出了turn_aware_error、label2model等信息，如A对应gt，B对应run27m3a1@C0等。该图片与上下文关于模型评分及表现的讨论相关，是对模型评分依据的呈现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODdkM2RkZGZhODg3NzYxOWUzOWE1NWQ0YTQzNzRlMTZfMmMxMDg0ZjM3ZjYyZjlhZWExMmY5ZmI0ZjAxMGMyMzJfSUQ6NzYzOTIzNDU2NTk2OTcyNjQxOF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## qwen loss case（qwen 未匿名化）

## qwen 以没有给出序号列表给 run27m3a2-r2 打分4分，给 run27m3a1 打分5分，但run27m3a1、run27m3a2-r2都没有给出序号列表

<grid>
<column width-ratio="0.523048">
![图片展示了一个界面，左侧是系统提示及对话输入区域，右侧有LLM Tool相关内容及模型回复。右侧回复内容中，有部分文字如“Now, to proceed with your Spicy”被高亮显示。结合上下文，该图片或是用于展示在Turn aware测试集中，不同模型（如ds、qwen）对对话内容评分判别的相关示例场景，可能与上下文中提及的模型对回答内容的评判标准，如回答是否遵守固定话术、是否冗长等情况相关。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDAwMDZiNmZjMjgwM2E0ODAwZTUyMjYyNzA0ZWZiZTdfZjY4MTQyNzQ3YjZjM2JlMWZlYTU3ODU5NzhjYTA1ZDdfSUQ6NzYzOTIzNDU2Mjk4Nzc4OTI0MF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.229371">
![图片展示的是run27m3a2-r2@C0的回答内容。上方显示“Completions (4)”，下方红字标注“run27m3a2-r2@C0”。回答内容先是感谢对方告知无特殊要求，关于修改订单，请立即联系客服热线1-800-QBITE-HELP。接着说明会尝试联系餐厅修改订单，但一旦订单进入准备阶段，无法保证更改。最后确认订单详情：一份辣鸡三明治，送至123 Main。该图片与文档中“qwen loss case”部分相关，是qwen以没有给出序号列表给run27m3a2-r2打分4分的依据。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZDY4NjI3N2VhZDVkZjFkN2VkOGE5YTg4YmJlNmFhZGZfNGNmNmQyMDk1NTRiOGUyYTRlNWJhOThjZTQ5YjliZDNfSUQ6NzYzOTIzNDU2MzM0NDQxOTc3MF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.247581">
![图片展示的是Judge模型对不同模型的回答进行评分及评论的内容。其中，run27m3a1@C0和run27m3a2@C0均得5分，评分理由分别为模型适当认可用户偏好，准确提供基于FAQ的订单修改答案等；run27m3](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZjZlNTQ0MTM0ZDJkZDU2MTQyZmIwNDM1OGI0Yzk1OTJfMDg4NGY0ZWY3MWRkODg4NGYzMDEzNjU1YmQwMjc1ODdfSUQ6NzYzOTIzNDU2NjA2NjA2NDM0NF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## tools 定义的参数名为 specia，四个模型回答都成功使用了这个独特拼写，但是 qwen 以拼写错误为理由给 run27m3a1 和 voyager 打了 3 分（正确回答：应该都是 5 分，因为所有模型都严格使用了定义的参数名称）

<grid>
<column width-ratio="0.635376">
![图片展示了Turn aware测试集Judge模型Diff对比中的一次Badcase（qwen未匿名化）示例。左侧为LUM Tool界面，显示了任务、delivery_information、generate_order等信息。右侧是Edit Panel，呈现了run27m3a1的对话内容，包括用户请求、系统回复及评分等。其中，用户请求中“special”被拼写为“specia”，系统回复中“special”被拼写为“speciala”。该图片与上下文紧密相关，直观呈现了模型在特定任务中出现的拼写错误问题。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MWI2NzBjNDdhZGJjNzY1YzEyNDFiODk4NGVmYWQ1NzRfNWNmMWY3NmQ0ZDIxN2MyZmVlYTFlMTRjZWQ1MDE1NWNfSUQ6NzYzOTIzNDU2MjM1NDMzNDY2M18xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.364624">
![图片展示的是Judge模型对Turn aware测试集的回答。Model run27m3a1@C0得分3，备注称模型使用了拼错的键“specia_requirements”而非“special_requirements”，可能引发功能问题。Model run27m3a2@C0得分5，备注指出模型准确调用generate_order，参数命名和格式与用户订单详情一致，满足会议要求。Model run27m3a2-r2@C0得分5，备注称完全正确调用generate_order，参数匹配用户最新输入。Model voyager-1.5@C0得分3，备注称generate_order调用包含拼错的键“specia_requirements”，违反预期键命名，存在风险。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=N2FlNWEwY2Q1YzEwOTM3MGEzOTA3ZjVhZDg2YzU4ZmFfNDQ0YzkwMzczZDMxODIwYjQwMjgzMTgwN2QyZjE2YThfSUQ6NzYzOTIzNDU2NTA5NzM5MzEyMV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>

## 模型回答无误，qwen 认为参数未填入，可能是 action 出现问题

<grid>
<column width-ratio="0.664434">
![图片展示了一个对话交互界面及相关代码内容。左侧部分是系统提示和代码，中间是用户与助手的对话，助手有 “Assistant” 和 “Assistant with tool” 两种身份回复用户，用户确认了相关信息。右侧是代码及注释等内容，其中有对 delivery_informa 相关代码的展示，包含 “NAME” 等字段。图片与上文提到的模型回答、action 等问题相关，可能是用于说明模型在对话交互及代码执行等方面的情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTZmZGVlYWNhOTYwYWE2YjYxMWE4YjVhNGFjMDMxMzdfZDI0NmM1MTE4ZmNlMWNkMjg4N2ZkZjE4ZTQ5MTZiM2RfSUQ6NzYzOTIzNDU2NjEyNDg1MDEyNV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.335566">
![图片展示了关于Judge模型对不同模型的评价内容。其中，model_1调用delivery_information函数参数正确，但未提供总价等关键信息，未完全满足标准；model_2情况类似；model_3函数调用参数格式恰当，但未给出定价等信息；model_4函数调用正确，但未说明价格等信息，未完全符合要求。图片内容与上文提到的模型回答情况相关，是对各模型在执行相关任务时表现的具体评判。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjdlZGJlMmU4YjIzMWNhNWQzOTgzNmE1YmNjYTY5MTBfZGJhMDcwZDU1NDJkYmE2NmQzOTE3MjY1MjVhMzhjMDBfSUQ6NzYzOTIzNDU2MzgwNTY0NTc5MF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>



## ori remark wrong

## remark 写死为 two roasted potates，导致模型不给出一样的回答就打低分

<grid>
<column width-ratio="0.500000">
![图片展示的是一个对话界面，左侧为用户与系统的对话记录，右侧是LLM Tool界面。用户要求系统确认订单信息，包括数量、特殊要求、电话号码、送货地址等。系统回复确认了数量、特殊要求、电话号码，但未确认送货地址。界面右侧显示“remark”为“no garlic”，“key”为“function - call”，“key value”为“string”。该图片与文档中“remark写死为two roasted potates，导致模型不给出一样的回答就打低分”内容相关，直观呈现了对话及LLM Tool界面情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YmUyOTJhNTMwNTkwZjI3MjRiYmE1NGZjZjgzZTg4OTJfNjEyMDhkYjU0YTk0OWI5M2M3MDk4ZWFjNTdmODY4ODFfSUQ6NzYzOTIzNDU2NDA3ODE5MzY0MV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.500000">
![图片展示的是Turn aware测试集Judge模型Diff对比中Badcase（qwen未匿名化）的界面。左侧为系统提示、用户和助手的对话，显示了不同模型的对话内容及评分。右侧是LLM Tool界面，有Review Notes区域，显示了对模型评分的备注，如模型run27m3a1的评分及备注，指出其item参数格式不正确。该图片与文档中对模型回答错误、参数未填入等问题的描述相关，直观呈现了模型评分及备注情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=M2ViMGU3MTE5YWFlMzJmMmQzNmNiOTU2Njg1YjUzZWRfNjAxMTRmNjEzMDBiODQ3MDFhMzkxNDIxY2E3NWZjZThfSUQ6NzYzOTIzNDU2NDcyNDAxODE1MF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>



## action wrong

## qwen 评价的 run27m3a1 实际是 gt

<grid>
<column width-ratio="0.679897">
![图片展示了Turn aware测试集Judge模型Diff对比中的一次Badcase示例。左侧为Qwen与用户对话界面，右侧是Llama 2、Qwen、Qwen 2、Qwen 3模型的对话界面。其中，Llama 2模型在用户询问“Would you like to proceed with the order now?”时，回复“Sure, that's correct, our delivery riders are instructed to leave the order at the door for your”，与用户预期一致。该图片与上下文紧密相关，直观呈现了模型在特定对话场景下的表现情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YmZiMGExMjVjYWJlNTc4YTk0OWQzMGZlZmMxNzM5ZmVfMTk3ZjcyNmZjOGMxYzA2MDI5MjI5NTY4YTY2YTVhYWZfSUQ6NzYzOTIzNDU2MzAxODczODY1OV8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
<column width-ratio="0.320103">
![图片展示了Turn aware测试集中Judge模型对不同模型回答的评分及备注。评分从3到5不等，备注中对各模型的回答进行了评价，如run27m3a1@C0确认了送货细节但地址表述有误；run27m3a2@C0确认骑手在门口取餐但未明确地址；run27m3a2-r@C0完全满足后续问题标准但未明确用户询问的送货地点；voyager-1.5@C0完全符合所有要求。该图片与文档中对模型回答的评分及评价内容相关，直观呈现了评分标准及结果。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODlhNTAzNTJhNzk2YTFkYzVhNmVlOTc4ZWViMGVhZmJfYmNkNDVkZjk0OGY1MjZkOTBmNjI5NDhkNjRjNDA0MmRfSUQ6NzYzOTIzNDU2MjE5NDg1MjgyNF8xNzgyMTIyNzgzOjE3ODIxMjYzODNfVjM)
</column>
</grid>
