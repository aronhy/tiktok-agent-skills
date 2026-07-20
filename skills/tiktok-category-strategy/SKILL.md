---
name: tiktok-category-strategy
description: Use when a user wants to evaluate, enter, position, or operate a TikTok or TikTok Shop category and needs market, product, shop, content, creator, competition, or category strategy.
---

# TikTok 类目策略

基于可追溯的商品、店铺、视频、达人和官方趋势证据，判断一个 TikTok/TikTok Shop 类目的进入条件，并形成运营策略；不把缺失数据、单个爆款或行业传闻写成市场事实。

## 先加载参考

在调用工具或下结论前，必须完整读取：

1. [references/workflow.md](references/workflow.md)
2. [references/report-template.md](references/report-template.md)

需要选择 KSS MCP 工具、参数、返回字段或关联方式时，再读取共享的 [../tiktok-shop-operator/references/mcp-tools.md](../tiktok-shop-operator/references/mcp-tools.md)。实际连接的 MCP schema 始终优先于该参考。

## 控制流程

1. 确认目标类目和目标国家或地区。两者都是阻断输入；缺任一项时，一次只反问一个最关键的问题。目标市场缺失时，先问“目标国家或地区是什么？它会改变趋势、价格、政策和竞争格局。”在得到答案前，不输出市场特定的趋势、价格、政策、竞争或量化基准。用户明确要求继续时，只提供清楚标注为通用且条件性的部分框架，并列出缺失市场及其影响。
2. 记录可选条件：价格带、账号类型、商业目标、预算、内容语言和计划周期。不要把未给出的条件补成行业基准、固定内容数量、视频时长、达人层级或效果阈值。
3. 按工作流规范化类目和市场，用 `product_search` 建立候选商品池，再以 `shop_search`、`video_search`、`creator_search` 和代表视频的 `caption_extract` 补齐相互关联的证据。保留对象 ID、地区、币种、统计窗口、分页范围和停止原因。
4. 以 TikTok Creative Center 的同地区、同类目公开趋势作为当前官方补充；它不可访问、需登录、受地区限制或字段不足时明确记录，不能用搜索摘要、记忆或其他网页冒充其数据。
5. 先完成允许的重试、分页、ID 关联和公开浏览器读取，再检查完整性。关键缺失会改变进入结论、切入方向或合规判断时，只问一个问题；用户要求继续时提供带影响范围和 A/B/C 可信度的部分报告。
6. 只有跨来源且口径可比的证据才能支持进入、竞争、需求、内容或达人结论。结果前置：先按报告模板给“是否建议进入”和三个优先动作，后给证据、明细、限制和风险；把观察、推断、假设和待验证项分开写。

## 输出边界

- 使用固定报告模板；标明目标市场、收集时间、筛选条件、样本/页数、来源、完整性、默认值、缺失字段和可信度。
- 不混淆播放量、销量、销售额、商品价格、视频发布时间和统计窗口；不跨地区、币种或不兼容周期合并排名。
- 不编造 KSS 字段、Creative Center 趋势、政策要求、链接、成功调用、配额状态或量化行业基准。没有返回的字段写“未提供”；浏览器无法读取写“读取失败”或“未公开”。
- 不绕过登录、验证码、反爬、地域限制或访问控制；不保存或回显 API Key、Token、Cookie 或其他凭证；不发布、投放、下单、改店或联系达人。
