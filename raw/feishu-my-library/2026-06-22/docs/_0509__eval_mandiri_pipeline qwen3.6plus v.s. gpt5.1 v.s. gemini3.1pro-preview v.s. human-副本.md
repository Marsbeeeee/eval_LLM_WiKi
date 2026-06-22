<title>[0509][eval]mandiri_pipeline qwen3.6plus v.s. gpt5.1 v.s. gemini3.1pro-preview v.s. human-副本</title>

# conclusion



1. 人工 v.s. 模型打分对比：4/5，仅一个节点，gemini给出了2分，其他模型和人工都是3分，且理由可以接受（model_1 简要地提到了后续调查的步骤）
2. 调整为 3 分制以后，多法官模型打分稳定性提升显著，验证了 key guide 中将 criteria 区分 should/must 梯度并测试有效性
3. qwen 出现 metrics 和 review 给分不符，以及 review.name 未填入的问题，属于模型能力问题，其他模型均为出现这个问题。建议 metrics 和 review 解耦
4. 后续平台提出了 evaluate 都改良方案，可以通过列表遍历。通过这种方法可以解决模型匿名化。

# background

1. task 789
2. 三级制

# workflow

1. 拿到修复好的数据集
2. 将正确回答替换到gt，并删除所有completion内容，确保completion是空白的
3. 导出为离线测试集，generate 3个模型生成的回答，并匿名化

   1. 参与模型：gpt4.1mini、qwen-flash、voyager1.6
4. 导入laep，人工对3个模型回答打分
5. 运行pipeline，使用 llm as judge 进行机器打分

   1. 参与模型：qwen3.6plus v.s. gpt5.1 v.s. gemini3.1pro-preview
6. 比较人工和judge模型打分差异

# case

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=488&dialogueId=13308

所有human和模型metrics都一致，qwen 在metrics 里面给出的分齐全，但是 remark 不完整

