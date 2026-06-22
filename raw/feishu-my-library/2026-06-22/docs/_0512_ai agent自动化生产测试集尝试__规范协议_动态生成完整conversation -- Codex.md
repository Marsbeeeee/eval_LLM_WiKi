# [规范协议]动态生成完整conversation -- Codex

**# codex动态生成完整conversation规范协议（完善版）**



**## 1. 适用范围**

- 适用于 `fc-continuer` 模板下的单链路对话样本构造、改写、压缩和质检。
- 适用于文件形态：单条 JSON（调试）与 JSONL（批量评测）。

**## 2. 文件与编码规范**

\- 文件编码必须为 \`UTF-8\` **无 BOM**。

- 禁止出现 BOM 头；否则可能触发 `Unexpected UTF-8 BOM`。
- 换行建议使用 `\n`，避免混合换行符导致平台解析差异。

**## 3. 顶层 Schema（必须完整）**

顶层必须包含且仅建议包含以下核心字段：

- `id`: string
- `type`: string，固定为 `compress`
- `dialog`: array
- `tools`: object（可为空 `{}`）
- `meta`: object

`meta` 子结构要求：

- `chat_lang`: string（可空字符串，或按任务填如 `es_mx`）
- `description`: string（可空）
- `chat_template_kwargs`: object
- `chat_template_kwargs.tool_template_version`: string，固定为 `fc-continuer`

**## 4. dialog 节点 Schema（每一轮必须完整）**

每个 `dialog[i]` 必须具备以下字段：

- `turn_index`: integer
- `role`: string（`user` 或 `assistant`）
- `content`: string
- `loss`: null
- `tags`: array
- `evaluate`: object
- `analysis`: null
- `metrics`: null
- `extras`: null
- `review`: object
- `settings`: object
- `laep`: object

`review` 必须含字段：

- `reviewed_at`: null
- `reviewed_by`: null
- `need_review`: null
- `review`: null
- `status`: null

`settings` 必须含字段：

- `prompts`: null
- `main_prompt_name`: null
- `knowledge_template`: null
- `prompt_name`: null
- `variables`: null
- `knowledge`: null
- `segments`: null

`laep` 必须含字段：

- `id`: string（如 `node-0009`）
- `remark`: null
- `created_by`: null
- `rag_rewrite_search_result`: null

**## 5. 对话顺序与角色约束（强校验）**

- `turn_index` 必须从 `0` 开始，连续递增，不能跳号、不能重复。
- `dialog[0].role` 必须是 `user`。
- 角色必须严格轮换：
- 偶数 `turn_index` -> `user`
- 奇数 `turn_index` -> `assistant`

**### 5.1 含 System Prompt 的兼容规则（推荐）**

- 当样本显式需要 `system prompt` 时，允许 `dialog[0].role = system`。
- 此时从 `turn_index = 1` 开始执行严格轮换：
- `1=user, 2=assistant, 3=user, 4=assistant ...`
- 除首轮角色外，其余结构与字段约束不变。

**## 6. content 内容规范**

- 单样本内仅允许一种业务语言（本样例为西语）。
- 清除乱码、控制字符、不可见污染字符。
- 不修改结构字段时，仅允许改写 `content`（以及必要的 `turn_index/role`）。
- 不得把 object/array/null 类型字段改成 string。

**## 7. 动态生成流程（执行标准）**

1. 选取合规模板样本作为骨架（保留完整字段结构）。
2. 根据业务脚本生成新对话文本，填充到 `dialog[*].content`。
3. 按轮次自动重排 `turn_index` 并校验角色轮换。
4. 保持非业务字段类型不变（`null/{}/[]` 不漂移）。
5. 写出 JSON 后做 UTF-8 无 BOM 校验。
6. 进入上传前质检（见第 8 节）。

**## 8. 上传前质检清单（Checklist）**

- 顶层字段是否齐全：`id/type/dialog/tools/meta`
- `type` 是否为 `compress`
- `meta.chat_template_kwargs.tool_template_version` 是否为 `fc-continuer`
- `dialog` 每轮字段是否完整
- `turn_index` 是否 0 开始且连续
- `role` 是否严格 `user/assistant` 轮换
- `dialog[0].role` 是否为 `user`
- `content` 是否统一语言、无乱码
- 空字段类型是否仍为原类型（`null/{}/[]`）
- 文件是否为 UTF-8 无 BOM

**## 9. 常见错误与修复**

- 错误：`index=0` 是 `assistant`
- 修复：把首轮改成 `user`，并整体重排后续角色。
- 错误：`turn_index` 缺失或跳号
- 修复：按数组顺序重写为 `0..n-1`。
- 错误：把 `evaluate` 从 `{}` 写成 `null`
- 修复：恢复为对象 `{}`（保持模板类型）。
- 错误：出现中文或混合语言
- 修复：统一改为目标语种，仅保留一种业务语言。
- 错误：UTF-8 with BOM
- 修复：重新以 UTF-8 无 BOM 导出。

**## 10. 最小合规示例（节选）**

```JSON
{
  "id": "single_chain_dialogue_xxx",
  "type": "compress",
  "dialog": [
    {
      "turn_index": 0,
      "role": "user",
      "content": "[inicio de conversacion]",
      "loss": null,
      "tags": [],
      "evaluate": {},
      "analysis": null,
      "metrics": null,
      "extras": null,
      "review": {
        "reviewed_at": null,
        "reviewed_by": null,
        "need_review": null,
        "review": null,
        "status": null
      },
      "settings": {
        "prompts": null,
        "main_prompt_name": null,
        "knowledge_template": null,
        "prompt_name": null,
        "variables": null,
        "knowledge": null,
        "segments": null
      },
      "laep": {
        "id": "node-0009",
        "remark": null,
        "created_by": null,
        "rag_rewrite_search_result": null
      }
    }
  ],
  "tools": {},
  "meta": {
    "chat_lang": "",
    "description": "",
    "chat_template_kwargs": {
      "tool_template_version": "fc-continuer"
    }
  }
}
```



**## 11. 基于当前样本的结论**

- 你提供的 `single_chain_dialogue_eval_like.json` 结构整体合规。
- 可增强项：
- 在生产场景建议将 `meta.chat_lang` 明确为 `es_mx`（若平台允许）。
- 如需批量评测，转换为 JSONL 时必须保证“一行一条完整 JSON”。

**## 12. System Prompt 规范补充（增量）**

- 本节为对现有协议的补充，不替代原有条款。
- 当任务明确要求包含 `system prompt` 时，必须使用 `dialog[0]` 承载：

  - `dialog[0].role = "system"`
  - `dialog[0].turn_index = 0`
  - `dialog[0].content` 为完整系统指令文本
- 在存在 `system` 首轮时，对话轮次从 `turn_index = 1` 开始继续，且严格按 `user -> assistant` 交替。
- 除首轮 `system` 外，其余 `dialog[i]` 仍必须完整保留既有 Schema 字段（`loss/tags/evaluate/analysis/metrics/extras/review/settings/laep` 等）。

**### 12.1 system.content 文本规范**

- 必须原样保留业务规则语义，不得擅自删减关键约束（如语言限制、静默处理、转人工、结案话术、步骤流转）。
- 允许修正明显乱码或编码导致的异常字符，但不得改变规则含义。
- 占位符必须保持原样：如 `{{{borrower_name}}}`、`{{{payment_amount}}}`、`{{{extented_3_days_date}}}`。
- 禁止把占位符改成普通文本；禁止新增未定义占位符。
- 若业务要求“按原文写入”，应整体覆盖 `dialog[0].content`，而不是拆散到多轮对话。

**### 12.2 与现有校验项的合并口径**

- 第五节“首轮必须是 user”为默认规则；若启用 system 模式，以第五节子条款（system 兼容规则）为准。
- 上传前检查需新增：

  - 是否启用了 `system` 模式（是/否）
  - 若是：`dialog[0].role` 是否为 `system`
  - 若是：`dialog[1].role` 是否为 `user`
  - `system.content` 是否完整且占位符未损坏

**### 12.3 编码与落盘要求（避免上传失败）**

\- JSON/JSONL 文件必须为 \`UTF-8\` **无 BOM**。

- 若出现 `Unexpected UTF-8 BOM`，需无损重写为 UTF-8 无 BOM 后再上传。
- 建议保存后做一次首字节检查：文件起始应为 `{`（十六进制 `7B`），不应为 `EF BB BF`。
