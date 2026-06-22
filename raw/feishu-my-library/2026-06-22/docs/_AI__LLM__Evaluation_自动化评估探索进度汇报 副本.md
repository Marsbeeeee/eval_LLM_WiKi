<title>[AI][LLM][Evaluation]自动化评估探索进度汇报 副本</title>

# 0513 Todo Summary





# 0513汇报核心结论

1. 当前基于LLM-as-Judge在Laep平台上的自动化功能已经通过初步验收，平台目前卡点主要还是在于整体pipeline性能（耗时、批量生成稳定性等），但不影响Judge模型生成效果。
2. 使用Judge模型替代人工评估的效果仍然未知。主要卡点在缺少一次对于Judge模型的缺陷（偏见、内部一致性）和边界的严肃测评。针对此评估部门将会提前开展数据侧的准备，期待与算法沟通优先级和排期。
3. 评估部门已经根据现有资源和结论优化重构工作流，且已逐步开始推进替换，具体提效收益待评估，会持续更新。且将持续尝试Agent化的开展。

# 前言

此文档主要记录评估部门当前在自动化评估推进以及更多实现路径的探索，做阶段性汇报。

## 自动化评估背景

基于算法团队此前的一次LLM-as-Judge尝试（使用离线feature测试集，Single Prompt方案，1-5分打分制），我们已初步验证了Judge模型的可行性，同时也发现了一系列已知偏差问题。

当前阶段评估的目标是在标注平台上落地自动化评估能力，将Judge模型嵌入日常评估工作流中，实现提质与提效。

### LLM-as-Judge简要展示

历史任务：<cite doc-id="EuvKws6Yzi2yAok1VYfc73sVnCe" file-type="wiki" title="[20260204]Turn aware (with  Semi_Auto_Eval)" type="doc"></cite>

