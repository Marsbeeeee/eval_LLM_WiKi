# [0611]LLM badcase 根因分析 skill 推进报告

## 一、Skill 背景

当前这个 `prompt-compliance-optimizer` skill 的核心目标，是把 system prompt compliance badcase 的处理链路标准化。

在客服类 LLM 测试里，当前我们会遇到的一些不可接受的 badcase 包含：

- 某类用户回复必须进入指定流程分支
- 某类场景必须输出 exact message
- 某类触发条件必须停止当前协商
- 某类状态必须调用或禁止调用工具
- 某类日期、金额、身份、热线等事实不能编造或改写

人工做这类评估时，最大的问题是：如果当前让人工去修复这些 badcase，不仅仅只看对话还需要结合 system prompt，然后根据自己的理解去修改可能为 system prompt 导致的一些 badcase，然后再拿到 chatdemo 上去测试是否对话真的被修复。但这过程中很难有明确的标准去知道 badcase 是否被修复，尤其当测试集放量后，每个评估人员对 badcase 的理解、分析粒度和结论质量都可能不一致。

所以这个 skill 要解决的是一条完整链路：

> 评估 -> 分析 -> 实验 -> 结论

希望把原本人工逐步操作、人工判断、人工总结的过程，沉淀成可以批量执行、可追踪、可复盘的 workflow。

---

## 二、当前 Skill 进度

当前 skill 已经推进到对**单条/批量**对话进行评估的阶段。

已完成的能力包括：

- 批量扫描多个 JSON conversation 文件
- 自动识别 strict system-prompt-compliance 候选 badcase
- 生成稳定的 review 文件
- scan 后保留 human review gate，不自动修复未批准 case
- 用户批准后，对 approved badcase 执行 prompt edit 和 targeted rerun
- 生成 updated conversation
- apply 后自动执行一次 residual scan
- 用 residual scan 判断是否真的修复
- 生成包含自然语言根因、表层证据、深层 backend/meta/logprob 信息的 conclusion
- 将输出文件收敛为稳定文件名，避免每次测试堆积大量历史文件

当前已经验证通过的工程状态：

- 批量扫描/修复，流程已跑通
- conclusion 格式已调整为 root-cause-first
- 输出目录清理和稳定文件策略已完成
- Python compile check 和 skill validate 已通过

---

## 三、设计链路：评估 -> 分析 -> 实验 -> 结论

当前 skill 的设计为，拆成四个节点：

1. **评估**：判断对话中是否存在候选 badcase
2. **分析**：基于 prompt rule 和对话证据解释 badcase 为什么成立，并给出初始修复方向。
3. **实验**：对人工批准的 badcase 做最小可行修复，并重跑目标 turn
4. **结论**：结合 updated conversation、residual scan、meta 信息和 logprob 信息，判断修复是否真实有效，并解释修复成功或失败的深层原因

将 workflow 拆分的目的是为了让，每个节点只负责一类判断，且每个节点都有自己的证据来源记录

**当前 skill 侧最大的特点**：

- 操作更自由，评估人员不需要被固定页面流程限制，可以在 scan、analysis、apply、conclusion 任意节点继续追问
- 评估人员可以随时要求展开某个 case 的规则证据、用户触发、assistant 违规点、prompt diff、rerun 记录、meta 信息或 logprob 解读
- skill 可以根据评估人员的新问题，把已有 conclusion、review、updated conversation 和 backend evidence 重新组织成更适合当前判断的摘要
- skill 不只展示固定页面结果，而是可以在同一条链路中继续做信息提炼、根因解释、残留 case 分析和下一步策略判断

skill 让评估人员在完整链路中有更高的操作自由度：既能批量跑流程，也能随时对某个节点继续问“为什么”“证据是什么”“下一步怎么做”

---

## 四、评估节点：为什么可信，为什么是真的

### 评估节点做什么

评估节点负责从 conversation JSON 中找出候选 badcase

它不会泛泛判断“回答好不好”，而是检查 assistant 是否违反 system prompt 中明确写出的规则

它会读取：

- system prompt
- conversation turns
- user trigger
- assistant response
- tool definitions

然后输出候选 badcase，包括：

