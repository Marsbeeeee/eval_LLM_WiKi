# [0617] Codex pro 20x plan usage tracker & analysis

## 总结

- 当前通过 5 小时窗口使用 token 计算，预计 gpt pro x20 可供 10 人共同使用一周
- 测试 5 个小时内，4 个人都进行了大批量消耗 token，主要花费在：

  1. 处理工具输出
  2. 推理/继续处理
  3. 读取本地文件
  4. 生成修改/应用补丁

##  背景

从 codex 5 小时 token usage 窗口 96% 开始计算，通过 token usage tracker 上 4 个人的 token 用量汇总，然后粗略估算出当前 5 小时窗口 token 的总量。

5小时窗口：97% -> 78%

4 人共使用：1 亿 token

5小时 token 总量：约等于 5 亿 token

周用量：100 % -> 92%

周 token 总量：约等于 12.5 亿



**Token 计算：**

周反推为：

```Plain Text
Pro x20 周总量 ≈ 12.5亿 token
```

如果 4 个人这次共用了 1 亿 token，并且这 1 亿代表的是**一天的使用量**，那么：

```Plain Text
4 人 / 天 = 1 亿 token
单人 / 天 = 2500 万 token
```

一周 5 天，则单人一周消耗：

```Plain Text
2500 万 × 5 = 1.25 亿 token / 人 / 周
```

所以 Pro x20 可以覆盖：

```Plain Text
12.5 亿 / 1.25 亿 = 10 人
```

结论：

如果4 个人的 1 亿 token 是一天工作量，那么 Codex Pro x20 按 5 个工作日算，大约够 **10 个同等强度的人** 使用一周。

换个角度验证：

```Plain Text
12.5 亿 token / 5 天 = 2.5 亿 token / 天
2.5 亿 / 2500 万 = 10 人 / 天
```



## 原始统计

通过使用 codex usage tracker 我们可以保存下来的关于 token 用量的 csv 文件 <cite doc-id="PE54woEu6iWHZkkSCnBctPYYnFf" file-type="wiki" title="codex-usage-insights-calls" type="doc"></cite>

原始数据主要是在统计**每一次 Codex 模型调用的用量明细**。

它包含这些信息：

1. **调用发生在什么时候**

   - `timestamp`
   - 每次模型调用的时间
2. **属于哪个会话 / 任务线程**

   - `thread`
   - 可以看出这次调用属于哪个 Codex 对话或任务
3. **调用是怎么触发的**

   - `initiated`
   - `initiated_reason`
   - 比如用户发消息后触发、Codex 自己继续处理、工具输出后继续处理
4. **在哪个项目里发生**

   - `project`
   - 例如 `Desktop`
5. **用了哪个模型和推理档位**

   - `model`
   - `effort`
   - 比如 `gpt-5.5`、`medium`
6. **token 用量**

   - `total_tokens`
   - `input_tokens`
   - `cached_input_tokens`
   - `uncached_input_tokens`
   - `output_tokens`
   - `reasoning_output_tokens`
7. **缓存情况**

   - `cache_ratio`
   - 可以看输入里有多少比例命中了缓存
8. **上下文窗口占用**

   - `context_window_percent`
   - 可以粗略判断这次调用占用了多少模型上下文窗口
9. **费用或 credits 估算**

   - `estimated_cost_usd`
   - `usage_credits`
   - `pricing_model`
   - `usage_credit_confidence`
10. **价格/统计可信度提示**

    - `recommendation`
    - 比如提示当前模型价格映射不完整，不要完全信任美元成本
11. **调用记录 ID**

    - `record_id`
    - 每条调用记录的唯一标识



## 归因单人报告

使用脚本，通过索引的方式将脚本内对于 activity 和 log 对应起来，生成的 md 报告可查看 token 用在了哪一些任务上。

Codex usage 归因单人报告 <cite doc-id="GRTewhKfLiUrALksZLqc1KC5nrh" file-type="wiki" title="Codex 用量任务归因报告" type="doc"></cite>，主要包含这些内容：

1. **报告基本信息**

- 原始 CSV 文件路径
- 匹配到的本地 Codex session 日志路径
- Session ID
- 匹配到的调用数量
- 调用时间范围

1. **用量总览**

- 总调用数
- 总 tokens
- 非缓存输入 tokens
- 输出 tokens
- 推理输出 tokens
- usage credits

1. **按 CSV Thread 汇总**

- 按 CSV 中的 thread 聚合调用量、tokens 和 credits
- 用于快速看不同主题 / 会话入口的用量分布

1. **按用途归因**

- 生成修改 / 应用补丁
- 读取本地文件 / 日志
- 搜索 / 检查本地内容
- 生成用户可见文本
- 处理工具输出、继续推理等其他用途

1. **按模型汇总**

- 按模型聚合调用数、tokens 和 usage credits
- 用于区分不同模型带来的用量占比

1. **按任务段汇总**

- 根据同一个 session 里的用户请求，把调用切成多个任务段
- 每个任务段显示：任务请求、thread、调用数、tokens、credits、模型、主要用途、相关文件
- 这是报告里最主要的任务级用量归因视图

1. **按调用原因汇总**

- Codex continuation
- Continuation after tool output
- First model call after a user message
- 以及 CSV 中出现的其他调用原因

1. **任务明细**

- 默认不生成，避免报告过长
- 如需展开每个任务段的时间范围、token 明细、用途分布、本地动作、相关文件、最终结果摘要，可运行时加 `--include-task-details`

1. **原始匹配调用**

- 默认不生成，主要用于排查和审计
- 如需查看每一条 CSV 调用的逐行明细，可运行时加 `--include-raw-calls`
- 明细包含时间戳、用途、调用原因、tokens、非缓存输入、输出、credits



## 归因多人报告

通过脚本读取多份单人报告，然后进行总的汇总

多人归因报告<cite doc-id="QmbZw4NIQifKeek9ncmcsSu6nec" file-type="wiki" title="Codex 用量多人汇总报告" type="doc"></cite>，主要包含内容：

1. **报告基本信息**

   - 汇总人员数
   - 汇总报告数
   - 覆盖时间范围
2. **团队总览**

   - 总调用数
   - 总 tokens
   - 非缓存输入 tokens
   - 输出 tokens
   - 推理输出 tokens
   - credits
3. **按日期汇总**

   - 日期
   - 人员数
   - 任务段数
   - 调用数
   - 总 tokens
   - 非缓存输入 tokens
   - credits
4. **按人员汇总**

   - 人员
   - 调用数
   - 总 tokens
   - 非缓存输入 tokens
   - 输出 tokens
   - 推理输出 tokens
   - credits
5. **按用途汇总**

   - 用途类别
   - 调用数
   - 总 tokens
   - 非缓存输入 tokens
   - 输出 tokens
   - 推理输出 tokens
   - credits
6. **按模型汇总**

   - 模型
   - 调用数
   - 总 tokens
   - 非缓存输入 tokens
   - 输出 tokens
   - 推理输出 tokens
   - credits



## Token 主要花费

| 用途 | 调用数 | 总 tokens |
|-|-|-|
| 推理 / 继续处理 | 253 | 21,617,594 |
| 处理工具输出 | 308 | 29,465,108 |
| 生成修改 / 应用补丁 | 162 | 15,933,674 |
| 读取本地文件 / 日志 | 251 | 18,567,266 |
| 生成用户可见文本 | 68 | 6,757,627 |
| 理解用户请求 | 32 | 2,369,581 |
| 搜索 / 检查本地内容 | 65 | 3,763,958 |
