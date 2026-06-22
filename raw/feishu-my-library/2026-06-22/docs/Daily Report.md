<title>Daily Report</title>

# 2026/6/17

- 制定 key guide 规范 txt 文件 <cite doc-id="MvU7wcCwViOFR5kAjn0cOFGpnvd" file-type="wiki" title="key_guide_prompt" type="doc"></cite>，然后使用 txt 文件借鉴已有 py 文件 run_qc_local 去挑选两条 en 对话数据生成对应的 key guide <cite doc-id="N7PSwY9LTiKgqLkHXA9cXxAannb" file-type="wiki" title="key_guide_results" type="doc"></cite>
- 将 codex usage tracker 从各自分工进行，通过脚本可生成单人<cite doc-id="GRTewhKfLiUrALksZLqc1KC5nrh" file-type="wiki" title="Codex 用量任务归因报告" type="doc"></cite>和多人报告<cite doc-id="QmbZw4NIQifKeek9ncmcsSu6nec" file-type="wiki" title="Codex 用量多人汇总报告" type="doc"></cite>来统计 codex token 用量，并且制作 codex pro x20 token 估算与人员使用文档<cite doc-id="IL5HwFpPZiiJ1jkTSYDc2rI9nyb" file-type="wiki" title="[0617] Codex token usage tracker 汇总" type="doc"></cite>

# 2026/6/16

- 对 badcase 根因分析 skill 进行微调，将其输出的报告 format 进行结构化调整，使其可读性更高，用于更多自然语言分析，同时更新 skill 推进报告

  - <cite doc-id="UIDvwu596iFJSjkx7IYcIFQnnPh" file-type="wiki" title="[0611]LLM badcase 根因分析 skill 推进报告" type="doc"></cite>
  - <cite doc-id="XAHewDrpdiUnabkyFPjcQEbfnLf" file-type="wiki" title="[0616]badcase 根因分析测试案例" type="doc"></cite>
- 尝试推进 eval llm wiki，参考 karpathy 提出的 llm wiki，建了一个 eval 的 repository 在 github，并且创建 readme 将初步工作流和其项目安排进行梳理

  - <cite doc-id="Lp8CwJqqeiQ60QkTkyIcht3anLh" file-type="wiki" title="[0616] eval llm wiki README" type="doc"></cite>

# 2026/5/28

- 对自动生成的remark的customer service测试集去跑 pipline
- 三个Judge模型数据整理：1. 三Judge模型一致率总结 2. H200 Accuracy 统计，顺便人工打分准确率。

# 2026/5/27

- Customer service 人工打分结束，key guide 问题已解决

# 2026/5/26

- 将原始数据进行整理，然后交给数据测对生成模型进行打分
- 将优化过的自动生成remark的数据集进行，badcase整理（进行中）
- 测试了一下pipline，发现key guide 不生成问题，寻找是测试机问题还是action问题

# 2026/5/25

- 将原始数据进行清洗和将其他测试集的数据转移进去，制作多个脚本
- 修改 customer service badcase 文档

# 2026/5/22

- 根据昨天的收集到的 badcase 发现 qwen 模型会把 should criterai 当成 must 来评分，这导致某一些节点会有分数错误，那今日尝试去修改 qwen 的 prompt 强约束让其先去看 must 判断，然后再去看should，发现没有明显改善，后提出去修改 key guide 的 prompt 看看能否有明显的改善，但看下来并无明显改变，可能会作为不可修复问题
- 测试 agent 自动写 remark 可行性跑了 32 条对话，去自动化生成 remark 并查找 badcase <cite doc-id="LNrTwXFovi5HpQk9PBTccZNinwb" file-type="wiki" title="[0522]自动生成 remark 测试记录文档" type="doc"></cite> 看看后续可优化方向

# 2026/5/21

- 将跑完 4 个生成模型回答的 customer service 测试及数据拉取成 excel，并进行准召率表格分析<cite doc-id="EBuDwaOD9iK7wYkkJYicddGXnqq" file-type="wiki" title="[20260521]Customer-Service—Judge-V2" type="doc"></cite>
- 统计测试集中 badcase 并分类整理成文档，按可修复不可修复进行统计，并制定下一步任务推进<cite doc-id="Erxswj8i6iFWc4kL6cxcyeHbnBg" file-type="wiki" title="[0519/0521]customer service judge 模型 badcase 记录分析" type="doc"></cite>

