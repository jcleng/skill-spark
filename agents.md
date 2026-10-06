# 项目说明：skill-spark-x2.5

> 本文件汇总 `skill-spark-x2.5` 项目的内容、结构与使用方式，供后续维护与复用参考。

---

## 一、项目概述

- **仓库**：`jcleng/skill-spark-x2.5`（GitHub 公开仓库）
- **用途**：作为 `spark-x2.5` 小模型的前置提示词（prompt）skill。
- **规范**：遵循 [Agent Skills](https://agentskills.io) 规范，可被 `skills` CLI（`vercel-labs/agent-skills`）自动发现与安装。
- **核心内容**：汇总通过 `mcp__mcphub` 工具集使用钉钉通知、服务器时间与 spreadsheet 相关工具的调用方式、参数、返回值与实例。

---

## 二、目录结构

```
skill-spark-x2.5/
├── SKILL.md      # 技能定义文件（含 YAML 前置字段 name/description），核心内容
├── README.md     # 安装与使用说明
├── LICENSE       # MIT 许可证
└── agents.md     # 本文件，项目汇总说明
```

---

## 三、SKILL.md 关键字段

```yaml
---
name: spark-x2.5
description: 作为 spark-x2.5 小模型的前置提示词。汇总当前通过 mcp__mcphub 工具集使用钉钉通知、服务器时间与 spreadsheet 的调用方式、参数、返回值与实例，便于复用。在 spark-x2.5 需要发送钉钉通知、获取服务器时间或操作 xlsx/PDF 时加载本技能。
---
```

- `name` 仅含小写字母、数字、`.` 与 `-`，符合 CLI 的 `sanitizeName` 规则。
- `description` 说明技能功能与触发场景，用于 agent 自动匹配加载。

---

## 四、技能涵盖的工具（来自 mcp__mcphub）

| 工具名称 | 功能 |
| --- | --- |
| `mcp__mcphub__tools_dingtalk_notify` | 发送钉钉机器人文本通知 |
| `mcp__mcphub__tools_get_current_time` | 获取当前服务器时间 |
| `mcp__mcphub__tools_spreadsheet_create` | 创建空 xlsx 文件 |
| `mcp__mcphub__tools_spreadsheet_read` | 读取 xlsx 单元格数据 |
| `mcp__mcphub__tools_spreadsheet_modify` | 修改 xlsx 指定单元格并保存 |
| `mcp__mcphub__tools_spreadsheet_to_html` | 将 xlsx 转换为 HTML |
| `mcp__mcphub__tools_spreadsheet_to_pdf` | 将 xlsx 转换为 PDF |
| `mcp__mcphub__tools_php_execute` | 执行 PHP 代码/脚本文件 |

---

## 五、安装方式

```bash
# 全局安装（用户级，推荐搭配 -y 非交互）
npx skills add jcleng/skill-spark-x2.5 -y -g

# 仅安装到当前项目（不加 -g）
npx skills add jcleng/skill-spark-x2.5 -y
```

- `-g`：安装到全局技能目录（如 `~/.agents/skills/`、Pi 为 `~/.pi/agent/skills/`）。
- `-y`：跳过确认提示，适合 CI / 非交互场景。

---

## 六、维护说明

- 修改 `SKILL.md` 内容后，提交并 `git push` 到 `main` 分支即可更新。
- 已安装用户可通过 `npx skills update jcleng/skill-spark-x2.5 -g -y` 获取最新版本。
- 发布前请确认 `SKILL.md` 的 `name` 与 `description` 字段完整且为合法 YAML。