![图片展示的是JQ3任务中模型qwen_3.6_plus的评测结果。评测内容为模型对用户确认信号的处理情况，模型得分3分，备注指出其正确解读用户确认信号，避免进一步澄清，同时在保持已建立语境的前提下，以相关查询调用知识检索。该图片与文档中对qwen在metrics给出分值但remark不完整这一情况的描述相关，直观呈现了模型在该任务中的表现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YjVjMTVkY2YzOGFlNjk2Mzc4Y2VlNWFhM2NjMzA5YjNfZjk2NjZhODA1M2I3NTdkNmJlZTViNTUwMTFlNWFjY2JfSUQ6NzYzOTIwMzkzNDQxODg5Nzg4OV8xNzgyMTIyNzYzOjE3ODIxMjYzNjNfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=488&dialogueId=13307

用户话语存在歧义，模型上一轮已经询问了还有别的问题吗？用户说了 okay okay okay，可以理解为没有问题，也可以理解为还有别的问题，属于语义歧义

暴露问题：guide 模型压力过大，需要生成出完整的 must/should 指标

![图片展示了Mandiri项目中LLM Tool界面及相关操作。左侧为Knowledge Search Prompts输入框，中间是LLM Tool界面，显示“image_id>>处有“OKAY OKAY OKAY”字样，右侧是Chat界面，显示了用户与模型的对话内容。该图片与文档中暴露的“guide模型压力过大，需要生成出完整的must/should指标”问题相关，直观呈现了模型在处理用户反馈时的对话场景，辅助说明模型在生成指标方面存在的挑战。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NjQxMDhlZDBhMjllYWU0YjFkOTlkODhmYjc4MDk5NDNfNzUxNzQxZjg2YTM0NmRhYjRlYzkwMzYxOWIzN2ZlODVfSUQ6NzYzOTIwMzkzNTkzMjY5NzU2OV8xNzgyMTIyNzYzOjE3ODIxMjYzNjNfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=488&dialogueId=13306

所有human和模型metrics都一致

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=488&dialogueId=13305

gemini 打分存在差异。model_1 简要地提到了后续调查，这里是否视为模型表现不完美是主观的，可以接受打分。

人类标准讨论：

主观：对于 turn-aware，如果上一步用户回答不影响下一步，可以 turn 感知，如果会有影响，不可以直接合并？

<grid>
<column width-ratio="0.410836">
![图片](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDEzNjQ3NWIyNGM0N2EyNjMwN2ZmZjVmODJlYTdhZTVfMzA0MzIzYjdlNjNhMzRlOGI2Y2MyOWE1MmM4NjNlMDFfSUQ6NzYzOTIwMzkzMzc2NTE3NjI5N18xNzgyMTIyNzYzOjE3ODIxMjYzNjNfVjM)
</column>
<column width-ratio="0.589164">
![图片展示了关于“Livin’ Fest”信息请求的用户回复及各模型的打分与备注。用户回复“udah ngerti makasih ya”，表示理解并感谢。模型1、2、3、4分别给出1、2、3、4分，其中模型1、2、3因违反“必须”条件被扣分，模型4因输出为空被扣分。各模型还对违反的“必须”条件进行了备注，如模型1、2、3违反了“在用户明确表示没有其他问题之前，就过早地引发调查问题”条件，模型4违反了“输出为空”条件。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NmViOWVlOGNmMzI1MWUyZmY4MGYwYzVlOWQwNGE1YjZfZDVmYTkwZmMzNjZmYmI5MzBiY2JiYTk0Yjk3MjA4ZTVfSUQ6NzYzOTIwMzkzMjA5OTU2MjQ0MV8xNzgyMTIyNzYzOjE3ODIxMjYzNjNfVjM)
</column>
</grid>



# Model visualization

- 3 分制 + should/must 梯度后，三位 judge 的均值区间更集中，整体稳定性相比以往是提升的。
- 但仍能看到 judge 标尺差异：Gemini整体更严格（对应你们提到的 2 分个例），Qwen/GPT更接近人工 3 分，这说明“与人工对齐”上 Gemini 偶有偏保守，但理由可接受，不构成系统性失真。

<chart-refer-host-perm thumbnail-url="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MGI5YjFjZmU3MWMwY2IwMTFhNTAyNjc1NTdhZGQ0MzlfNTI2NDNjNTIwOGViNjQzODNmZGRiMGRhMzZhNTJiZjFfSUQ6NzY1NDE1ODk2NTM0NjExMDQwMl8xNzgyMTIyNzYyOjE3ODIyMDkxNjJfVjM" token="chtcnCaK3mreW3w8NewaXKSx2Fb"></chart-refer-host-perm>



- 三个 judge 在样本上的占比结构有波动，说明模型间判断机制不完全一致，因此需要多Judge汇总而不是单模型裁决。
- 图二里分布不稳定，和 Qwen 出现的“metrics 分数与 review 分数不一致、review.name 缺失”现象一致，说明输出格式有耦合问题，本来应该独立的两个模块（metrics 和 review），被模型一次性混在一起生成，导致互相影响，出现不一致或漏字段。因此把 metrics 和 review 拆开处理是必要的。
- 从laep平台后续方案看，采用 evaluate 列表遍历有助于统一输出结构并支持匿名化，对图二这种多模型统计口径也更友好。

<chart-refer-host-perm thumbnail-url="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDlkNzA0ZDE2YWZlZTQyMzRiOTJhOTdlMGQ2NTEwYjFfYTAwMGIwYzM4ZGJhY2MyNWRjOTg2YThmMWM1ZmI4NTJfSUQ6NzY1NDE1ODk3MTMzOTE4MTAyM18xNzgyMTIyNzYyOjE3ODIyMDkxNjJfVjM" token="chtcno0S7bOjbAEZiK7jRsqAoCg"></chart-refer-host-perm>

# todo_list

1. 实现 mandiri 逐日期数据集的自动评估
2. 完成 turn-aware 的 judge 模型横评，探测模型偏好和能力差异
