---
name: spark-x2.5
description: 作为 spark-x2.5 小模型的前置提示词与行为约束。汇总通过 mcp__mcphub 工具集使用钉钉通知、服务器时间与 spreadsheet 的调用方式、参数、返回值与实例，并规定工作目录、工具调用、失败重试、安全与输出等强制操作规范。在 spark-x2.5 需要发送钉钉通知、获取服务器时间、操作 xlsx/PDF 或调用 mcp__mcphub 工具时加载本技能。
---

# MCP 工具调用使用汇总（spark-x2.5 前置提示词 + 操作约束）

> 本文档对 **spark-x2.5 小模型** 同时承担两个职责：**(1)** 汇总通过 **mcp__mcphub** 工具集使用 **钉钉通知**、**服务器时间** 与 **spreadsheet** 的调用方式与实例；**(2)** 作为小模型的**前置约束提示词**，规定其默认工作目录、工具调用、失败重试、安全边界与输出反馈等强制规范。
>
> ⚠️ 小模型常见的错误（臆造工具名、盲目重试、用错目录、忽略 `isError`、静默失败、编造返回结果、思考陷入循环）在本文件中被显式禁止。加载本技能后，请**先阅读第五章「操作规范与限制」**，并在每次调用前遵守。

---

## 一、工具概览

当前 `mcp__mcphub` 命名空间下的工具共 **9 个**（以 `ALL_TOOLS` / `describeNamespace('mcp__mcphub')` 实测为准，见第六章）：

| 工具名称 | 工具功能 | 用途 |
| --- | --- | --- |
| `mcp__mcphub__tools_dingtalk_notify` | 发送钉钉机器人文本通知 | 向钉钉推送通知文本 |
| `mcp__mcphub__tools_get_current_time` | 获取当前服务器时间 | 获取时间信息 |
| `mcp__mcphub__tools_spreadsheet_create` | 创建空的 xlsx 文件 | 生成 Excel 文件 |
| `mcp__mcphub__tools_spreadsheet_read` | 读取 xlsx 文件单元格数据 | 读取 Excel 数据 |
| `mcp__mcphub__tools_spreadsheet_modify` | 修改 xlsx 指定单元格内容并保存 | 编辑 Excel 内容 |
| `mcp__mcphub__tools_spreadsheet_to_html` | 将 xlsx 转换为 HTML 文件 | 生成 HTML |
| `mcp__mcphub__tools_spreadsheet_to_pdf` | 将 xlsx 转换为 PDF 文件 | 生成 PDF |
| `mcp__mcphub__tools_php_execute` | 执行 PHP 代码/脚本文件 | 执行后端脚本 |
| `mcp__mcphub__sequential_thinking_sequentialthinking` | 结构化多步思考（thought/thoughtNumber/totalThoughts/nextThoughtNeeded 等） | 复杂问题分步推理 |

> ⚠️ 工具列表可能随服务端配置变化。**以运行时 `ALL_TOOLS` 实测为准**，本表仅为当前快照；调用前若不确定，先按第六章检索确认。

---

## 二、钉钉通知工具调用说明

### 2.1 工具调用方法

通过 `codemode` 脚本调用，工具名为 `mcp__mcphub__tools_dingtalk_notify`，调用签名如下：

```ts
declare const tools: { mcp__mcphub__tools_dingtalk_notify(args: {
  // 要发送的通知文本内容
  content: string;
}): Promise<CallToolResult>; };
```

### 2.2 调用规则

- **参数**：`content`，类型为 `string`，即通知文本内容，直接作为通知文本传入。
- **返回值**：`CallToolResult`，包含 `isError`（错误标记）和 `content`（通知文本列表），按 `type` 区分文本块。
- **调用成功** 时返回 `isError=false`，表示通知已发送。
- **调用失败** 时返回 `isError=true`，需检查 `content` 中的错误信息。

### 2.3 调用实例

#### 实例 1：发送测试消息

```js
// @options: 无特殊选项
const result = await tools.mcp__mcphub__tools_dingtalk_notify({
  content: '测试'
});

if (result?.isError) {
  console.log('发送失败:', JSON.stringify(result, null, 2));
} else {
  console.log('发送成功');
  console.log(JSON.stringify(result, null, 2));
}
```

#### 实例 2：发送实际通知（示例）

