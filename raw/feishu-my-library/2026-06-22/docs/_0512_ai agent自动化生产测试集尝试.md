<title>[0512]ai agent自动化生产测试集尝试</title>

# 总结

- 为解决 `system prompt` 变更后人工重建测试集效率低的问题，尝试用 AI Agent 批量生成对话测试集。  
- 方法上采用了“`system prompt + 硬性 constraint`”来约束生成过程，以保证数据结构一致、流程可控。  
- 实践结果是成功产出并上传了可用测试集，同时修复了 `UTF-8 BOM` 导致的上传失败问题。  
- 结论是该方案相较人工和旧平台助手更高效、更稳定，适合后续持续扩展。



## 任务背景

当前流程中，我们从 PA 获取对话数据集后，只要 system prompt 发生改动，就需要对整套数据进行全量重跑。若采用人工方式逐条重建对话 case，整体成本较高，且在效率、一致性和可复现性上存在明显瓶颈。



前期我们尝试使用 LAEP 平台内置 assistant 自动生成数据，但效果不理想，主要问题包括：

1. 仅支持在单条数据内生成，难以高效扩展到批量场景。
2. 生成过程依赖实时对话交互，缺少可复用的固定约束（constraint）。
3. 平台模型版本较旧，对复杂规则与流程约束的理解能力有限。



基于以上痛点，本次尝试转向新型 AI Agent，通过“system prompt + 硬性约束（constraint）”方式批量化生成测试集。



## 任务目标与验证方式

基于现有 system prompt 与单条基准对话样本，按树结构规范扩展多场景 conversation，产出可上传、可校验、可复用的测试集。

本次验收口径：

1. 产出树结构测试样本并完成 JSONL 落盘。
2. 数据可通过基础结构校验（逐行可解析、字段结构一致）。
3. 数据可用于 LAEP 上传（含编码兼容性处理）。



## 输入与依据  

核心输入包括：  

- system prompt（业务规则与对话流程）  

  <figure view-type="Card"><source href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NzExMDc1OTUyZWIyZTNjZDUxM2VjNmZlMGEwNzg2OTdfMGYxNzhlNzU2MDcwYWY0MzNlMjA5YjQ4ZDc3ODEyMzdfSUQ6NzYzODg5MzAzMTc0MzkxNzI3N18xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM" mime="text/plain" token="KH6Lbsfqco22uXxbuIWchikSnAc"/></figure>
- 单链样本（基准对话风格与字段结构）  

  <figure view-type="Card"><source href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGIxZjdhMmNiMDkxOTM3N2I3MDgwN2FjYzBkYTljMWFfZmNiZjQ2ZDg1ZTZiOTA3OTRjMTg2ZWRjOGY5ZmRjMGFfSUQ6NzYzODg5MjkzNjQ2MTMzOTg2Nl8xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM" token="HS8zbwAXuoAPudxoPr1c5B5mnAg"/></figure>
- 参考树结构样本（节点组织与 JSONL 约束）

  <figure view-type="Card"><source href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZjBiNDFjMTM0ZDIwNDhjMmQxN2M0Njk3OWNhOTU1OGRfYTE2NTBlZTdkZTg1MzNlNWRhNjIzMGNmNjNkNzlhZWFfSUQ6NzYzODg5Mjk3NjY2MzgyNTM0Nl8xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM" token="REpRbFdRpoRLbKxwzmwcwiUlnng"/></figure>

## 执行内容  

- 对齐目标 schema：确认 type=tree、dialog、node_index、parent/children 等关键字段规范。
- 基于单链样本扩展多场景测试数据，覆盖忙碌、错人、已还款、部分还款、转人工、亲属接听、沉默等典型分支。
- 统一输出为 JSONL（每行一个完整样本对象）。
- 执行基础校验：逐行 JSON 可解析性检查、树结构字段一致性检查。
- 处理上传失败问题：针对 UTF-8 BOM 导致的校验失败，重写为 UTF-8 无 BOM 编码并复检。



## 产出结果  

- 单链路含system prompt的对话https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=495&dialogueId=13368）

  - codex生成原始jsonl文件，<source href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjA3ZWZjNjgxNmJhY2VlM2FlNDQyZDZmZTlhNmU3OTlfZmQ0MjlkNjk4NGEzZTBmYWI4YzMyMjBiMWE1MDlmZDRfSUQ6NzYzODkxMDAyNTE0MzM3MzAyNF8xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM" mime="application/json" token="D9KLbGx8soA5yixtZq8cxhpNnge"/>
  - 生成对话整体符合 system prompt 流程约束，语气自然。
  - 在忙碌、错人、已还款、部分还款、转人工、亲属接听、沉默等场景下可稳定生成结构化样本。
  - 按照以下硬性constraint进行，<cite doc-id="N3WBwnTZaiqln2kK3aacFWBdn9e" file-type="wiki" title="codex动态生成完整conversation规范协议" type="doc"></cite>