# 2026/5/20

- 例会讨论当前要任务，当前首要任务是两个测试集：Mandiri和customer_service
- 当前customer service重新在原本23条对话上新跑了4个需要测试的模型，分别是qwen-3.6-27b、voyager-1.6、gemma-4-31b、gemma-4-26b，然后进行了人工评分，然后跑完了pipline
- 将旧的只包含2个生成模型的数据进行复查，然后找一些评分差异大的badcase记录在文档<cite doc-id="Erxswj8i6iFWc4kL6cxcyeHbnBg" file-type="wiki" title="[0519]customer service judge 模型 badcase 记录分析" type="doc"></cite>
- Mandiri数据集clearance，只保留completion里面一个gemma4-26b生成模型的回答，然后剩下3个模型gemma-4-31b、h200_01_fc_9010、gemma4-26b-a4b-run28a1生成对话

# 2026/5/19

- 将5/18跑完pipline的数据拉成excel表格<cite doc-id="UQVawFXkbi4ll2kCiFjcV3IYnjj" file-type="wiki" title="cs_human_score_808_682_no_lang_20260519083011_23" type="doc"></cite>，并粗略将一些 judge 模型评分差异大的 case 挑出来看一下 judge 模型评分的原因
- 将原本跑完 pipline的数据，进行人工打分
- 指定之后评估 workflow，之后拿到数据集先用 agent 把节点 content 复制一份放到 completion 里面，然后再交由数据组进行 remark 标注和 human score，最后再返还给我们去跑生成模型和 pipline

# 2026/5/18

- 发现 key guide 生成内容大体为 system prompt 内的内容和步骤，推导出是否 key guide 无法读取当前 node 节点的内容，对 key guide 内节点的动态路径进行修改调整，使其可以读到 node 内容。用原本的一条 customer service 对话进行测试，key guide 生成正常，criteria 可生成关于 system prompt 和 node 内容的规定
- 整理制作 Judge

# 2026/5/15

- 整理了近期 pipline 成果报告<cite doc-id="GP84w8NNSi7j7ykln28clELUnmg" file-type="wiki" title="LLM as Judge Pipeline项目进展汇总（2026年4月29日-5月15日）" type="doc"></cite>
- 调整 key guide 尝试只从客观层面来指定criteria 尝试跑 pipline ，看看模型表现发现生成的的 criteria 午饭完全脱离主观性。

# 2026/5/14

- Ai agent 动态生成测试集任务交接给 rebecca 和 kystel，可能未来会替换 PE 给的对话 case
- 拿到 UOB 测试集 batch 3；7 条对话，测试 pipline 之前处理数据时，发现卡点（已解决，不考虑用gt替换，将gt删掉或保留在completion）：

  - 原本assistant节点content就是文本，但正确的应该是call function是放在completion内部的，现在如果跑pipline，把call function替换到content后，user应该是有个response，那么原本user的文字回答就错了
  - turn 12：https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=512&dialogueId=13452

  <grid>
  <column width-ratio="0.539984">
  ![图片图片展示了对话系统开发平台的界面，左侧为对话流程图，显示了user、assistant等节点及call function等操作。右侧是编辑面板，有Role、Content、Tool Calls等区域，其中Tool Calls下有language_detection工具，检测等参数。下方有生成的gpt-4o-min_1、generate_qwen1.5-plus等任务信息。该图片与文档中提到的“原本assistant节点content就是文本，但正确的应该是call function是放在completion内部的”等内容相关，直观呈现了对话系统中call function的使用场景。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MzhmZjQzMDYxNmRlNmI5YzZjNDhlNGNjNzBmZDI4OGFfMTJmNjBlYzAwYzBiNTljZTQ4MGJkNGE3YmJkYmZmNjVfSUQ6NzYzOTYyNjQ2NzY5OTkzNjIyN18xNzgyMTIyNzU3OjE3ODIxMjYzNTdfVjM)
  </column>
  <column width-ratio="0.460016">
  ![图片展示的是一个对话系统界面的界面，左侧为对话流程图，显示了用户和助手的交互过程，如“Buat, tada”](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZDg3NmRmMmE0ZWVmYzZlYmExZDQwMmRlYzViNDYyYWJfY2U4N2UzMTVkMTNkZTQxN2QxMjRhNWRkOGFkMzhlNTNfSUQ6NzYzOTYyNjQ5MDYzMjIyNzc4OF8xNzgyMTIyNzU3OjE3ODIxMjYzNTdfVjM)
  </column>
  </grid>

  - Turn 16：https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=512&dialogueId=13451

  - 同样原本assistant节点调用了function，但正确的应该是文字回答是放在completion内部的，现在如果跑pipline，把文字替换到content后，user原本response消失，那么原本user节点报错
  - Turn 18：https://test-laep.airudder.com/#/datasets/dialogue_detail?datasetId=512&dialogueId=13450

