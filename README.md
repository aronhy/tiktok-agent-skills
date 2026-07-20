# TikTok Agent Skills

> 通过 Codex + KSS MCP，用自然语言完成 TikTok Shop 的选品、店铺分析、爆款视频发现、达人匹配、字幕提取和行动方案生成。

这个项目把 TikTok Shop 运营中反复进行的数据查询和分析流程，整理成一个可以被 Agent 直接执行的 Skill。你不需要记住 MCP 工具名和参数，只需要描述业务目标，Agent 就会选择工具、组织查询、整理结果并给出下一步建议。

## 完整运营闭环

```text
选品 → 找店铺 → 找爆款视频 → 找达人 → 提取字幕 → 生成行动方案
```

例如，当你提供一个商品或选品方向后，Agent 可以：

1. 筛选符合地区、价格、评分、销量和增长率要求的商品。
2. 补充店铺规模、评分、销售表现和服务指标。
3. 查找关联的带货视频，并按销量或销售额排序。
4. 从视频中识别达人，进一步分析粉丝、播放、互动和带货能力。
5. 提取重点视频字幕，拆解开头钩子、卖点、证明方式和 CTA。
6. 汇总数据证据，形成选品、内容和达人合作行动方案。

## 这是什么

仓库目前包含一个核心 Skill：

### `tiktok-shop-operator`

面向 TikTok Shop 卖家、运营人员和内容团队的运营 Skill，覆盖：

- 商品趋势与选品研究
- 店铺和竞品分析
- 爆款带货视频发现
- 达人筛选与匹配
- TikTok 视频字幕提取
- 商品、店铺、视频和达人的关联分析
- 基于数据证据的行动方案生成

它不会自行购买套餐、联系达人或发布内容。它负责研究、分析、生成建议和执行方案，真实的外部动作仍需要用户单独确认。

## MCP 与 Skill 如何配合

| 组成 | 负责什么 |
| --- | --- |
| **KSS MCP** | 提供商品、店铺、视频、达人和字幕的真实数据查询工具 |
| **tiktok-shop-operator Skill** | 理解业务目标、组合 MCP 工具、处理分页与排序、检查数据口径并生成行动方案 |
| **Codex** | 接收自然语言任务，读取 Skill，调用 MCP 并整理最终结果 |

简单来说：

> MCP 给 Agent 数据和工具，Skill 给 Agent TikTok Shop 的运营方法。

没有 MCP，Skill 无法获得真实的 TikTok 数据；没有 Skill，Agent 仍然可以调用 MCP，但复杂任务的流程、排序、核验和输出不够稳定。

## 核心能力

### 商品选品

按地区、类目、关键词、价格、评分、销量、销售额、增长率和上架时间筛选商品。

### 店铺分析

查询店铺评分、商品数、平均价格、近 30 天销量和销售额、达人数量、视频数量以及服务指标。

### 爆款视频

按商品、店铺、关键词、发布时间、播放量、互动率、销量和销售额查找带货视频。

### 达人匹配

按地区、粉丝数、平均播放、互动率、增长率、带货类目、销量和 GPM 筛选达人，并通过 ID 交叉确认关联关系。

### 字幕与内容拆解

提取指定 TikTok 视频字幕，分析开头钩子、核心卖点、证明与演示、异议处理和 CTA。

### 数据完整性

自动处理分页、去重、统计周期、币种和缺失字段。查询被额度或限流中断时，明确标记为“非完整结果”，不会把第一页称为全部结果。

## 可以直接执行的任务

安装并连接 KSS MCP 后，可以直接向 Codex 提问：

> 使用 $tiktok-shop-operator 查找美国区评分 4.5 以上、价格 30–50 美元、近 7 天销量增长较快的美妆商品，并按销售额降序输出。

> 使用 $tiktok-shop-operator 查找美国区评分 4.5 以上、近 30 天销量较高的美妆店铺，并给出店铺地址。