- case id
- source file
- turn index
- badcase category
- violated rule
- 用户触发证据
- assistant 违规证据
- recommendation
- scan round id

### 为什么评估结果可信

评估节点可信的关键在于，其不是根据模型自我经验去动态判断 assistant 是否为 badcase，而是绑定到具体 prompt rule

比如本次测试中，`LLM_ID_Bahasa_PTP100_DAX_Prd_PAtest.json` 的 turn 9 被判为：

`late_payment_proposal_not_rtp_closing`

原因是：

- system prompt 规则要求：当用户提出超过最大允许日期的付款时间时，assistant 必须立即进入 `RTP_Closing`，不能继续协商或尝试调整日期
- 用户触发证据：用户说 `Mungkin bulan depan saja.`
- assistant 违规证据：assistant 没有直接进入 `RTP_Closing`，而是继续解释和追问新的付款安排

评估节点判断为“明确规则是否被违反”，不是主观喜好

### 为什么评估结果是真的

评估结果可信，核心原因是每个 candidate badcase 都不是凭主观感觉生成的，而是绑定到可追踪的证据链

评估结果通常包含三层证据：

1. **对话表层证据**可以直接看到触发该 case 的 user turn 和对应 assistant turn 原文也就是说，评估人员可以回到原始 conversation 中确认：用户到底说了什么，assistant 到底回了什么
2. **规则对应证据**每个 badcase 都需要绑定明确的 violated rule、workflow branch、tool rule、exact-message requirement 或 fact constraint它不是只写一句“对话不合规”，而是说明 assistant 违反了 system prompt 中哪一条具体要求
3. **结构化记录证据**每次 scan 都会生成结构化记录，包括 case id、file、turn index、error type、source、evidence、recommendation 和 scan round id这些信息会写入 `batch_review.json` 和 `batch_review.md`，后续 apply 也会基于这些 case id 继续推进，保证 scan -> review -> apply -> residual scan 的链路可追踪

因此，评估节点的可信度来自：

- 原始对话可核对
- prompt rule 可定位
- badcase 分类可解释
- case id 和 scan round 可追踪
- 后续 apply 可以沿用同一批 case 继续验证

评估节点的目标不是直接证明“已经修复”，而是把候选 badcase 以可审核、可追踪的方式筛出来，并为后续人工 review 和实验修复提供稳定输入

---

## 五、分析节点：为什么根因分析是真的、可信

### 分析节点做什么

分析节点是基于 scan 阶段发现的 badcase，进一步判断：

- 这轮违反的是流程分支、exact message、工具调用，还是终止条件
- 用户输入是否确实触发了某个明确条件
- assistant 是没识别触发条件，还是识别后走错分支
- 当前 prompt 是否需要更明确的分支约束
- 初始修复方向应该更偏 prompt edit、conversation-only rerun，还是继续人工确认

### 为什么分析可信

分析节点可信，因为其不是脱离上下文猜原因，而是依赖 prompt rule 和对话表层证据

**表层行为回答**：

- 用户说了什么
- assistant 回了什么
- 哪条 prompt rule 被违反

**规则证据回答**：

- system prompt 是否存在明确要求
- 用户输入是否触发该要求
- assistant 是否违反该要求
- 这个 violation 应该归到哪类 badcase category
- recommendation 应该指向哪类 prompt/routing 修复

![图片展示的是LLM_MV_PAX_Multilingual_PTP100_Prod_PATest7.json文件内容，包含文件路径、扫描轮次、文件状态、bad和badcase数等信息。重点中“48e0dd50b50505”对话片段，呈现了User turn 13的沉默及Assistant turn 14的回复，指出违反了“如果用户第三次连续保持沉默，assistant必须停止重复case details，不再继续提问，并立即输出专业、礼貌的call addCriterion](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=OGUzMGYxODY5YTQxNzllNjhmMWJlZWFkZGE1M2Q5Y2ZfZWZlOGU5N2YwODQ3MDM5YmM1OTRjZGI2ODJhYWExYjNfSUQ6NzY1MTkyOTY5OTg4Njg0NDg1OF8xNzgyMTIyNzc3OjE3ODIxMjYzNzdfVjM)