<grid>
<column width-ratio="0.500863">
![图片展示了UOB测试集的对话流程及关键节点。左侧为对话界面，显示了User、Assistant的交互，如“ah ha ah不理解”“language_detection”等。右侧是Edit Panel，呈现了Completion、Real Calls等信息，其中Real Calls下有language_detection，其备注指出用户不想转移人工代理时，应询问何时支付。该图与上下文紧密相关，直观呈现了上下文中提到的用Turn-aware key guide action跑UOB测试集时，因key guide过多读取上下文导致的问题。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=YWUzOTRmMTBkNDRmY2RhOWY0OTZhYjBiZDQyMTVjMDhfOGVlMjllNjg3MmNiZWU3YmQ1YTIxNjgxMzk0Njc5ZDVfSUQ6NzYzOTYyOTQ2ODk5NDkxNTU0M18xNzgyMTIyNzU3OjE3ODIxMjYzNTdfVjM)
</column>
<column width-ratio="0.499137">
![图片展示的是一个对话系统界面，左侧为对话历史，显示了User、Assistant、LLM Tool等角色的交互内容，如“我想要一个蛋糕”“好的，您想要什么口味的蛋糕？”等。右侧是编辑上下文编辑面板，显示了Role为User、Content为空、Tool Calls为禁用、Remark为空等信息。该图片与文档中提到的用之前turn-aware的key guide action跑UOB测试集发现key guide过多读取上下文的情况相关，直观呈现了对话系统中对话历史及上下文编辑的界面情况。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=ZTUxMTZhZmQ5MDUzMDIzNzk3OWUxZjU3OTQ3ZDE4ZDFfMzVlNjk0NmYyOTkwMDMwOTEyYTliZmE1ZGZmMjg0NTJfSUQ6NzYzOTYyOTYwNjM3OTQ5MDUwM18xNzgyMTIyNzU3OjE3ODIxMjYzNTdfVjM)
</column>
</grid>

- 用之前 turn-aware 的 key guide action 去跑 UOB 测试集发现，key guide 过多读取上下文，其原因是：

  1. 明确喂了全量历史  
  原文输入里有 Dialogue: {{{dialogue_branch}}}，模型天然会把它当主要证据源，不会只看当前 turn。
  2. 把“turn aware”写成了“必须看完整历史”  
  原文有一句接近“before writing EXPECTED and CRITERIA, review the full dialogue history…”。这会强驱动模型做全局整合，而不是局部判断。
- 对 key guide action 内容进行调整，创建一个新的action（guide_writer_review[0]\_en_for_UOB），只读 system prompt 和 当前轮次的 remark，包括检测是否当且节点应该调用function。当前跑了一条对话（包含4个模型生成的对话），看下来新 key guide 可以运作，内容也没问题。
- 把gt留在completion，新跑两条对话，judge模型挂断了2个，留下一个qwen内容有问题，明日看一下是 action 问题还是 laep 平台的问题

