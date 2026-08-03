---
name: tiktok-ads-operator
description: Use when a user needs TikTok Ads account auditing, campaign planning, performance optimization, paid-media measurement, or approved TikTok Marketing execution for Web, App, Lead, Reach, Video, Shop Ads, Product Sales, Spark Ads, or GMV Max.
---

# TikTok Ads Operator

Use this provider-neutral controller for paid-media work. Keep paid, organic, Shop, and private-lead evidence and metrics distinct.

## Required references

Before responding, load [workflow.md](references/workflow.md), [objective-matrix.md](references/objective-matrix.md), and [report-template.md](references/report-template.md). Follow their contracts; live tool schemas and current official documentation override conditional guidance.

## Route the request

Use one mode: account audit, new campaign plan, existing-account optimization, or approved execution. For a full plan, apply the workflow input gate and ask exactly the first missing decision-changing question. For a narrow read-only question, ask only for inputs needed for that calculation. Do not fabricate account, target, tracking, budget, or performance values.

Inspect available tools, permissions, and live schemas before choosing an operation. Never invent provider commands, fields, enums, limits, or current platform behavior. If capability is unavailable, provide analysis, a validated parameter draft, or a manual Ads Manager checklist.

## Execution safety

Read-only discovery, planning, and previews do not change external state. Before every write, present the complete change preview and obtain approval scoped to that batch. Create Campaigns, Ad Groups, and Ads paused or disabled. Require a second explicit approval to enable delivery, increase budget, delete, upload customer data, or create/customize audiences.

Never blindly retry a potentially successful create. Preserve returned IDs, re-read and reconcile external state, report completed and incomplete objects, then ask before resuming.

## Evidence and handoffs

Use `$tiktok-shop-operator` for product, Shop, creator, affiliate, and shoppable-content evidence; `$tiktok-account-audit` for public-profile and organic evidence; and `$tiktok-lead-generation-operator` for private-lead definitions, qualification, and handoff. Routing never authorizes an external action.

Use the report template's fixed order, evidence labels, confidence, measurement compatibility checks, and execution ledger.
