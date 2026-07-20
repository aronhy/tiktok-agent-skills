# KSS TikTok Agent Skills

A public Codex Skill for operating TikTok Shop with KSS MCP.

本仓库提供 Agent 的运营方法和工具编排规则，不提供 KSS 数据本身，也不包含任何 MCP Key。

## What It Does

`tiktok-shop-operator` 让用户通过自然语言完成：

- 商品选品与趋势筛选
- 店铺和竞品分析
- 爆款带货视频查找
- 达人搜索与合作对象筛选
- TikTok 视频字幕提取
- 商品 → 店铺 → 视频 → 达人 → 字幕 → 行动方案的完整闭环

Skill 负责“如何做”；真实数据由 KSS MCP 工具返回。

## Requirements

- 支持 Skills 的 Codex 环境
- 支持 Streamable HTTP MCP 的客户端
- 管理员单独发放的 KSS MCP Key

KSS MCP Key 与普通 API Key 是两套不同凭证，不能混用。真实查询会消耗用户自己的 KSS MCP 套餐额度。

## Install

将 Skill 文件夹复制到 Codex Skills 目录：

```bash
mkdir -p ~/.codex/skills
cp -R skills/tiktok-shop-operator ~/.codex/skills/
```

重新启动或刷新 Codex 后，可显式调用 `$tiktok-shop-operator`。不同 Codex 版本的 Skill 安装入口可能不同，请以当前客户端界面为准。

核心文件：

- [SKILL.md](skills/tiktok-shop-operator/SKILL.md)
- [MCP 工具参考](skills/tiktok-shop-operator/references/mcp-tools.md)
- [运营工作流](skills/tiktok-shop-operator/references/workflows.md)
- [输出模板](skills/tiktok-shop-operator/references/output-templates.md)
- [使用案例](skills/tiktok-shop-operator/references/examples.md)

## Configure KSS MCP

复制 [mcp.example.json](mcp.example.json) 的内容到本地 MCP 配置。

示例中的 `${KSS_MCP_KEY}` 是占位符。请只在本地配置中替换为真实 MCP Key，不要修改并提交本仓库里的示例文件。

两个服务：

| 服务 | Endpoint | 能力 |
| --- | --- | --- |
| kss-universal | `https://mcp.kolsprite.com/universal/mcp` | 商品、店铺、视频、达人查询 |
| kss-caption | `https://mcp.kolsprite.com/caption/mcp` | TikTok 视频字幕提取 |

Header 名必须是 `secret-key`。

## Use

可以直接输入自然语言：

> Use $tiktok-shop-operator to find high-growth US beauty products and build a creator outreach shortlist.

也可以使用中文：

> 使用 $tiktok-shop-operator 查找美国区评分 4.5 以上、近 7 天增长率超过 50% 的商品，并按销售额降序输出。

完整闭环示例：

> 分析一个商品的店铺、带货视频和达人，提取重点视频字幕，并生成下一步运营方案。

Agent 会自动选择 KSS MCP 工具、处理分页、说明统计口径，并把未返回字段标记为“未提供”。

## Security

- 永远不要把真实 MCP Key 写入 Skill、README 或示例配置。
- 不要提交 `mcp.local.json`、`.env`、客户数据或本地查询结果。
- 用户要求“全部”但查询被额度中断时，结果必须标记为“非完整结果”。
- MCP 未连接或返回 401 时，Agent 必须明确报告，不能用普通网络搜索冒充 KSS 数据。
- 发布前应运行仓库级密钥扫描。

## Repository Structure

```text
skills/tiktok-shop-operator/
├── SKILL.md
├── agents/openai.yaml
└── references/
    ├── mcp-tools.md
    ├── workflows.md
    ├── output-templates.md
    └── examples.md
```

## License

[MIT License](LICENSE)