![图片展示的是LLaMP平台界面，左侧为对话流程图，显示用户、工具、助手等节点及对话轮次。右侧是LLM Tool区域，呈现对话内容、工具调用、助手备注等信息，如“Saya faham, Bolil saya tahu kompa anda terlambat”等对话内容，以及“transfer_to_human”等工具调用。下方是Review Nodes部分，有“generate_gpt-40-mix_2”等节点，还显示了key guide等信息。该图与文档中对LLaMP平台的介绍及对话流程等内容相关，直观呈现了平台操作界面。](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=MjM2ZGExMDhlMWZlY2FlY2EyNTRkYjQwNjQwMzhkZGRfZGViNmI2ODVjODE0ZDhiNjhmNjk3ZGNiNTJkMjJhZWZfSUQ6NzYzOTY4ODM0MDc5OTQwOTA5NV8xNzgyMTIyNzU3OjE3ODIxMjYzNTdfVjM)

# 2026/5/13

- 将近期制作的pipline和ai agent批量生产测试集相关的报告文档，进行复盘修改，将缺失的数据进行补齐，并制作可视化图表，对文档内容进行更有力的支撑。
- 从laep上将eval_mandiri_pipeline（id 508）数据拉取下来制作成表格，查看每条模型之间打分差异大的节点。并定位回到 laep 平台上查找评分差异大的原因是什么，制作成文档报告。
- 寻找judge model跨语言理解相关的benchmark，跨语言理解主要是看模型对于不同语言的评分能力是否稳定，或者说是否有明显差异

  - 找到两个已公开benchmark，XNLI和XQuAD，将其信息整理成文档内容
- 测试laep平台更新之后的agent生成remark的能力有没有被改善，当前使用测试集 id 497

  - 测试下来发现依旧无法直接在remark内直接生成可用内容，还有就是agent默认当前节点content内容为当前节点正确答案，remark生成内容受其影响很大

# 2026/5/12

- 测试ai agent（codex）动态生成完整测试集的能力，已上传laep平台测试成功，并将ai agent能力测试过程与结果写成文档报告进行记录。
- 制作近期pipline工作流时间线，文档内记录pipline调整期间的痛点
- 将pipline新功能进行测试并尝试跑pipline，当前action将score数据结构更改成list形式

  - 测试完pipline之后发现qwen模型的分数不出现在matrices中，目前已解决
- 将已有remark的mandiri数据集，进行人工评分标注，为之后的pipline做前期准备

# 2026/5/11

**ai跑数据集**

- Fc-continuer的数据集放到ai上面跑一下
- 有没有remark，拆数据结构
- 判断fc正不正确
- 承接有但不能太长（1-10）（合不合理）

**agent测试集制作**

根据system prompt和user对话目标生成user对话

- PA 提供之前的业务机器人 bankaya 的数据集

  - system prompt
  - 每个对话的目标（意图大标签）

# 2026/5/9

- 更改 key guide action 中 criteria 规范，将要求进行分层处理（must/should），并新增三个 action 接口（qwen/gemini/gpt）对已进行人工标注和评分的数据集跑了pipline。横向评测改善keyword prompt之后不同Judge模型的评分能力稳定性是否有所改善，并制作报告。

# 2026/5/8

- 将Judge模型对比的原始数据，做成可视化的形式，通过分析可视化图表，对当前Judge模型的judge能力进行评估。
- 将可视化图表和对比发现评分差异大的点整理成报告，并对当前已有信息进行分析与总结。同时确定后续工作的改进方向。



# 2026/5/7

1. 参与讨论自动化生成guide的可能性探索，测试assistant对于节点remark内容生成能力
2. 使用Judge llm和deepseek模型对于当前模型表现进行打分，看评分差异大的原因是什么，进行文档的整理
3. 对之前老旧数据库进行remark修改，将conversation中逻辑有错误的地方进行修正或者loss标注



# 2026/5/6

1. 获得chat bot、laep账号权限；登录账号无异常情况，数据都能正常显示
2. 基本知晓各平台（chat bot/laep）操作流程，包括各功能按键对应作用，还需实操熟练
3. 了解业务侧各规则维度、标签（幻觉，未调用等）与模型侧不同标注方案（Non-strict/strict)
4. 阅读leap平台使用手册，大致了解如何在laep上实施评估SOP