> 使用 $tiktok-shop-operator 分析 PolyLemon 自动磁力搅拌杯的带货视频和达人，按销售额排序，并筛选值得合作的达人。

> 使用 $tiktok-shop-operator 提取这个 TikTok 视频的字幕，分析开头钩子、卖点、演示方式和 CTA。

> 使用 $tiktok-shop-operator 从选品开始，完成店铺、爆款视频、达人和字幕分析，最后生成一份 TikTok Shop 运营行动方案。

Agent 会在结果中说明查询地区、统计周期、筛选条件、排序方式、分页范围、结果数量和完整性。

## 快速开始

### 1. 安装 Skill

克隆仓库：

```bash
git clone https://github.com/aronhy/tiktok-agent-skills.git
cd tiktok-agent-skills
```

复制 Skill 到 Codex Skills 目录：

```bash
mkdir -p ~/.codex/skills
cp -R skills/tiktok-shop-operator ~/.codex/skills/
```

重新启动或刷新 Codex，使 Skill 被发现。

### 2. 配置 KSS MCP

复制 [mcp.example.json](mcp.example.json) 中的配置到本地 MCP 配置文件。

示例中的 `${KSS_MCP_KEY}` 是占位符。请只在本地副本中替换为管理员发放的真实 MCP Key，不要修改并提交仓库中的示例文件。

| MCP 服务 | Endpoint | 能力 |
| --- | --- | --- |
| `kss-universal` | `https://mcp.kolsprite.com/universal/mcp` | 商品、店铺、视频和达人查询 |
| `kss-caption` | `https://mcp.kolsprite.com/caption/mcp` | TikTok 视频字幕提取 |

认证 Header 名必须是 `secret-key`。

注意：KSS MCP Key 与普通 API Key 是不同凭证，不能混用。

### 3. 开始使用

显式调用 Skill：

```text
使用 $tiktok-shop-operator 分析美国 TikTok Shop 最近增长较快的美妆商品。
```

也可以直接描述完整业务目标，Agent 会自动判断需要调用哪些工具和工作流。

## 项目文件

| 文件 | 用途 |
| --- | --- |
| [SKILL.md](skills/tiktok-shop-operator/SKILL.md) | Agent 的核心触发条件、执行规则和安全边界 |
| [MCP 工具参考](skills/tiktok-shop-operator/references/mcp-tools.md) | KSS MCP 工具、参数、返回字段、限流和关联规则 |
| [运营工作流](skills/tiktok-shop-operator/references/workflows.md) | 选品、店铺、视频、达人、字幕和完整运营闭环 |
| [输出模板](skills/tiktok-shop-operator/references/output-templates.md) | 商品、店铺、视频、达人、字幕和行动建议格式 |
| [使用案例](skills/tiktok-shop-operator/references/examples.md) | 十个自然语言任务与预期工具计划 |
| [MCP 示例配置](mcp.example.json) | 不包含真实 Key 的 KSS MCP 配置 |
| [LICENSE](LICENSE) | MIT 开源许可证 |

## 安全与额度

- 不要把真实 MCP Key 写入 README、Skill、示例或 Git 提交。
- 不要提交 `mcp.local.json`、`.env`、客户数据或本地查询结果。
- 真实查询会消耗用户自己的 KSS MCP 套餐额度。
- 收到 `ERROR_API_MINUTE_MAX` 或 `ERROR_API_MONTH_MAX` 时，Agent 会停止继续调用并说明已完成和未完成部分。
- MCP 没有返回的字段统一标记为“未提供”，不推测或补造。
- MCP 未连接或返回 401 时，Agent 会明确报告，不使用普通网络搜索冒充 KSS 数据。
- Skill 只提供研究、草稿和行动建议，不自动联系达人、购买额度或发布 TikTok 内容。

## License

本项目使用 [MIT License](LICENSE)。
