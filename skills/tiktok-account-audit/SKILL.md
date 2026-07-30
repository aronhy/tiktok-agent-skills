---
name: tiktok-account-audit
description: Use when a user provides a TikTok profile or account link and asks for account analysis, competitor research, content or commerce performance, operational-logic research, account-launch process reverse engineering, selling guidance, operational diagnosis, or growth opportunities.
---

# TikTok 账号诊断

对公开 TikTok 账号进行可追溯的运营诊断；不把未取得的数据当作零或事实。

## 先加载参考

在调用工具或下结论前，必须完整读取：

1. [references/workflow.md](references/workflow.md)
2. [references/report-template.md](references/report-template.md)

需要了解已确认的 KSS MCP 工具事实时，再读取 [../tiktok-shop-operator/references/mcp-tools.md](../tiktok-shop-operator/references/mcp-tools.md)。实际连接的 MCP schema 始终优先于该参考。

## 控制流程

1. 按工作流的确定性规则确认输入是公开 TikTok **个人主页/账号 URL**：仅接受 TikTok-owned host；公开短链先在浏览器中解析；最终必须为规范化 `/@handle` 路径。拒绝视频、帖子、店铺、搜索页、非 TikTok host 或仅有昵称。保留原始 URL 并记录规范化 URL 与 Handle；链接文字、昵称或相邻说明与 URL Handle 冲突时，以规范化 URL Handle 为目标并披露冲突，绝不合并两个身份。无法可靠提取时，只问一个问题，请用户提供主页链接。
2. 记录可选条件：市场、日期窗口、最多视频数、运营目标和对标账号。未提供时默认 50 条；显式更高或更低的视频上限均应尽力遵守，并披露实际取得样本与停止原因。对标账号按同一验证、取数和可比性规则执行。
3. 先检查已连接 MCP 是否实际提供 `creator_profile` 与 `creator_videos`，阅读其实时 schema 后优先使用这两个工具。不要猜测参数、端点或字段。
4. 用户请求分析公开 TikTok 账号时，读取该账号的公开主页和视频页已获授权。账号工具不可用、失败、为空或关键字段不足后，必须**自动执行公开浏览器降级**；不得询问用户许可，也不得先要求用户补充资料。遇到登录、验证码、反爬或访问限制即停止降级读取并记录原因。
5. 已通过主页或视频来源确认目标 Handle 或达人 ID 后，才可用 `creator_search` 补充账号汇总指标；模糊昵称搜索不得确认身份。逐项保留工具返回的统计窗口，未说明窗口的字段必须标为“窗口未注明”。
6. 收集视频时分别记录置顶与非置顶状态。近期内容分布默认排除置顶视频；只有显式时间窗口按发布时间确实包含该视频时才能纳入，同时仍须标注其置顶状态。
7. 执行完整性门槛。只有在浏览器降级实际失败、受阻或仍然不足，且缺失证据会改变主要结论时，才可一次只问一个聚焦问题并说明影响；优先请求备用 canonical profile 链接、公开截图或针对关键缺口的导出。用户明确要求继续时，提供部分报告。
8. 仅基于来源账本中的字段分析代表视频和账号类型；先给结果和行动，再给证据、明细、缺失信息与可信度。把“运营逻辑”写为重复样本支持的分析，把“可复用起号流程”和“带货指南”写为推导方案而非账号历史事实，并注明证据、假设或待验证项。

## 输出边界

- 使用报告模板的固定顺序，结果前置。
- 标明原始 URL、规范化 Handle、请求与实际采样范围、来源账本、假设、未公开/读取失败字段和 A/B/C 可信度。
- 只在有实际商品、销量或销售额字段时讨论带货表现；不要混淆播放量、销量和销售额，也不要用累计播放量与近 30 天销量推算转化率。
- 明确区分观察事实、证据支持的运营模式、可复用推断和待验证建议。
- 不绕过登录、验证码、反爬或访问控制；不请求、保存或输出 API Key、Token；不发布、私信、下单、改店或投放。