![图片展示了AI Agent自动化生产测试集的界面。左侧是系统提示框，显示名为Nanati的AI助手，需用墨西哥西班牙语、礼貌、同理心且专业正式的讨债语气，涵盖流式问候、身份 addCriterion、验证、超时提醒、重申、关闭等场景。右侧是LLM Tool界面，有多个节点，部分节点有红色箭头指向，如“流式问候”1 addCriterion”等，还显示了Review Nodes数量为0/0。该图片与文档中AI Agent自动化生产测试集的生成及测试集id、包含对话样本等内容相关，展示了测试集生成时的系统提示及工具界面情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTE1ZTA1MjRjZDg4N2Y2NzMxYzVmOTQ4NDk3N2I5MDZfY2QwZjE0Zjk3YjA1NTczNTA5MDQ5NmZiMDk3MDQ5YzlfSUQ6NzYzODg5ODA0NzA0OTk0NDI1OV8xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM)



- 已生成并上传测试集

  - codex生成原始jsonl文件，<source href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Y2M4YmFjNDQxYzliYWUxMzlmNmNjY2UyODc1MmVjOGVfNzU0MTgwZDIyYzU3ZGYzY2EyYTNhZmI5NmE3MTAzYjhfSUQ6NzYzODkwOTg2MDE2NzczMjQ0MV8xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM" token="GVUtbria1oo2wTxtjXGcUhevnwd"/>
  - 测试集id 501，当前包含 7 条 tree 对话样本（覆盖 7 类典型场景）
  - 使用以下的constraint进行规定，<cite doc-id="FX8Hws637ixYyDkEdFKcrk8AnJf" file-type="wiki" title="用codex作为ai agent批量化生成测试集" type="doc"></cite>

![图片是一张界面截图，展示的是测试集管理界面。界面中有ID、NAME、DESCRIPTION等列，如ID为501的测试集名为test_mult_i_conversion_action_by_agent，包含65条对话样本，语言为EN，创建于2026 - 05 addCriterion5](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MTAxZDExYmE0NGUxMWFkMDg0NWU3Yjc4NTY1ZTg3YzlfOTUzY2IwZTk3NzgyMjAyYmExNGE3MzM5NzU0ZTUzZDZfSUQ6NzYzODg5OTIyNjI3NzU2MzYwOV8xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM)



- 模型横向比较生成效果

  - 使用cluade code生成测试集，效果同codex一样
  - 原始jsonl文件<source href="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YjNkY2QzMmM5MTg1MWU5NTA1ODcyMWY1YTg4YWMxZjRfMWFhNjcwZjk4OTAxNzM5MDA2OTM1NTJiNTNiMTg5ODJfSUQ6NzYzODkxNDA3MjQ4Mjg1OTk2M18xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM" token="PAwubvVaNo5JnTxkHYUc2p4oncg"/>
  - 测试集id 503，共29条tree对话样本

![图片展示的是0](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZDdiNjU4MGY0MjI0MDkzNWI1M2I2YmI4OGM0NjY5ZTNfYTM5ZjA3NzkyOTliYzYzYTRhMzRmZjQwNTlmZTg0YTdfSUQ6NzYzODkxMzYwNTI2NTYyNDI0OV8xNzgyMTIyNzY1OjE3ODIxMjYzNjVfVjM)



## 问题与修复记录

- 问题现象：上传时报错 Unexpected UTF-8 BOM，导致 JSONL 校验失败。
- 根因：输出文件包含 BOM 头，平台按无 BOM 读取时在首字符处失败。
- 修复动作：将文件重写为 UTF-8（no BOM），并进行首字节与逐行解析复检。
- 修复结果：编码问题消除，文件可继续用于上传校验流程。



后续有完整的constraint文档，当前问题应不会出现





## 未来可拓展方向

- 建立自动化校验流水线：在生成后自动执行编码、JSON 语法、tree 结构一致性和上传前检查。
- 扩展样本复杂度：从多条单链扩展到单样本多分支树，并补充更多边界场景。
- 形成版本化机制：对 prompt、constraint、数据集分别做版本管理，支持回归对比与快速重跑。