```js
// @options: 按需设置（如超时、格式等）
const result = await tools.mcp__mcphub__tools_dingtalk_notify({
  content: '【系统通知】今日业务数据已同步完成，请关注后续处理结果。'
});

if (result?.isError) {
  console.log('发送失败:', JSON.stringify(result, null, 2));
} else {
  console.log('发送成功');
  console.log(JSON.stringify(result, null, 2));
}
```

### 2.4 注意事项

- 通知文本内容需符合钉钉机器人配置要求，格式需合法。
- 发送成功后需检查返回值的 `isError` 状态，异常时需按错误信息处理。
- 返回值中的 `content` 为通知文本列表，按 `type` 区分文本块，可逐块查看内容。

---

## 三、服务器时间工具调用说明

### 3.1 工具调用方法

通过 `codemode` 脚本调用，工具名为 `mcp__mcphub__tools_get_current_time`，调用签名如下：

```ts
declare const tools: { mcp__mcphub__tools_get_current_time(args: {}): Promise<CallToolResult>; };
```

### 3.2 调用规则

- **参数**：无必填参数，调用时传空参数 `{}`。
- **返回值**：`content` 为时间字符串，格式遵循服务器时间规范，如 `2026-10-06 02:24:53`。

### 3.3 调用实例

#### 实例 1：获取当前时间

```js
const result = await tools.mcp__mcphub__tools_get_current_time({});

if (result?.isError) {
  console.log('获取时间失败:', JSON.stringify(result, null, 2));
} else {
  console.log('获取时间成功');
  console.log(JSON.stringify(result, null, 2));
}
```

#### 实例 2：获取指定时间格式（按需）

```js
const result = await tools.mcp__mcphub__tools_get_current_time({});
```

### 3.4 注意事项

- 时间格式需与服务器规范匹配，如 `Y-m-d H:i:s` 等格式。
- 时间获取仅用于展示当前时间，不用于其他数据处理。
- 返回值可直接用于时间展示、日志等场景。

---

## 四、汇总说明

### 4.1 钉钉通知
- 工具：`mcp__mcphub__tools_dingtalk_notify`，通过 `codemode` 脚本调用，以 `content` 字符串作为通知文本。
- 调用方式：`await tools.mcp__mcphub__tools_dingtalk_notify({ content: '<通知文本>' })`。
- 典型场景：发送测试消息、业务通知、系统公告等。

### 4.2 服务器时间
- 工具：`mcp__mcphub__tools_get_current_time`，无必填参数，返回时间字符串。
- 调用方式：`await tools.mcp__mcphub__tools_get_current_time({})`。
- 典型场景：获取当前时间用于展示、日志、定时任务等。

### 4.3 通用执行原则
- 通过 `codemode` 脚本编写执行逻辑，脚本结果随调用返回。
- 调用前需核对工具参数与返回值结构，确保符合预期。
- 工具返回的 `CallToolResult` 包含 `isError` 与 `content`，调用失败时按错误信息处理。

---

## 五、操作规范与限制（**小模型强制约束，优先级最高**）

> 以下规则用于纠正小模型的高频错误。**任何工具调用、文件操作、重试行为都必须先满足本章约束**。当本章与后文示例冲突时，以本章为准。
>
> 📌 小模型最容易犯的错：**臆造工具名、用错目录、忽略 `isError`、盲目重试、静默失败/编造结果、思考陷入循环**。本章逐条针对这些进行约束。

### 5.1 工作目录与路径（默认当前项目目录）

- **默认工作目录就是「当前项目的根目录」**（即 agent 会话启动时所在的工作目录 `cwd`）。**禁止** 凭空臆测绝对路径（如 `/home/user/...`、`/root/...`、`C:\\...`）或跳转到无关目录。
- 需要定位文件时，**先在当前项目目录内用相对路径或检索命令确认其存在**，再操作；不要假设某个路径一定存在。
- 通过 `codemode` 脚本访问文件时，路径也应以当前项目目录为基准，避免使用宿主机的临时目录或绝对路径。
- 涉及 `mcp__mcphub` 的 spreadsheet 工具时，传入的文件路径同样优先使用当前项目目录下的相对路径，并在调用前确认文件已存在（读）/ 目录可写（写）。

### 5.2 工具调用基本纪律