分析节点能确认的是：

- 用户输入 `<silence>` 触发了 silence handling 场景，并且根据前序对话已经构成第三次连续 silence
- system prompt 要求：如果用户第三次连续保持沉默，assistant 必须停止重复 case details，不再继续提问，并立即输出专业、礼貌的 call-termination script，最后追加 `<dialog-end>`
- assistant turn 14 没有直接进入 call-termination / dialog-end 分支，而是继续询问用户是否能听到，并再次追问是否可以今天结清全额
- 因此该 case 可以归类为 `violation_of_silence_handling_rule`
- 初始 recommendation 是加强 silence handling branch，使第三次连续 silence 后必须立即停止追问、停止重复 case details，并执行完整的终止通话话术 + `<dialog-end>`

这些信息说明，分析节点的可信度来自“规则触发 -> assistant 行为 -> violation 分类 -> 修复假设”的可见证据链

### 分析节点的价值

分析节点的价值是在实验前形成一个明确、可验证的修复假设

它不会只停留在“这个回答错了”这一层，而是会进一步判断：

- 用户输入触发了什么规则或流程分支
- system prompt 对该触发条件有什么明确要求
- assistant 实际走错了哪个分支、漏掉了什么动作，或调用了什么不该调用的工具
- 这个 violation 应该归到哪一类 failure type
- 后续修复应该优先走 prompt edit、conversation-only rerun、targeted rerun、tool correction，还是 missing-ground-truth handling

因此，分析节点的输出不是泛泛的改进建议，而是一个可以被后续实验直接执行和验证的修复方向

分析节点的输出会成为后续 Repair Plan 的基础：它帮助确定 root cause、patch target 和 verification cases。后续实验只需要围绕这个假设做最小修复，并用 updated conversation 和 residual scan 验证这个假设是否成立

---

## 六、实验节点：做了什么，为什么实验真实

### 实验节点做什么

实验节点只处理人工批准的 badcase。scan 阶段发现的候选 badcase 不会被自动修复，必须等评估人员明确批准后，skill 才会进入 apply

**批准方式是：**

- 批准全部候选 badcase，例如输入 `all` / `approve all`
- 批准指定 case id
- 批准指定文件或指定范围

进入 apply 后，实验节点会围绕 approved badcase 做最小可验证修复，而不是无边界重写 prompt。

**典型动作包括：**

- 为每个 approved badcase 构建 Repair Plan
- 判断修复方式是 prompt edit、conversation-only rerun、targeted rerun，还是两者结合
- 对命中的 prompt 分支做最小必要修改
- 对目标 assistant turn 做 targeted rerun
- 必要时执行 required-tool replacement 或 deterministic replacement
- 生成 updated conversation
- 执行一次 post-apply residual scan
- 生成 batch apply conclusion

实验节点的目标不是证明“我改过了”，而是验证“改动是否真的让 badcase 消失”

### 为什么实验是真实可信的

实验节点可信，关键在于它保留了可验证的运行证据，而不是只输出自然语言结论

1. **有人工批准证据**

scan 后不会自动修复。只有在人明确批准 approved cases 后，skill 才会执行 apply。这样可以避免模型擅自修改未确认的候选 badcase

1. **有 Repair Plan 证据**

每个 approved badcase 在 apply 前都需要形成修复计划，包括 root cause、patch target 和 verification cases

这样可以证明本轮修复有明确边界，不是泛泛地让模型“优化一下 prompt”

1. **有 prompt 变化证据**

如果本轮涉及 prompt edit，conclusion 会记录 prompt hash 变化、prompt version 和 prompt edit summary  

这可以证明目标 prompt 是否真的发生了变化，以及变化大致针对哪个规则或分支

1. **有 targeted rerun 证据**

如果本轮涉及 rerun，conclusion 会记录 rerun target turns  

这说明 apply 不是重跑整段对话，而是围绕命中的 assistant turns 做受控实验

1. **有 backend request 证据**

conclusion 会记录 backend、model、request id、rerun error 等信息  

这些信息可以追踪模型请求是否真实发生，也可以定位失败是来自模型输出、工具调用配置、后端参数，还是 prompt 约束不足

