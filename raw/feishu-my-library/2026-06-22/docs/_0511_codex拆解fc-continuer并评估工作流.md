# [0511]codex拆解fc-continuer并评估工作流

## 数据结构：

已读完这个文件并做了结构拆分。先给你结论：这是一个**样本级 JSONL（50条）**，每条样本核心由 `id/type/dialog/tools/meta` 组成，重点信息都在 `dialog` 里。



### **1. 样本级结构（每一行一个样本）**

典型顶层结构：

```JSON
{
  "id": "...",
  "type": "compress",
  "dialog": [ ...turns... ],
  "tools": { ...function schema... },
  "meta": { ...dataset metadata... }
}
```



模块职责：

1. `id`：样本唯一标识（含任务名、时间、叶子节点等信息）。
2. `type`：样本类型（这里看到是 `compress`）。
3. `dialog`：对话主干（最重要，按 turn 组织）。
4. `tools`：工具定义（函数名、参数 schema、required 等）。
5. `meta`：数据集/模板元信息（如 `chat_template_kwargs.tool_template_version`）。

---



### **2. dialog 对话结构（turn级）**

`dialog` 是数组，每个元素一个 turn。典型 turn：

```JSON
{
  "turn_index": 0,
  "role": "system|user|assistant",
  "content": "...",
  "loss": null|false,
  "tags": [],
  "evaluate": {},
  "analysis": null,
  "metrics": null,
  "extras": null,
  "review": {...},
  "settings": {...},
  "laep": {...}
}
```



子模块拆分：

1. `基础对话字段`：`turn_index / role / content`
2. `训练与评估字段`：`loss / evaluate / metrics / analysis`
3. `审核字段`：`review`（reviewed_at/by/status/need_review/review）
4. `配置字段`：`settings`（prompts、variables、knowledge 等）
5. `追踪字段`：`laep`（id、created_by、remark 等）
6. `扩展字段`：`tags / extras`

---



### **3. tools 结构（函数调用协议）**

`tools` 是函数字典，当前样本里典型有：

1. `search_house`
2. `rent_house`

每个函数都遵循：

```JSON
{
  "type": "function",
  "function": {
    "name": "...",
    "description": "...",
    "parameters": {
      "type": "object",
      "properties": {...},
      "required": [...],
      "additionalProperties": false
    }
  }
}
```



差异点：

1. `search_house` 参数是 `rent_range`（array）+ `unit`（string）
2. `rent_house` 参数是 `house_ranking`（string，语义上要求整数编号）

---



### **4. evaluate 结构差异（你这个文件里很关键）**

`evaluate` 不是固定单一键，而是“按评估器/模型名做动态键”：

1. `generate_gpt-4o-mini_1`（最常见）
2. `run27m4a8_fc-continuer@C0`
3. 少量其它键（如 `generate_gpt-4o-mini_4`）

每个动态键下通常含：

`content / index / metrics / meta / tags / review`  

其中 `run...` 这类会带更重的 `metrics/meta/logprobs` 细节，体积很大。



---



### **“模块化拆分”**

1. `样本容器层`：`id/type/dialog/tools/meta`
2. `对话行为层`：`dialog[*].turn_index/role/content`
3. `评测结果层`：`dialog[*].evaluate`（动态模型键）
4. `工具协议层`：`tools.*.function.parameters`
5. `审核与追踪层`：`review/settings/laep`

---



## 模型评分标准  

- 若输出中没有 `<function-call>...</function-call>`，评分为 `-1`。  
- 若有 function call，但 `<function-call>` 之前没有承接词（0词），评分为 `-1`。  
- 若 function call 存在且承接词存在，但 function 名或参数与参考答案不一致（参数问题），评分为 `0`。  
- 若 function call 与参数都正确，但承接词语义有问题，评分为 `1`。  

  - 承接词语义问题包括两类：  
  - 一是承接词过长（超过 10 个词）。  
  - 二是承接语义弱或与函数动作不匹配（缺少清晰动作意图，或动作和将调用的函数不一致）。  
- 只有同时满足“有 function call + 有承接词 + function 参数正确 + 承接语义合理”时，评分为 `2`。