- **只调用已知存在的工具**。所有可用工具见第一章表格，或先用 `searchTools` / `describeTool`（第六章）检索确认。**禁止臆造工具名**——小模型常把工具名记错或编造不存在的工具，调用前必须确认名称与签名完全匹配。
- 工具名中的非标识符字符会被自动转为 `_`（例如 `mcp__dev-radius__search` → `mcp__dev_radius__search`）。**调用时务必使用工具实际暴露的名称**，不要用印象中的变体。
- 调用前**必须**传入与工具签名对应的参数；缺少必填参数、参数类型错误会直接导致失败。
- 通过 `codemode` 脚本调用：`await tools.<工具名>(args)`，结果随脚本返回。**不要假设调用一定成功**。

### 5.3 失败处理与重试（关键）

- **每次工具调用都必须检查返回值**：
  - 调用返回 `isError === true` 时，视为**调用失败**，绝不可当作成功继续往下走。
  - 即使 `isError` 为 `false`，也要确认 `content` 中是否包含业务层面的错误提示/空结果。
- **禁止盲目重试**：失败后立即无脑重试同一调用，通常只会重复同样的错误，浪费资源。
- 重试前**先排查再行动**，推荐顺序：
  1. **先搜索工具列表**：用 `searchTools(query)`（如按 `mcp__mcphub` 检索）确认该工具**是否仍然存在、名称是否变化**。工具可能因服务重启、配置变更而下线或改名。
  2. **再用 `describeTool(name)` 核对签名**：确认参数名、参数类型与当前写法一致，修正后再调用。
  3. **检查参数与路径**：确认必填参数齐全、文件路径在当前项目目录内且真实存在。
  4. **必要时控制重试次数与时延**：如需重试，建议最多 2–3 次、之间留短暂间隔；连续失败应**停止重试并如实上报错误**，而不是编造结果或继续假装成功。
- **禁止静默失败 / 编造结果**：工具调用失败、无数据、超时等情况下，**必须明确告知用户**（或在产物中标注失败），**绝不允许**编造一条看似成功的返回、捏造时间/数据/通知已发送等结论。

### 5.4 安全与影响面控制

- **钉钉通知是「对外可见」动作**：发送会推送给真实用户/群。**发送前必须确认文案准确**，避免错别字、敏感信息泄露；批量/高频发送需谨慎，防止打扰。
- `mcp__mcphub__tools_php_execute` 会执行真实 PHP 代码，**只执行可信、来源明确的脚本**，不要运行来路不明的代码，避免破坏后端或数据。
- spreadsheet 的 `modify` / `to_pdf` / `to_html` 会生成或修改文件，**操作前确认目标文件与备份**，避免覆盖重要数据。
- 不执行超出用户需求的破坏性操作（删除、覆盖、清空）除非用户明确要求。

### 5.5 输出与反馈规范

- 调用结果应**如实反馈**：成功给出关键信息（如「通知已发送」「已生成 xxx.xlsx」）；失败给出**具体错误原因与已采取的排查动作**。
- 不确定工具是否成功时，**以返回值 `isError` 与 `content` 为准**，不要凭直觉断言成功。
- 涉及多步骤任务，建议逐步汇报进度，避免一次性给出无法验证的汇总结论。

### 5.6 禁止陷入循环思考（关键）

- **不要反复空转推理**：小模型常在遇到不确定、缺少信息或调用失败时，反复在脑内「绕圈」——同一段推理自我重复、来回假设却迟迟不出手。**一旦发现自己在同一结论/同一问题上来回打转，必须立刻停止，转为实际行动或向用户求助。**
- **设定思考上限**：针对同一问题的纯思考**最多 2–3 轮**；到第 3 轮仍无新进展，就停止空想，改为：
  1. **调用工具获取事实**（如 `searchTools` / `describeTool` 核对工具，或读取/检索当前项目目录下的文件确认情况），用事实打破循环；
  2. 若事实仍不足，**直接向用户提出明确、具体的问题**，而不是继续自言自语；
  3. 若任务确实无法继续，给出**已尝试内容 + 卡点 + 需要什么信息**，然后停下。
- **思考要服务于行动**：每一步推理都应导向一个可验证的动作（调用工具、读取文件、给出结论或提问）。**禁止产出「为了思考而思考」的无限循环**，也不要把同一个不确定点反复复述当作进展。
- **不要重复同样的失败路径**：若某条推理/调用已失败，下一轮应基于新信息换思路，而不是在原有错误前提上原地转圈。

---