1. **有 updated conversation 证据**

apply 会生成对应的 `*_updated.json`

评估人员可以对比 original conversation 和 updated conversation，确认目标 turn 是否真的发生了行为变化

1. **有 residual scan 证据**

apply 后会执行一次 residual scan

residual scan 是判断修复是否成功的核心依据：如果 residual scan 仍然命中同类 violation，就不能只因为 prompt edit 或 rerun 执行过而宣称修复成功

### 实验结果如何表达

实验结果通常不会只写“成功”或“失败”，而是用结构化指标说明本轮修复状态：

| 指标 | 含义 |
|-|-|
| approved badcase | 人工批准进入 apply 的 badcase 数量 |
| applied badcase | 实际执行修复动作的 badcase 数量 |
| verified fixed | 经 updated conversation 和 residual scan 验证通过的 badcase 数量 |
| residual badcase | apply 后仍然残留的 badcase 数量 |
| verification failure | 已尝试修复但 residual scan 仍未通过的 case 数量 |
| unsupported case | 因缺少 ground truth、工具定义或业务事实而无法安全修复的 case 数量 |

如果所有 approved badcase 都通过 residual scan，可以判定为 `verified fixed`

如果部分通过、部分残留，则判定为 `partially fixed`

如果 apply 执行后 residual scan 仍然没有关闭关键 badcase，则判定为 `not fixed` 或保留 verification failure

### 实验节点的价值

实验节点的价值在于，它把“修复动作”和“修复成功”区分开了

prompt edit、targeted rerun、request id、updated file 都只能证明实验被执行过；真正证明 badcase 是否被修复的，是目标 turn 的可见行为变化和 post-apply residual scan

因此，实验节点可以回答三个关键问题：

- 这次到底改了什么？
- 改动是否真的影响了目标 assistant turn？
- residual scan 是否证明 badcase 已经消失？

这使 badcase 修复从主观判断变成了可追踪、可验证、可复盘的实验闭环

---

## 七、结论节点：有什么特色

### 结论节点做什么

