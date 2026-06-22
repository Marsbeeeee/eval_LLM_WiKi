<title>[0522]自动生成 remark 测试记录文档</title>

# 当前 remark 读取的上下文

system prompt + formal steps

- full dialogue history before current assistant node
- latest user/tool response
- previous assistant question
- current assistant content
- declared tools

![图片展示了文档中“4. Earlier](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWRmYzE1NzZkYjY5MDY4MmRhMGQ1Njg2YzcxMTlkYjNfZmI1YTc4NTg4NGEzOTY3YzJlNzc0NDM1NDEzNWExYjFfSUQ6NzY0MjY0NjUwNDg0ODMxMzU2MF8xNzgyMTIyNzcxOjE3ODIxMjYzNzFfVjM)

# Badcase

## faq回跳：

![图片展示了用户与AI助手的对话界面，左侧为系统提示，包含FAQ和正式步骤等内容。右侧是用户与AI助手的对话，用户询问“如果我选择的日期已过期怎么办”，AI助手回复“如果所选日期已过期，您需要选择新的日期”，并提供了“重新开始”按钮。界面右上角显示“Review Studio”和“Review”按钮。该图片与文档中“faq回跳”内容相关，直观呈现了用户查询及AI助手回复的场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YWY1MjBmZjI4N2NiY2NkY2UzODRmZGJmZWVhMmU5ODFfNDE2ZGQ4ZThkNGQ1MjMwMzhjNjIwODYyZDM4NmEwY2NfSUQ6NzY0MjY0NzIxMDYxNjI0NTQ1MV8xNzgyMTIyNzcxOjE3ODIxMjYzNzFfVjM)

用户提出一个 query，在 prompt 的 faq 部分，我们期待回复之后，是要回归主线。但是当前 badcase 会偏离主线或者询问有没有其他可以帮助的



![图片](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NTQ0ZGRlYzkyZWYwMTVlNjczODdlNjZlYTllMmU5ZThfNDkxYzE0MTU4NWIyZTA1YzJkYzg0MTk3NTc3MmY4MzBfSUQ6NzY0NDEwNjQ0NDEzNzUxNTk5OF8xNzgyMTIyNzcxOjE3ODIxMjYzNzFfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=595&dialogueId=18383Turn 18：

Turn 18：

当前turn应该是接收到function返回的账号信息错误，然后礼貌结束对话，但是这边remark读到faq那边的内容了，目前难以判断其错误原因



![图片展示的是一个对话界面，左侧显示用户与助手的对话内容，用户询问账号信息错误问题，助手回复需提供手机号等信息。右侧是LLM Tool界面，显示了对话轮次、角色、工具调用等信息，还呈现了用户、助手的对话内容及工具调用情况。界面右上角有Review Nodes、Branch等操作按钮。该图片与文档中当前remark读取的上下文内容相关，展示了对话及工具调用情况，辅助说明当前remark读到faq内容难以判断错误原因的问题。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=N2UwMTQ4N2U3ODg2OTRmODYwNzZkNmVlNjA3MWE1YWRfMmQ0MGRkNDgxY2M1N2NmMjM2OTAxNWU3ZTE4NWM1ODZfSUQ6NzY0NDEyOTc2MTQ4NDMxMTUxOF8xNzgyMTIyNzcxOjE3ODIxMjYzNzFfVjM)

https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=595&dialogueId=18379

Turn 12：
