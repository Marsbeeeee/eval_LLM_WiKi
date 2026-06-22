<title>[0507]Judge模型初步对比（qwen 3.6 v.s. deepseek 3.2）- 副本</title>

# Background

离线 judge 和在线 judge 的比较

# Conclusion

- 自动化评估结果表现出较高的一致性，qwen 和 deepseek 在大部分 case 上的评分分布接近，具备基础稳定性的评测能力。
- 当前高分（4-5）与低分（1-2）case 具备最简单的自动化筛选价值，可给出明确原因，因此当前功能能完善之后，适合用于减少人工初筛工作量。
- 然而，五分制无法对高分（4-5）与低分（1-2）内部形成差异辨析，因此提出三分制的方案

# Case

- 单一模型 Judge 存在主观偏好，deepseek 更倾向于严格按照 prompt 与上下文进行 literal matching，而 qwen 更倾向于结合真实场景，进行场景与语义合理性进行评分，因此单个数据集目前需通过人工审查或 multi judge 模型进行对比来提高结论置信度。

<grid>
<column width-ratio="0.552439">
![图片 addCriterion图片展示了deepseek-v3.2模型的评分分析结果。分析指出，A选项完全遵守所有规则，使用口语词汇、合理呈现价格和时间并询问接受，而B、C、D、E选项违反了使用数字而非口语词汇的规则。评分结果为A得5分，B、C、D、E各得2分。最佳至最差为A、B、C、D、E。未出现轮次感知错误，且标注模型为A为gt，B、C](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NzM4NzM4OWIxMDJlYzBkZjhhNGEzMjk2ZTRhYWY1MTVfOTBlZGFiMjkwNTA2YmZkZTJkOTVhMjQyMjBlNGMwMTNfSUQ6NzYzOTIwODM3ODYzNTk0Njk1MV8xNzgyMTIyNzYwOjE3ODIxMjYzNjBfVjM)
</column>
<column width-ratio="0.447561">
![图片展示 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTFiZGFhYzYxYTVhODg4NjBmZWVkMzk4MjA2ZDhlZWNfODhjMjljNTQ2Y2NhN2NkN2NjOWIzYjA1ZDUxNDAwZTBfSUQ6NzYzOTIwODM3NjE5OTQxNjc5M18xNzgyMTIyNzYwOjE3ODIxMjYzNjBfVjM)
</column>
</grid>

- 上下文会明显降低模型的稳定性，在多轮对话中后期多个模型会出现评分下降的现象，说明可能 1）对话模型本身因为 attention 偏移导致在对话中后期表现变差。2）自动化评测在长上下文场景下出现 attention 偏移，或上下文遗漏或者语义判断不稳定的问题
- 工程类问题不能仅仅依赖语义Judge判断，例如字段命名错误（special_requirement → specia_reqirement）虽然语义上可理解，但真实场景可能直接失败，因此schema、字段名等工程类问题不可直接交由LLM Judge进行处理与判断。

<grid>
<column width-ratio="0.466725">
![图片展示的是deepseek addCriterion addCriterion()函数的代码示例。代码中定义了两个函数，分别是`addition`和`subtraction`，分别用于执行加法和减法操作。`addition`函数接收两个参数`a`和`b`，返回它们的和；`subtraction`函数接收两个参数`a`和`b`，返回`a`减去`b`的结果。代码还定义了两个变量`x`和`y`，分别赋 自动生成 addCriterion()函数的代码示例。代码中定义了两个函数，分别是`addition`和`subtraction`，分别用于执行加法和减法操作。`addition`函数接收两个参数`a`和`b`，返回它们的和；`subtraction`函数接收两个参数`a`和`b`，返回`a`减去`b`](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YzM2ZTk2MGU4N2Y0YzEzMGYwNzc2MGZmOWEwMzk1MDZfOGM5NTBkZThkOWY1NjkwY2RjNDkyNDJjZGIwMzc2YThfSUQ6NzYzOTIwODM3NjkzNTE1NjY4OF8xNzgyMTIyNzYwOjE3ODIxMjYzNjBfVjM)
</column>
<column width-ratio="0.533275">
![图片展示的是Judge模型对多个模型的模型在特定任务上的评分及备注。模型包括model_1、model_2、model_3、model_4，评分均为2。备注指出这些模型在参数命名上存在错误，如使用了“specia_requirements”而非正确的“special_requirements”字段，违反了参数命名规范。该图片与上下文紧密相关，上下文提到工程类问题不能仅靠语义Judge判断，此图直观呈现了Judge模型对模型参数命名问题的反馈情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NWYyMGUyOWJhZDYzMWVmMzQ5ZDc3YTIxNTIwMmVlOTVfYzc2ODI1YTcwNzU1NjA3MTk3ZDZmM2E2NDkxYjI1ZDRfSUQ6NzYzOTIwODM3NzY1NDU5NDUyOF8xNzgyMTIyNzYwOjE3ODIxMjYzNjBfVjM)
</column>
</grid>

# Data analysis

<cite doc-id="XzbkwT1NdiuLdckq7JycR978nig" file-type="wiki" title="judge_compare_2_779_625_no_lang_20260430102102_9" type="doc"></cite>

