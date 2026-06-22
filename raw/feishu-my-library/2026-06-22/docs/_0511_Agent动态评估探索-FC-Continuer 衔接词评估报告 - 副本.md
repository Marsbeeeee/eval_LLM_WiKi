<title>[0511]Agent动态评估探索-FC-Continuer 衔接词评估报告 - 副本</title>

# 结论

1. **此测试集中两个 judge agent 均可胜任自动化评估**

   1. DeepSeek-v4 和 Codex-5.3 的一致率达 96%。
   2. 差异主要在两方面：(1) DeepSeek 对口头拼写输入（"S, H, U, Y..."）更严格，Codex 未判出 \`<fc>\` 格式错误；(2) 1/2 分边界存在轻微模型偏好（4%），Codex 略严格，但不影响整体结论。
2. **工作流可复用**

   1. 数据结构摸底→提取关键数据→硬性指标自动打分→软性指标使用模型能力打分→总结报告
   2. 可迁移到其他 JSONL/CSV 评估任务，核心代码已经保存 skill。

# 背景

- 数据集背景：fc-continuer 策略能不能让模型在调工具之前先说一句自然的过渡话？对比组是 baseline（老模型，直接调用 function-call），实验组是 fc-continuer（先说话再调工具）。
- Judge agent：测试 Codex-5.3 和 Claude Code 搭载 deepseek-v4-pro 两个 code-agent 能否实现 fc-continuer 自动化评估，总结可复用工作流和 skill，并比较模型能力差异。

# Codex-5.3 v.s. Claude Code 搭载 deepseek-v4-pro

## 评分结果

| agent 模型 | 对话模型 | -1 | 0 | 1 | 2 |
|-|-|-|-|-|-|
| both | run27m4a8@C0 | 75 | 0 | 0 | 0 |
| both | run27m4a8-r1@C0 | 75 | 0 | 0 | 0 |
| deepseek-v4-pro  | run27m4a8_fc-continuer@C0 | 8 | 18 | 3 | 45 |
| deepseek-v4-pro  | run27m4a8_fc-continuer_r1@C0 | 7 | 16 | 4 | 48 |
| codex-5.3 | run27m4a8_fc-continuer@C0 | 0 | 16 | 6 | 52 |
| codex-5.3 | run27m4a8_fc-continuer_r1@C0 | 0 | 15 | 10 | 50 |

**baseline 完全不出衔接词，fc-continuer 出了而且大部分（>60%）质量过关。**

fc-continuer 两个模型的有效衔接词（得 1 或 2）占比分别是 64% 和 69%，其中优质衔接词（得 2）占了绝大多数。衔接词长度集中在 7-8 词（均值 7.7，中位数 8，P25-P75 为 7-8 词），典型句式就是 "Got it, I'll search for houses within that range for you." 的模板变体。

## judge 模型差异性挖掘

<cite doc-id="TIAgwfN99iQjAvkPCiJcyCH4nBf" file-type="wiki" title="eval_fc_continuer_params_50_781_658_no_lang_20260507073427_10_compare_eval_long" type="doc"></cite>

1. 一致率：260/299 = 87%，去除定义未明确后，260/272 = 96%，一致率极高
2. [-1, 0] 类错误（共 27），均属于定义未明确，属于无效数据

   1. Codex 未发现 <fc> 这样的格式错误是不被允许的（提示词未指出）
   2. Deepseek 未能理解口头输入信号，无法将用户的 spoken words 邮箱地址与数字、字母组成的邮箱地址对应

<callout emoji="🍊">
Okay. my email is like follows. S, H, U, Y, I, dot, C, H, E, N, at, outlook, dot, com.
用户的输入，从 dot、at 来看是口语，但是 S, H, U, Y 等如果是口语，是无法判断大小写的  
因此 deepseek 判断为 0 分合情合理
</callout>

1. [1, 2] 类错误（共 12）

   1. 均为 Codex 评为 1，DeepSeek 评为 2，比例较小，判断和模型偏好有关

# Workflow

## 数据结构

50 条多轮对话，每条截取后半段跑 4 个模型变体——baseline 两个模型（`run27m4a8`、`run27m4a8-r1`）和 fc-continuer 两个模型（`fc-continuer`、`fc-continuer_r1`），共 75 个 turn。

## 评分标准

| 分数 | 含义 |
|-|-|
| -1 | 没有 function-call、没有衔接词、或 FC 格式错误（自动标，不参与人工/ judge model 判） |
| 0 | FC 有且格式对，但参数跟上下文对不上（比如用户说 CNY 它写 IDR） |
| 1 | FC 参数对，衔接词有，但太短/太生硬/跟操作不搭 |
| 2 | FC 参数对，衔接词自然、长度适中、呼应上下文 |

## Agent 流程

1. 摸数据结构 — 读 CSV 前几行，看列名和 prefix/content/words 的对应关系；跑 Python 一行搞定分布统计，发现 baseline 两个模型全 -1，fc-continuer 两个模型大部分空白待打分。                       
2. 批量提取待评分数据 — 写一个 Python 脚本把每行的 expected FC、actual FC、prefix 文本、dialogue_branch 上下文全部 dump 出来，一次性拿到所有需要判断的信息。                                    
3. 逐条打分 — 读完 dump 文件，使用 agent 的模型能力，按评分标准逐行过：FC 参数对不对？prefix 长短如何？措辞是否匹配上下文？判断完 75×2=150 个字段。                                                            
4. 写脚本落盘 — 把评分逻辑写成 Python（FC 比对用 JSON 解析、prefix 质量用词数和动词判断），跑一遍直接写回原 CSV。输出每行改动和最终分布，人工核对有没有误判。                                 
5. 转长格式、写报告。 