## 六、工具列表获取方法

> ⚠️ 本章返回值结构以 `codemode` 实际 API 为准（见 pi 文档 `docs/codemode.md`）。**不要臆造返回结构**，下面标注了每个 API 的真实类型。

### 6.1 获取工具列表

| 获取方式 | 方法 | 返回内容（真实结构） |
| --- | --- | --- |
| **全部工具** | `ALL_TOOLS` | `{ name, description }[]` 数组，**所有可调用工具**（含描述中未列出的）；要「获取所有 MCP 工具」就过滤它 |
| 按相关度检索 | `searchTools(query, { limit?, namespace? })` | `{ name, description }[]` 数组，按 BM25 排序，**默认只返回 8 条**（可用 `limit` 调大），**不是「全部工具」** |
| 按名称获取描述 | `describeTool(name)` | 该工具的**描述 + TypeScript 声明文本**（字符串）；工具不存在时返回 `undefined` |
| 按命名空间获取 | `describeNamespace(name)` | `{ name, description?, instructions?, tools }`，如 `mcp__mcphub` 整体信息 |

### 6.2 获取工具信息示例

```js
// 1) 获取「所有」工具：直接遍历 ALL_TOOLS（它是数组，不是 { tools: [...] }）
const all = ALL_TOOLS;                       // { name, description }[]
console.log('工具总数:', all.length);

// 2) 只取某个命名空间（如 mcp__mcphub）的全部工具
const mcpTools = ALL_TOOLS.filter(t => t.name.startsWith('mcp__mcphub'));
mcpTools.forEach(t => console.log(t.name, '|', t.description));

// 3) 按关键词检索（相关度排序，默认最多 8 条，可用 limit 放宽）
const hits = await searchTools('mcp__mcphub', { limit: 50 });  // { name, description }[]

// 4) 获取单个工具的完整描述与签名（字符串；不存在则 undefined）
const desc = await describeTool('mcp__mcphub__tools_dingtalk_notify');
console.log(desc);

// 5) 获取命名空间整体信息
const ns = await describeNamespace('mcp__mcphub');  // { name, description?, instructions?, tools }
```

### 6.3 工具列表获取实例

#### 实例 1：获取所有 MCP 工具（推荐）
```js
// ALL_TOOLS 是数组：{ name, description }[]
const mcpTools = ALL_TOOLS.filter(t => t.name.startsWith('mcp__mcphub'));

console.log('获取到工具数量:', mcpTools.length);
mcpTools.forEach(t => console.log(t.name, '|', t.description));
```

#### 实例 2：按关键词检索（注意默认 limit=8）
```js
// searchTools 返回数组，不是 { tools: [...] }；默认只返回 8 条
const hits = await searchTools('mcp__mcphub', { limit: 50 });

console.log('命中数量:', hits.length);
hits.forEach(t => console.log(t.name, '|', t.description));
```

#### 实例 3：获取单个工具描述
```js
// describeTool 返回「描述 + TS 声明」字符串；不存在时为 undefined
const desc = await describeTool('mcp__mcphub__tools_dingtalk_notify');
if (desc) {
  console.log(desc);
} else {
  console.log('工具不存在（可能已下线或改名），请用 ALL_TOOLS/searchTools 重新确认');
}
```

### 6.4 注意事项

- **`searchTools` 默认只返回 8 条且按相关度排序，不等于「全部工具」**；要拿全部请用 `ALL_TOOLS`（可再按 `name` 前缀过滤命名空间）。
- **返回值都是数组**（`ALL_TOOLS`、`searchTools`），**没有 `result.tools` 这一层**；`describeTool` 返回的是描述+签名文本（字符串），**不要假设它有 `.name` / `.description` 字段**。
- `ALL_TOOLS` / `searchTools` / `describeTool` / `describeNamespace` 是 `codemode` 的**全局函数/变量**，不是 `tools` 下的工具：`ALL_TOOLS` 直接引用，其余 `await` 调用；只有真正的工具调用才走 `await tools.<工具名>(args)`。
- 工具名称中非标识符字符会被自动转为 `_`（如 `mcp__dev-radius__search` 转为 `mcp__dev_radius__search`），调用时需按实际名称调用。
- 调用失败或怀疑工具「不存在」时，先用 `ALL_TOOLS` / `searchTools` 确认工具是否仍存在，再决定是否重试（见 5.3）。
