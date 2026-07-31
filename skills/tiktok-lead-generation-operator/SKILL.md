---
name: tiktok-lead-generation-operator
description: Use when a user needs TikTok lead generation, private-traffic or private-channel conversion, inquiry capture, appointment or consultation acquisition, or an existing TikTok account converted into a measurable lead-generation system.
---

# TikTok 内容获客运营

开始任何分析前，完整读取 [references/workflow.md](references/workflow.md) 与 [references/report-template.md](references/report-template.md)。详细定义、边界和报告格式只以两份参考为准。

## 控制流程

边界请求优先于定制方案输入门槛：仅判断指标可计算性时，直接应用同一统计总体或可追踪 cohort、同一归因窗口和兼容来源系统规则。兼容来源系统是指事件位于一个系统内，或存在明确、可信、可审计的跨系统关联/归因方法；漏斗与 KPI 必须记录关联键/方法。只有纯禁止操作请求（仅要求抓取联系人或执行外联，不含其他合规定制规划）可绕过四项输入门槛；此时必须拒绝，并同时提供不依赖业务假设的自愿加入内容方向、一个 CTA、资格问题与仅供审核的回复草稿，四项缺一不可。混合请求若同时包含禁止操作和合规定制规划，先只拒绝禁止操作，再对合规部分执行以下输入门槛；有缺项时只问第一个缺项，不提供定制策略：

1. 依次确认目标国家或地区、具体产品或服务、目标客户、期望私域动作及渠道。一次只问第一个未收到的问题，然后停止；“收到”仅限用户明确表述或所给 brief 无歧义说明。
2. 未提供账号 URL 时使用从零模式；所有定位和内容结论均标为假设。提供 URL 时，先复用 `$tiktok-account-audit` 的公开证据、来源账本与可信度规则，再做获客改造；绝不把公开互动推断成私域转化。
3. 仅输出计划、可审阅的内容和回复草稿。不得发送私信、抓取或导入联系人、提交表单、发布内容，或改动任何外部账号。
4. 以报告模板的固定十二节顺序交付，并为结论标注 A/B/C 可信度。

| 情况 | 处理 |
| --- | --- |
| 缺少必填输入 | 只问固定顺序中的第一个缺项并停止。 |
| 无账号 URL | 从零模式；将账号、受众和结果写为假设。 |
| 有账号 URL | 复用 `tiktok-account-audit`，仅采用已验证公开证据。 |
| Shop 为主目标 | 转交 `$tiktok-shop-operator`，不以本 Skill 为主流程。 |
| Shop 与线索并存 | 分开交易与线索路径；若影响方案，询问哪个业务结果优先。 |

缺项在用户明确说明无法提供某项但仍要求继续时，才给出带条件的有限框架，可信度为 C；“直接给方案”或“不要问问题”不等于无法提供，仍须只问第一个缺项并停止；不得伪装为定制正式报告。
