---
name: tiktok-lead-generation-operator
description: Use when a user needs TikTok lead generation, private-traffic or private-channel conversion, inquiry capture, appointment or consultation acquisition, or an existing TikTok account converted into a measurable lead-generation system.
---

# TikTok 内容获客运营

开始任何分析前，完整读取 [references/workflow.md](references/workflow.md) 与 [references/report-template.md](references/report-template.md)。详细定义、边界和报告格式只以两份参考为准。

## 控制流程

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

缺项在用户明确无法提供但仍要求继续时，才给出带条件的有限框架，可信度为 C；不得伪装为定制正式报告。
