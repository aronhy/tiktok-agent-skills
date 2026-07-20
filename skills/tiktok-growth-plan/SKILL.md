---
name: tiktok-growth-plan
description: Use when a user provides or discusses both a TikTok account and a target category and wants fit analysis, repositioning, transition strategy, growth planning, or an executable operating roadmap.
---

# TikTok 增长计划

将账号能力与目标类目机会匹配为可执行的 30 天增长计划。只用可追溯、口径可比的上游证据；不把缺失数据、未经验证的基准或推测写成事实。

## 先加载参考并复用上游工作流

在调用工具、编排上游分析或下结论前，必须完整读取：

1. [references/workflow.md](references/workflow.md)
2. [references/report-template.md](references/report-template.md)

本 Skill 是编排层，必须复用而非重写以下上游 Skill 的工作流、实时 MCP schema、浏览器降级、来源账本、分页、完整性、可信度与安全规则：

1. [../tiktok-account-audit/SKILL.md](../tiktok-account-audit/SKILL.md) 及其 `references/workflow.md`、`references/report-template.md`
2. [../tiktok-category-strategy/SKILL.md](../tiktok-category-strategy/SKILL.md) 及其 `references/workflow.md`、`references/report-template.md`

需要了解 KSS 工具事实时，由上游工作流读取 [../tiktok-shop-operator/references/mcp-tools.md](../tiktok-shop-operator/references/mcp-tools.md)。实际连接的 MCP schema 始终优先；本 Skill 不得复制、猜测或扩展工具参数、字段和端点。

## 控制流程

1. 依次检查四项**首次阻断输入**：公开 TikTok 账号主页 URL、目标类目、目标国家或地区、商业目标。首次发现缺失时，每次只问最高优先级的一个缺失项并停止：账号 URL 缺失时问“请提供公开 TikTok 账号主页 URL；它用于确认账号身份和现有能力。”；其后依次询问类目、市场、商业目标。此时不调用研究工具，不输出适配判断、条件计划、内容配比、进入建议或正式报告。
2. 只有在反问后，用户明确表示**不能或不愿提供**该缺失输入并同时要求继续时，才允许有界部分输出：不调用缺失输入所依赖的上游研究；所有因此不可得的定位、适配与进入决定写“暂无法判断”；逐项列出缺失输入、影响和假设；仅给不依赖该缺失项且仍成立的条件框架/动作，标为 C（低可信度）。不能把这项例外用于默认、猜测或弱化首次反问。
3. 记录可选约束：预算、团队能力、库存、每日可投入时间、价格带、计划周期、内容语言和对标账号。它们不是绝对门槛；未提供时标为“未提供”，不伪造行业基准、固定频次、预算、转化率或结果承诺。
4. 对通过门槛的输入，完整执行 `tiktok-account-audit` 工作流，保留其规范化账号、样本范围、来源账本、字段状态、代表内容、账号能力和可信度。
5. 完整执行 `tiktok-category-strategy` 工作流，保留其类目/市场映射、来源账本、可比窗口、进入结论、机会、内容/商品/达人证据、风险和可信度。
6. 按增长工作流对齐市场、类目、时间窗口、对象 ID、币种、样本范围和指标定义；不可对齐的数据分开展示，不能据此比较或计算适配度。
7. 结果前置：先按固定报告模板给运营定位、进入决策和三个立即执行动作，再给适配证据、保留/停止/新增、三条路径、日历、指标和限制。
8. 若上游已完成允许的获取、重试、分页、关联和公开浏览器降级后，仍有会改变定位、进入决策或首要行动的缺失，只问一个最关键的问题并停止深度判断。用户明确表示无法补充且要求继续时，按同一有界部分输出规则处理。

## 输出边界

- 使用固定报告模板；将观察、推断、假设和待验证项分开写。适配或进入判断必须列出账号与类目两侧的支撑证据、反证/缺口及适用条件。
- 30 天日历、内容数量/比例、增长路径和 KPI 必须受已知预算、团队、库存和时间约束；缺失约束时写成待验证假设与调整条件，而不是默认运营标准。
- 不混淆播放量、互动、销量、销售额、价格、视频发布时间和统计窗口；不把单条爆款、单一来源或不可比样本当作账号或市场结论。
- 用户自述账号同时包含普通内容和带货内容时，只可把账号类型写为“混合型（用户提供）”，不得称为基于样本测量的账号分类或表现证据。
- 不绕过登录、验证码、反爬、地域限制或访问控制；不请求、保存或输出 API Key、Token、Cookie；不发布、私信、下单、改店或投放。