![图片展示的是LLM bad 自动生成系统中结论节点的输出内容。上方显示Apply已完成，结论是partially fixed，本轮批准并应用了6个badcase。下方列出多个JSON文件及MD文件。接着是badcase诊断及后台证据，分析PTPI00_Prot_PATest6的两个undefined tool call已通过替换修复，PTPI00_Prot_PATest7的silence handling case已通过，但language detection undefined call仍残留。还上下文关系](https://internal-api-drive-stream.feishu.cn/space/api/box/stream/download/authcode/?code=NTJjNzg2YjAzOTZkMGVjMzgzZWNlZWE3MTFmMzYwYmJfNjc5NWQ2OTg5ZWM5Mzc1ZDQ3ZWNhMjI0Mzg1MmJkZTZfSUQ6NzY1MTkzMDYxMzQwNjg2MjMxNV8xNzgyMTIyNzc3OjE3ODIxMjYzNzdfVjM)

结论节点负责把本轮 scan、apply、rerun、residual scan 的结果收束成可读结论。

当前 conclusion 固定包含三部分：

1. `Verification Verdict`
2. `Badcase Diagnosis And Backend Evidence`
3. `Next Action`

### 特色一：先给自然语言根因

当前 conclusion 会先给出 `Root cause analysis`，用自然语言解释本轮 badcase 为什么成立、为什么修复成功或为什么仍然失败

它不会直接把 request id、prompt hash、logprob、rerun detail 这类底层信息堆给评估人员，而是先回答：

- 这个 badcase 违反了哪类规则？
- 用户输入触发了什么条件？
- assistant 实际走错了哪个分支或漏掉了什么动作？
- 本轮修复为什么有效，或为什么仍然没有生效？
- 问题更像是 prompt branch priority、tool-call state、exact-message 缺失、ground truth 缺失，还是 backend rerun 配置问题？

这种 root-cause-first 的结构比单纯展示 logprob、request id 或 raw rerun output 更适合人工阅读

### 特色二：同时保留表面可见信息

conclusion 会保留 surface evidence，也就是评估人员可以直接从对话和 residual scan 中看到的信息

典型 surface evidence 包括：

- 哪个文件
- 哪个 case id
- 哪个 turn
- badcase / residual category
- approved、applied、fixed、residual、unsupported、verification failure 的数量
- user trigger 是什么
- assistant 原始行为是什么
- updated conversation 中目标 turn 是否发生变化
- residual scan 是否仍然命中同类 violation

这部分信息的作用是让结论可以被人工快速核对

### 特色三：结合深层 meta 信息

conclusion 也会保留 deep evidence，用来证明本轮实验确实发生过，并且可以被追踪

典型 deep evidence 包括：

- backend / model
- prompt hash 变化
- prompt 是否发生变更
- prompt edit summary
- rerun target turns
- rerun errors
- request ids
- tool-call state
- round ids
- updated file path
- logprob summary

这些信息的价值是可追踪、可复盘、可定位问题

如果修复失败，deep evidence 可以帮助判断失败来自哪里：是 prompt 没改到关键分支、targeted rerun 没真正执行、工具调用配置不完整、模型稳定偏向错误分支，还是缺少必要 ground truth

### 特色四：结合 logprob 信息，但不滥用

当前 conclusion 可以使用 logprob interpretation，但不会把 logprob 当成正确性证据

logprob 的作用是辅助解释模型生成某个输出时的置信度状态，例如：

- 模型是否高置信地产生了错误分支
- 错误是否更像稳定 routing preference，而不是随机抖动
- 低置信 token 是否集中在关键动作、工具调用或分支切换位置
- 当前输出是否显示模型对某条话术或某个分支有明显偏好

但 logprob 不能证明修复成功，也不能推翻 residual scan

真正判断是否修复的依据仍然是：

- Updated Conversation 中目标 turn 的可见行为是否正确
- apply 后唯一一次 residual scan 是否清零
- residual badcase 是否仍然命中同类 rule violation

因此，logprob 在 conclusion 中是诊断辅助信号

### 特色五：会给下一步动作

当前 conclusion 会给出 `Next action`，根据 residual scan、verification failure、rerun behavior 和 backend evidence 选择下一轮最小可验证动作

当所有 approved badcase 都通过 residual scan 时，next action 会建议停止本轮优化，保留当前 updated prompt / conversation，不继续对同一批 case 重复修复

当仍有 residual badcases 时，next action 会说明：

- 下一步应该处理哪些 residual cases
- 是否应该走 prompt edit、conversation-only rerun、deterministic replacement、tool/workflow correction，还是 unsupported / missing-ground-truth handling
- 为什么当前证据支持这个动作
- 不应该重复处理哪些已经 verified 的 case
- 下一轮 residual scan 的验收标准是什么

如果用户批准继续，skill 会从当前 `batch_apply_conclusion.json` 进入 residual continuation，只处理残留 badcase，不用重新扫描原始文件或重复修复已经验证通过的 case

这种设计保证了优化链路是连续的：scan 发现问题，apply 执行最小修复，residual scan 判断结果，conclusion 给出下一步可执行策略

---

## 八、最终总结

当前 skill 的核心价值，是把 badcase 分析从 “人工看一段对话，然后主观判断哪里错” 推进到 “基于规则、实验和证据链的闭环判断”

四个节点各自解决不同问题：

| 节点 | 解决的问题 | 可信来源 |
|-|-|-|
| 评估 | 找出候选 badcase | system prompt rule + user/assistant 表层证据 + scan artifact |
| 分析 | 判断为什么错 | violated rule + prompt/rerun/meta/logprob/residual evidence |
| 实验 | 验证修复是否生效 | prompt hash + request id + targeted rerun + updated conversation |
| 结论 | 输出可读判断和下一步 | residual scan + surface evidence + deep evidence + logprob interpretation |

最新测试结果说明：当前 skill 已经能完成从 scan 到 conclusion 的完整链路，并且能区分 “已执行修复” 和 “真正验证修复”。本次 5 个 approved badcase 中，4 个通过验证，1 个 residual case 被定位为 branch-priority / routing failure。这个结果说明 skill 不仅有能力发现 badcase，也能解释为什么修复失败，以及下一步应该采用什么策略继续推进
