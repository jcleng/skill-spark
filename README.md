# skill-spark

`spark-x2.5` 小模型的前置提示词 skill。汇总通过 `mcp__mcphub` 工具集使用钉钉通知、服务器时间与 spreadsheet 的调用方式、参数、返回值与实例，并附带工作目录、工具调用、失败重试、安全与输出等强制操作规范。

## 安装

```bash
npx skills add jcleng/skill-spark -y -g
```

- `-g`：安装到全局（用户级）技能目录。
- `-y`：跳过确认提示（适合 CI / 非交互场景）。

仅安装到当前项目（不加 `-g`）：

```bash
npx skills add jcleng/skill-spark -y
```

## 更新

按 **skill 名称** 更新（`update` 子命令不支持 `owner/repo` 形式）：

```bash
npx skills update spark-x2.5 -g -y
```

## 内容

- 钉钉机器人文本通知（`mcp__mcphub__tools_dingtalk_notify`）
- 服务器时间获取（`mcp__mcphub__tools_get_current_time`）
- spreadsheet 创建 / 读取 / 修改 / 转 HTML / 转 PDF
- PHP 脚本执行（`mcp__mcphub__tools_php_execute`）
- 结构化多步思考（`mcp__mcphub__sequential_thinking_sequentialthinking`）
- 按主题检索工具列表（`ALL_TOOLS` / `searchTools` / `describeTool` / `describeNamespace`）
- 小模型操作约束（工作目录、失败重试、禁止循环思考等）

## 规范

遵循 [Agent Skills](https://agentskills.io) 规范，根目录 `SKILL.md` 含 `name` 与 `description` 前置字段，可被 `skills` CLI（`vercel-labs/agent-skills`）自动发现。

## License

MIT
