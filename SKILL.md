---
name: spark-x2.5
description: 作为 spark-x2.5 小模型的前置提示词。汇总当前通过 mcp__mcphub 工具集使用钉钉通知、服务器时间与 spreadsheet 的调用方式、参数、返回值与实例，便于复用。在 spark-x2.5 需要发送钉钉通知、获取服务器时间或操作 xlsx/PDF 时加载本技能。
---

# MCP 工具调用使用汇总（spark-x2.5 前置提示词）

> 本文档汇总当前通过 **mcp__mcphub** 工具集使用 **钉钉通知** 与 **服务器时间** 的调用方式，包含工具说明、详细调用指令与实例，便于后续复用。

---

## 一、工具概览

当前使用的 mcp__mcphub 工具共 8 个，其中与本次需求相关的工具如下：

| 工具名称 | 工具功能 | 用途 |
| --- | --- | --- |
| `mcp__mcphub__tools_dingtalk_notify` | 发送钉钉机器人文本通知 | 向钉钉推送通知文本 |
| `mcp__mcphub__tools_spreadsheet_create` | 创建空的 xlsx 文件 | 生成 Excel 文件 |
| `mcp__mcphub__tools_php_execute` | 执行 PHP 代码/脚本文件 | 执行后端脚本 |
| `mcp__mcphub__tools_spreadsheet_read` | 读取 xlsx 文件单元格数据 | 读取 Excel 数据 |
| `mcp__mcphub__tools_get_current_time` | 获取当前服务器时间 | 获取时间信息 |
| `mcp__mcphub__tools_spreadsheet_to_html` | 将 xlsx 转换为 HTML 文件 | 生成 HTML |
| `mcp__mcphub__tools_spreadsheet_modify` | 修改 xlsx 指定单元格内容并保存 | 编辑 Excel 内容 |
| `mcp__mcphub__tools_spreadsheet_to_pdf` | 将 xlsx 转换为 PDF 文件 | 生成 PDF |

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

## 五、工具列表获取方法

### 5.1 获取工具列表

通过 `codemode` 脚本调用工具，可按主题检索工具列表，具体获取方法如下：

| 获取方式 | 方法 | 返回内容 |
| --- | --- | --- |
| 按主题检索列表 | `searchTools(query)` | 该主题下的全部工具，格式 `{ name, description }[]` |
| 按名称获取描述 | `describeTool(name)` | 工具的详细描述与 TypeScript 声明 |
| 全局工具列表 | `ALL_TOOLS` | 所有可调用工具（含描述中未列出的） |

### 5.2 获取工具信息示例

```js
// 按主题检索工具列表
const result = await searchTools('mcp__mcphub');
// 结果: [{ name, description }, ...]

// 获取单个工具描述
const tool = await describeTool('mcp__mcphub__tools_dingtalk_notify');
// 结果: { name, description, ... }

// 获取命名空间整体信息
const ns = await describeNamespace('mcp__mcphub');
```

### 5.3 工具列表获取实例

#### 实例 1：按主题获取工具列表
```js
const result = await searchTools('mcp__mcphub');

if (result.tools) {
  console.log('获取到工具数量:', result.tools.length);
  result.tools.forEach(t => console.log(t.name, '|', t.description));
}
```

#### 实例 2：获取单个工具描述
```js
const tool = await describeTool('mcp__mcphub__tools_dingtalk_notify');
if (tool) {
  console.log(tool.name, '|', tool.description);
}
```

### 5.4 注意事项

- 获取工具列表时结合需求，按主题检索即可，无需一次性全部获取。
- 获取单个工具描述可用于校验工具功能与调用签名，便于后续调用。
- 工具名称中非标识符字符会被自动转为 `_`（如 `mcp__dev-radius__search` 转为 `mcp__dev_radius__search`），调用时需按实际名称调用。
- 工具调用通过 `codemode` 脚本的 `tools.<工具名>(args)` 方式，参数需与工具签名对应。