![图片展示了文档 addCriterion图片展示了LLM-as-Judge任务流程。流程共6个步骤，分别为任务接收（口头沟通需求）、测试集设计（三部门联合发散）、数据构造（<50条·单测试点）、离线推理（算法团队脚本）、人工打分（-1/0/1）、结论汇报（Turn级accuracy）。该图与上文介绍的LLM-as-Judge简要展示内容相关，直观呈现了任务从接收需求到汇报结论的全流程。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzFmNGY4MWVkYTY3NDQ1MjY1ZWU0MTVjZWI4MTI3MjNfNDI3NDFjMjAwMmZiNDY1NWRlNzI5ZjRkNzgyZTA3NzlfSUQ6NzYzOTI2OTYzNjc5MDAxMjg2OF8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

![图片 addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=N2VlNzBiMjViNjdiZTE5N2FmM2JiZGRkMDA0YjYwZjlfZGZhZDQwYWUzYzAyMzM4ZTU2MjdlYjUxNWEzNDg4OWFfSUQ6NzYzOTI2OTYzNjAyMDA3OTU4NF8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

![图片为LLM-as-Judge已知缺陷总览，位于“LLM-as-Judge简要展示”部分。图中列图片展示了感知层偏差、认知层偏差、多语言偏差、结构性问题等四类缺陷，每类缺陷下有具体问题 自动生成的示例及对应风险等级。如感知层偏差中位置偏差示例为“你好，我叫李华，今年25岁，是一名大学生”，风险等级为中等风险；认知层偏差中自我增强偏差示例为“我 addCriterion addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MmVhYjQ1YjZmMjM2ZWI1MTBkMjRkNmIwYzdmM2MzNWNfMGEyNjY1NDU3ODk4MDY5YWRmOTU0ZGJmYWY2ZjgzNjRfSUQ6NzYzOTI2OTYzNzE3NTU5Mzk2MV8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

## 自动化评估在Laep上的实现

### **2.1 平台提供的基础能力**

标注平台提供了两个核心组件来支撑自动化评估：

**Action** — 链路中的原子化能力单元，每个Action负责一个独立的操作（如调用LLM生成回复、调用Judge模型打分等）。

**Pipeline** — 将多个Action串联起来，实现批量自动化执行。

### **2.2 当前Pipeline实现**

![图片展示了当前Pipeline实现的流程。首先是Action 1，生成Key Guide并结合人工标注；接着是Action 2，调用Judge模型基于Key Guide打分；最后输出Judge评分结果，包含分数和理由。该图片位于介绍“2.2当前Pipeline实现”的部分，与上下文提到的多数据集验证通过、当前存在的问题等内容相关，直观呈现了Pipeline实现中的关键步骤，有助于理解整体评估流程及其在当前工作进展中的位置。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NjdhNzk4Mzg1YWZmNzQ2Yjc4YWNiZDM0YzJjYzRmZDRfNTFmOTc0ZGRiZTAzZTUzZGJkZDA0NzRkODc3ZGFhNDFfSUQ6NzYzOTI2OTYzNjE5MTkzMTM0OV8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

#### **2.2.1 实现情况**

- **多数据集验证通过**：turn-aware、instruction obfuscation、language switch 均人工抽出五到六条case跑通，验证可以上线。

#### **2.2.2 当前存在的问题**

<cite doc-id="MdoDwFqZKiHwZSkeq6icIxJqnBP" file-type="wiki" title="Pipline业务推动时间线及难点" type="doc"></cite>

<cite doc-id="O2ZWwwwBji5NOmkgZcnce4I8nLd" file-type="wiki" title="[0429] 初步测试跑通" type="doc"></cite>

<cite doc-id="VRE6wGOCLi5g3TkVEADccRnxnXa" file-type="wiki" title="跑通情况测试" type="doc"></cite>

- 评测需求在平台视角下的适配有一定难度，平台和我们都在积极配合中（如模型匿名化处理、Action之间的数据传递与写入等）
- 平均调用时间较*长*，且调用属于黑盒状态，无法人工干预（暂停、打断、终止），出错后需找平台排查
- 受上述限制，目前不敢做严肃的批量测试，所有验证均基于少量case抽样

## 自动化评估提效探索与现阶段结论

### 3.1 Judge模型能力初探

基于之前Turn Aware测试集用qwen3.6- plus 在线跑一次Pipeline，横向对比两个模型评分效果

<cite doc-id="ZaWJw32ZXiypEgkjecycmzB6nYd" file-type="wiki" title="[0507]Judge模型初步对比（qwen 3.6 v.s. deepseek 3.2）" type="doc"></cite>

#### 3.1.1结论摘要

##### Judge模型能力对比（Turn级别打分1-5分各分数分布图）

![图片为“模型评分差异”柱状图，展示了了qwen_3 addCriterion3.6 -plus与Deepseek_V3.2在1 - 5分各分数分布情况。横轴为分数占比，纵轴为分数。其中，“all”类别下，qwen addCriterion addCriterion3.6 -plus与Deepseek_V3.2评分占比均为50%；1分、2分、3 addCriterion3.6 -plus评分占比分别为40%、50%](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YmI1Y2ExYTU2ZmJhMDYyODEyNTQ0ZjlmZTRkNDdjZWZfZmE0N2M0MzE5MjQ2ODVjMDBmYmE4Y2I2NjQ4YjVjZTFfSUQ6NzYzOTI2OTYzNTQxNDQ2MTQxNF8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

##### 结论反思![图片展示了评估Judge的两个核心维度。左侧为人工一致率，指Judge的分数和人的分数的重合度，细分为Overall Agreement / Cohen's Kappa 、分档混淆分析、方向性偏差，且当前可基于现有测试集对比做部分验证。右侧为内部一致性，即同一批case多次跑Judge结果是否稳定，细分为同case多次推理的分数方差、相似case之间的评分逻辑一致性、偏好稳定性，目前因需Pipeline支持重复执行而无法验证。图片对上下文提到的Judge模型评估维度做了直观呈现。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MThhYjFiNzE0YTJkYzc4ZGE3YTViODM3OTY1YWI3NDNfMjJiNTlmZDdjOTM2YjliMDcxMjhkYzAzNTdjNTg4NGFfSUQ6NzYzOTI2OTYzNzMyNjU3MjUxN18xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

<callout emoji="🖐️">
**打分标准的问题** — 1-5分的分制缺乏清晰的档位定义。人类标注员自身对于每个分数的含义也没有统一的理解，Key Guide未能将各分数段的判定条件讲清楚。模型在标准模糊的情况下，自然无法给出稳定的判断。
**模型偏好的影响** — Judge模型存在业界已知的固有偏差（如冗长偏差、位置偏差等），导致部分评分结果需要人工复核，且分布未知。
</callout>

### 3.2 Key Guide优化实验

基于上述分析，进行了以下调整：

<callout emoji="🤚">
**打分制度调整** — 将1-5分改为更适配离线测试集的-1/0/1三档制，与模型层功能验证的评估口径统一。
**Key Guide细化** — 在remark中明确规定了每个档位的判定条件：什么情况判1（通过）、什么情况判-1（不通过）、什么情况判0（错误但非预期维度）。
**跨测试集验证** — 在多个测试集上抽选case进行测试。
</callout>

<cite doc-id="VxCGwAcUFiyxozkPwEOclQNTn0f" file-type="wiki" title="[0509][eval][Guide 优化尝试]mandiri_pipeline qwen3.6plus v.s. gpt5.1 v.s. gemini3.1pro-preview v.s. human" type="doc"></cite>

#### 结论

1. 跨场景（Mandiri业务机器人测试集、Language-Switch、Insutruction Obfuscation测试集各抽选5条对话）。模型的确开始展现一定的区别，因为测试集数量太少，无法排除是生成波动还是能力偏差。

<chart-refer-host-perm thumbnail-url="https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NDlkNzA0ZDE2YWZlZTQyMzRiOTJhOTdlMGQ2NTEwYjFfYTAwMGIwYzM4ZGJhY2MyNWRjOTg2YThmMWM1ZmI4NTJfSUQ6NzY1NDE1ODk3MTMzOTE4MTAyM18xNzgyMTIyNzYyOjE3ODIyMDkxNjJfVjM" token="chtcno0S7bOjbAEZiK7jRsqAoCg"></chart-refer-host-perm>

### 3.3 基于LLM-as-Judge的工作流重构

<callout emoji="🤚">
在当前的模式下，实现LLM自动化评估的替代暂不可行，每一个任务的标注流程仍需要评估人员的参与。但可以基于此进行工作流的重构。参见下图
**明显收益** — 提效提质，一个任务干两件事 （评估+评估Judge模型）
提质提现在：评估标准的一致性提升（基于统一Key Guide而非个人理解）、交付物的信息密度提升（从单一通过率到多维度结论）。
提效体现在：培训和对齐成本下降（复核比独立判断更容易上手）、打回重做的概率降低。
在Turn级别测试 + Accuracy统计口径的前提下，应该可以看到明显收益。
**推行情况** — 当前已在日常机器人测试集、验证集制作和评估中推动。
</callout>

![图片展示了基于LLM-as-Judge的工作流重构前后的对比。重构前为纯人工模式，流程为培训对齐、独立评估、质检打回、汇总交付，评估员是独立判断者，门槛高且标准不一。重构后为Judge辅助模式，Judge自动评，人工复核，再质检，最后汇总交付，评估员为复核者，门槛低、标准统一。其核心价值在于提质，即标准一致性和信息密度提升，还能提效，使培训成本和打回率降低，与上下文介绍的自动化评估提效探索及推行情况相呼应。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YjdjZDQ2NjkyMTZlOTZkMmMzMDg4NjVmOTFjMTllYzlfNjRjYjRkMjYxM2Q3ZGI2MzAwYmY4NTUxYjNhY2Y1OGVfSUQ6NzYzOTI2OTYzNzMxNTQ5NjkzN18xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

<callout emoji="🖐️">
**受限：**
1. 测试集必须先行
2. 目前仅适用于Turn级别，单轮受控生成，-1 0 1的评测标准
</callout>

## Agent在自动化的探索

### 4.1 Laep - Assistant 内置 Agent接口

当前仅限对话级别的内部Agent接入，实现形式基于所提供的LLM接口，仅支持PE手段调优。

#### 创建对话 - 对于评估场景暂无收益。

![图片展示了一个与Agent自动化探索相关的操作界面。左侧有“System Prompt”区域，显示着用于向AI系统发送的指令文本，下方有“HINTS”提示信息。中间部分是一个类似流程图的布局，包含多个节点和连线，节点上有不同标识，可能代表不同的操作或状态。右侧有“LLM Test”等板块，展示着相关测试内容及反馈信息。该图片与文档中“Agent在自动化的探索”上下文对应，直观呈现了Laep - Assistant内置Agent接口相关的操作和测试场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=Njk2NzdiZTcwODY0MDM2MzA4NmQwZGQ3YWI4M2MwNjRfYmEyNDU0MWYyMWIxZGMzYzFiOTM1MTVjYWYxYTRmNzZfSUQ6NzYzOTI2OTYzNjA1MTk3OTIwOV8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

#### 自动制作Remark - 待平台优化Agent逻辑后，存在很大可行性

![图片展示了Agent动态评估探索中，Laep-Assistant与LLM Tool的交互界面。左侧是Laep-Assistant的系统提示，包含背景信息、](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YjY5NDY0ZjQ5YjQyNWZjNDg2YzdkYjA4M2FkMTk1NjZfZmVkNWU1MzczYjFkYWE4MmU2OWE4M2VhODgxOGQwMDVfSUQ6NzYzOTI2OTYzODMzNDQwMTQ4Nl8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)

### 目前三方Agent探索

<grid>
<column width-ratio="0.484592">
![图片展示了Judge模型的评估流程。Judge模型接收System Prompt、Key Guide、评测Case上下文等输入，进行单次Prompt推理，输出判断结果（分数+理由）。下方列出已知局限，包括Key Guide粒度粗导致标准不稳定、上下文过载造成理解能力瓶颈、调优手段有限需改prompt或换模型、无外部工具限制无知识库/技能。该图与文档中介绍Judge模型评估任务的内容相关，直观呈现了模型的输入与输出及局限性。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YThjNTg0NDFjZWU3MjM0OGNhMzAwZTc3NGQwMjIzNjFfY2VlYmI4ZThkZjI4ODU0NmEyYTZkMGIzZWRmOTU1MWNfSUQ6NzYzOTI2OTYzNDg1MjQ0MTAzN18xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)
</column>
<column width-ratio="0.515408">
![图片展示了编排器Agent的评估流程。从评测Case开始，经编排器Agent，依次进行检索标准、分析上下文、提取关键关键行为特征、多维度打分、错误归类等步骤，最终输出结构化结果，包括通过/不通过判定、多维度分数、推理链路、错误类型标签、置信度等。该图与文档中介绍Agent动态评估探索的内容相关，直观呈现了Agent在执行评估任务时的流程及输出结果。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YzAyOTI4ZjgyYTk2OTBiMDNmMDdkNTk4ODk1NmIxY2ZfNzI1OTA5MjFlZTg2MWViOGRhZmU1YTZmNzhmYmViOTFfSUQ6NzYzOTI2OTYzNzgwNDcyMzE1Nl8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)
</column>
</grid>



特点：动态，自由拆解、全链路

#### 动态拆解执行评估任务记录 -- 测试需求简单，可行性已验证。

<cite doc-id="Qbo6wkbChiyg7ikqm4fcyCPYn8t" file-type="wiki" title="Agent动态评估探索-FC-Continuer 衔接词评估报告" type="doc"></cite>

#### 自动化创建测试集对话 -- 可行性已验证

<cite doc-id="Eyi2wrLnxiYliEkStDlcpukkn7R" file-type="wiki" title="[0512]ai agent自动化生产测试集报告" type="doc"></cite>

<cite doc-id="FX8Hws637ixYyDkEdFKcrk8AnJf" file-type="wiki" title="用codex作为ai agent批量化生成测试集" type="doc"></cite>

<cite doc-id="N3WBwnTZaiqln2kK3aacFWBdn9e" file-type="wiki" title="codex动态生成完整conversation规范协议" type="doc"></cite>

#### 核心结论

此处探索讨论的Judge已经是一个新的框架的，只是一个设想，可行性暂不可评估，~~且不知道依托于哪个平台。~~

更新：关于Agent化的评估，Laep后面还会开放他们的CLI，因为我可以在我自己的库里维护一些roadmap、Skill之类的东西，通过沉淀我的评估知识库等东西，实现一个初步的流程。

## 后续计划

1. 评测框架的丰富与完善---Most Urgent

   1. 打分的原则 -- Benchmark![图片展示了Benchmark维度体系（基于Feature维度），包含知识问答（生成）、Function Call、流程指令遵循、安全、特殊能力、轮次感知、语言切换、风格本地化、综合理解能力等九个维度。其中知识问答是基于知识库的回答生成能力，Function Call涉及工具调用与参数提取等。每个维度通过对应feature的Turn通过率，按维度加权汇总得出整体Benchmark分数。此图与后续计划中评测框架丰富完善里打分原则的Benchmark内容相关，解释其打分依据的维度体系。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ODQ1MWI0ODYwNjc0YzFiZTcwNjIwMmEwY2ZiMTIzYmZfM2Q2NTY2NzM5NDQ4YTU0MTE4YmJkMjkyMmZhZDIyOWVfSUQ6NzYzOTI2OTYzNDQ5MzQ2NzYyMl8xNzgyMTIyNzY2OjE3ODIxMjYzNjZfVjM)
   2. Session级别的机器人评测框架
2. 数据侧测试集先行---日维度的定时任务

   1. 所有历史测试集的Judge适配（remark），同时必须注重remark的版本管理方便及时修改
   2. Judge模型元能力测试准备
3. 评估历史知识沉淀与维护 -- Chat-Eval Agent可能性准备

   1. Laep CLI的开放 - 已和平台沟通，预计周五拉会。
