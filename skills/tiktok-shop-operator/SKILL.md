---
name: tiktok-shop-operator
description: Operate TikTok Shop research and planning with KSS MCP across product discovery, shop analysis, viral commerce videos, creator matching, caption extraction, pagination, sorting, and evidence-based action plans. Use when a user asks to research TikTok Shop products, shops, videos, creators, subtitles, competitors, or a complete commerce operations workflow.
---

# TikTok Shop Operator

Use KSS MCP to turn natural-language TikTok Shop research requests into verified results and actionable operating plans.

## Load References

1. Read [references/mcp-tools.md](references/mcp-tools.md) before choosing tools, parameters, joins, or return fields.
2. Read [references/workflows.md](references/workflows.md) for operational or multi-step tasks.
3. Read [references/output-templates.md](references/output-templates.md) before presenting results.
4. Read [references/examples.md](references/examples.md) only when intent, pagination, missing-data, or error behavior is unclear.

Do not duplicate detailed tool schemas in this file.

## Interpret the Request

1. Extract region, keyword/category, price, rating, sales, revenue, growth rate, date window, requested count, and sorting.
2. Preserve every explicit user condition.
3. Ask one concise question only when a missing condition blocks a valid query.
4. Use an MCP default only when valid, and disclose it in the query scope.
5. Identify whether the task needs one tool or a cross-tool workflow.

## Choose Tools

- Use `product_search` for products, prices, ratings, sales, growth, shops, and product-level trend filters.
- Use `shop_search` for shops, service metrics, shop ratings, product counts, and shop-level sales.
- Use `video_search` for commerce videos, video sales, engagement, products, shops, and embedded creator data.
- Use `creator_search` for creator discovery and creator metrics.
- Use `caption_extract` for captions from a specific TikTok video URL.

Use IDs for joins. For video-to-creator enrichment, query by returned Handle/name with `searchType=N`, then confirm the returned creator ID against the video creator ID when available.

## Execute Safely

1. Query the smallest useful scope first.
2. When the user requests “all,” paginate until there is no next page, the tool limit is reached, or quota/rate limiting stops collection.
3. Never call the first page “all results.”
4. Deduplicate paginated objects by their stable ID.
5. Apply the user's sort. If none is provided, sort the relevant returned sales amount descending.
6. Do not invent an `orderField` value absent from the reference. Sort locally within the disclosed collected scope when necessary.
7. Keep regions, currencies, and time windows separate.
8. Check the live tool schema or official examples before encoding a percentage growth filter; never guess whether 50% means `50` or `0.5`.
9. Never invent fields, results, links, quota status, or successful tool calls.
10. Write missing fields as “未提供.”
11. Stop on 401, unavailable tools, `ERROR_API_MINUTE_MAX`, or `ERROR_API_MONTH_MAX`, and report completed versus remaining work.
12. Do not silently broaden filters after an empty result. Suggest one relaxation and wait for approval.

## Respect Action and Credential Boundaries

1. Never repeat, store, log, or place a user-provided MCP Key in generated files, examples, or answers.
2. Refer to credentials only as `${KSS_MCP_KEY}` or a redacted value.
3. Do not purchase quota, change subscriptions, bypass limits, or weaken platform restrictions.
4. Do not contact creators, send messages, place orders, or publish TikTok content.
5. Produce research, drafts, recommendations, and action plans only. Treat any external execution as a separate user-authorized task requiring an appropriate tool.

## Present Results

1. Start with the query scope: region, period, filters, sorting, pages, row count, and completeness.
2. Use the applicable template from `references/output-templates.md`.
3. Mark interrupted collections as “非完整结果.”
4. Use MCP-returned shop links directly.
5. Construct creator or video links only when the required Handle and ID are present.
6. Do not invent product links.
7. Separate evidence from interpretation.
8. End analytical tasks with:
   - the main conclusion;
   - supporting evidence;
   - uncertainty and risks;
   - prioritized next actions.

## Handle Missing MCP

If the KSS MCP tools are unavailable, explain that MCP must be connected or refreshed. For 401, tell the user to verify the `secret-key` header and MCP permission. Never substitute ordinary web results and present them as KSS MCP data.
