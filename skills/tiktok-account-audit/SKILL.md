---
name: tiktok-account-audit
description: Use when a user provides a TikTok profile or account link and asks for account analysis, competitor research, content performance, commerce performance, operational diagnosis, or growth opportunities.
---

# TikTok 账号诊断

对公开 TikTok 账号进行可追溯的运营诊断；不把未取得的数据当作零或事实。

## 先加载参考

在调用工具或下结论前，必须完整读取：

1. [references/workflow.md](references/workflow.md)
2. [references/report-template.md](references/report-template.md)

需要了解已确认的 KSS MCP 工具事实时，再读取 [../tiktok-shop-operator/references/mcp-tools.md](../tiktok-shop-operator/references/mcp-tools.md)。实际连接的 MCP schema 始终优先于该参考。

## 控制流程

1. 按工作流的确定性规则确认输入是公开 TikTok **个人主页/账号 URL**：仅接受 TikTok-owned host；公开短链先在浏览器中解析；最终必须为规范化 `/@handle` 路径。拒绝视频、帖子、店铺、搜索页、非 TikTok host 或仅有昵称。保留原始 URL 并记录规范化 URL 与 Handle；无法可靠提取时，只问一个问题，请用户提供主页链接。
2. 记录可选条件：市场、日期窗口、最多视频数、运营目标和对标账号。未提供时默认 50 条；显式更高或更低的视频上限均应尽力遵守，并披露实际取得样本与停止原因。对标账号按同一验证、取数和可比性规则执行。
3. 先检查已连接 MCP 是否实际提供 `creator_profile` 与 `creator_videos`，阅读其实时 schema 后优先使用这两个工具。不要猜测参数、端点或字段。
4. 工具不可用、失败、为空或关键字段不足时，按工作流使用浏览器读取可见的公开主页和视频页；遇到登录、验证码、反爬或访问限制即停止降级读取并记录原因。
5. 执行完整性门槛。若缺失会改变账号类型、关键诊断或行动优先级的信息，先自行完成允许的重试、分页和浏览器降级；仍不足时一次只问一个最关键的问题，并说明影响。用户明确要求继续时，提供部分报告。
6. 仅基于来源账本中的字段分析代表视频和账号类型；先给结果和行动，再给证据、明细、缺失信息与可信度。不要把推断写成已取得数据。

## 输出边界

- 使用报告模板的固定顺序，结果前置。
- 标明原始 URL、规范化 Handle、请求与实际采样范围、来源账本、假设、未公开/读取失败字段和 A/B/C 可信度。
- 只在有实际商品、销量或销售额字段时讨论带货表现；不要混淆播放量、销量和销售额。
- 不绕过登录、验证码、反爬或访问控制；不请求、保存或输出 API Key、Token；不发布、私信、下单、改店或投放。