<chart-embedded thumbnail-url="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGU0ODRkMDkwM2FiMzg5MzlmNjMxZDhlY2QzZTFhZTdfOTVhNzIzZGViMjZhNmM0MWQ1NGM2MTdiMGFiMjZjYmRfSUQ6NzY1NDE1ODk1NTc1MzMxMTIwM18xNzgyMTIyNzU5OjE3ODIyMDkxNTlfVjM" token="chtcn3s3BP6SEJcJDtArj71VAUg"></chart-embedded>

**图标说明：**

分值为 5，qwen 包含 151 条 v.s. deepseek 包含 128 条

分值为 4，qwen 包含 48 条 v.s. deepseek 包含 71 条



**结论：**

高分（4、5）存在数量差异，但整体来讲两个模型的整体分布接近（± 1内一致率普遍>90%)，自动化评测目前就当前两个模型来讲具备一定的一致性。但也存在模型评分偏好，deepseek 更注重是否与标准完全一致，qwen 会更结合真实场景进行一些调整。就目前来讲单一的Judge可信度低，需要人工二次验证，或者多模型 Judge 进行对比。

<chart-refer-host-perm thumbnail-url="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NjhhYmRiMzEzMGM3OTA2NTIzNjBjZjhiYzIwODQzNGJfYmEwZjMzZmFiNzE0YjNlY2MwZmY1NzMyODZjYjhkZDlfSUQ6NzY1NDE1ODk1NzQ4OTk0OTY1N18xNzgyMTIyNzYwOjE3ODIyMDkxNjBfVjM" token="chtcnOtwb3R1jEoaSKZ1TxerU8X"></chart-refer-host-perm>

![图片问图片展示了各Turn一致率对比情况。图中柱状图呈现不同Turn的低（<60%）、中（60 - 75%）、高（>75%）一致率占比，如Turn 2低一致率70%（n= addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MDM5ODY0MzI4N2Q0NmVjODZhNmNlNWEyMDVlZjMwODNfNTFkYWJiY2VlNWNlM2Y3NzRiNzBhZmFhNmYwNDM4OWVfSUQ6NzYzOTIwODM3NjI5NjIxMzQzNl8xNzgyMTIyNzYwOjE3ODIxMjYzNjBfVjM)

**结论：**

大部分case未来可以选择自动化完成评测，质量过低的样本也可以进行自动化的筛除，如果在system prompt完善且有明确的评分标准之后，可能可以达到的效果是：真正需要人工审核的样本较少。长上下文会对模型稳定性有一定的影响，则用自动化模型评测对于长上下文的数据对话需要更慎重使用。



**图标说明：**

第一张图可以看出模型在前期表现是比较稳定的，能维持在4分左右，模型基础回答能力较为稳定，中后期尤其是Turn 16 附近集体出现2分左右的低谷



**数据说明**：

1）中后期 Turn 数据量本身较少，数据分布不平衡。2）每条对话只有几个节点是非 loss 的，这些节点分布具有随机性，和 Turn 无明显耦合。



<chart-refer-host-perm thumbnail-url="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Mjc1NzZiMTQ1YWEwNmM0Y2VkNTRhNmE5NzE0ZmQ1OGNfMjRlZjViZTJhY2ZlNGVmNzU4MmIyNWFmMzkyYTRiMDJfSUQ6NzY1NDE1ODk2MjQ2MzgwNDM2N18xNzgyMTIyNzYwOjE3ODIyMDkxNjBfVjM" token="chtcnVwwnjm3je1vM84TvUQIEIf"></chart-refer-host-perm>

**结论**：

当前 turn 一致率结果可用于趋势判断，但不宜直接做“回合能力强弱”的因果解释。由于各 turn 实际读取的上下文长度不一致，且 tree 结构中存在节点被 loss 后被 Judge 跳过的机制，结果会受到“可见上下文差异 + 有效样本选择偏差”的共同影响；因此该图更适合用于发现风险区间，而非直接比较 turn 间绝对优劣。



**图标说明**：

图中的峰谷不仅反映模型稳定性，也混合了评测输入不一致带来的波动。部分低谷可能来自长上下文负担，也可能来自该 turn 有效节点被大量过滤（loss）后的样本结构变化；高点同理可能受益于更短上下文或更“干净”的保留节点集合。



**数据说明**：

1）turn 间上下文长度不一致，会改变 Judge 的注意力负担与判定依据，导致一致率不可直接横比。

2）tree 结构下节点 loss 会使对应节点被 Judge 忽略，等于每个 turn 的评测样本集合都在变化，带来显著选择偏差。

3）非 loss 节点本身分布随机、且与 turn 弱耦合，进一步放大中后期波动与局部异常点。

# 后续工作

当前数据暴露以下问题，验证 judge 模型需要人工评分横向比较，对比 judge 模型与人工评分的一致性差异和kappas系数

五级制评分未能良好地反映 pass/error 二分，因此考虑改为三级制

1. 加入更多横评模型
2. 打分改为三级制（1-error，2-acceptable，3-pass）
