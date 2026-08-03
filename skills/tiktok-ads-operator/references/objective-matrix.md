# TikTok Ads Objective Matrix

This matrix is provider-neutral. It records relationships that must be validated, not fixed API values or promises of account eligibility. The live account schema and current official documentation override every stored example in this file.

## Objective-family dependencies

| Objective family | Destination or promotion type | Optimization and billing or bid compatibility | Tracking source | Placement, identity, and Spark authorization | Product or catalog source | Creative type | Schedule, budget or currency floor, and account eligibility |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Traffic/Web | Website or web destination | Validate traffic optimization event and compatible billing or bid method | Landing-page measurement and applicable pixel events | Validate permitted placement, identity, and Spark authorization | Not required unless product-linked destination is used | Click-oriented web creative | Validate dates, budget, account currency floor, and advertiser eligibility |
| Web Conversion/Lead Generation | Website conversion path or native lead offer | Validate conversion or lead event and compatible billing or bid method | Pixel, Events API, native Lead, or verified lead handoff | Validate permitted placement, identity, and Spark authorization | Validate lead offer or product source when applicable | Conversion or lead-form creative | Validate dates, budget, account currency floor, and objective eligibility |
| App Promotion | App-store destination or app promotion | Validate app event optimization and compatible billing or bid method | App Event source and measurement partner relationship when used | Validate permitted placement, identity, and Spark authorization | App package and store destination | App-focused creative | Validate dates, budget, account currency floor, app linkage, and eligibility |
| Reach | Reach or awareness destination | Validate reach optimization and compatible billing or bid method | Reach reporting source; do not substitute conversion events | Validate permitted placement, identity, and Spark authorization | Not normally required | Awareness creative | Validate dates, budget, account currency floor, and reach eligibility |
| Video View | Video-view destination or content objective | Validate view optimization and compatible billing or bid method | Video-view reporting source | Validate permitted placement, identity, and Spark authorization | Not normally required | Video-view creative | Validate dates, budget, account currency floor, and video-view eligibility |
| Product Sales/Shop Ads/GMV Max | Product sales destination, Shop product set, or catalog-linked promotion | Validate sales or GMV event and compatible billing or bid method | Shop attribution, pixel, Events API, or approved product-event source | Validate permitted placement, identity, and Spark authorization | Validate catalog, Shop, product set, and product availability | Product or Shop creative | Validate dates, budget, account currency floor, Shop linkage, catalog readiness, and eligibility |

## Logical tool capabilities

Discover the actual runtime capability and live schema before use. A capability may be absent or differently named.

| Logical capability | Validate before use | No-capability output |
| --- | --- | --- |
| Discover advertiser accounts | Account visibility and advertiser scope | Request account identifier or export |
| Read Campaign, Ad Group, Ad, identity, creative, tracking, audience, and reports | Read permission, object coverage, report dimensions, pagination, and data freshness | Analyze user-provided export or screenshots |
| Create or update Campaign, Ad Group, and Ad | Write scope, hierarchy, required fields, state values, and idempotency behavior | Parameter draft and Ads Manager checklist |
| Upload or select creative assets | Asset rights, permitted formats, identity relationship, and authorization | Manual asset preparation checklist |
| Change delivery status | Current status, allowed transition, and write scope | Explain required preview and approval only |
| Read or create audiences | Scope, data source, lawful basis, consent conditions, and separate approval | Audience hypothesis without audience creation |
| Read objective, placement, billing, optimization, budget, schedule, and reporting schemas | Live values, dependencies, eligibility, and current documentation | Mark as unverified and request confirmation |

## Report data levels and compatible dimensions

Only compare or calculate across rows when population, source or join method, currency, date range, attribution window, timezone, event definition, and attribution settings are compatible.

| Data level | Common dimensions to record | Compatible comparison rule | Incompatible shortcut to reject |
| --- | --- | --- | --- |
| Account | Advertiser, account currency, timezone, date range, attribution setting | Same advertiser and aligned reporting controls | Different advertiser currencies treated as one total |
| Campaign | Objective family, campaign, date range, currency, attribution window | Same objective event and aligned window | Mixing reach with purchase efficiency as one KPI |
| Ad Group | Audience, placement, optimization, billing or bid, schedule, budget | Same optimization and compatible audience or placement cohort | Comparing different optimization events as equivalent CPA |
| Ad | Creative, identity, Spark authorization, delivery state | Same delivery and attribution context | Treating organic engagement as paid conversion evidence |
| Event or conversion | Source system, event name, join method, attribution window | Explicit trustworthy join and same event definition | Dividing private-lead count by paid spend without a join contract |
| Shop order or GMV | Shop, product set, order event, GMV currency, attribution window | Same Shop attribution and currency basis | Calling Shop GMV from another window universal ROAS |

## Dangerous operations and required approval

| Operation | Required state and approval | Safety condition |
| --- | --- | --- |
| Read data or discover schema | READ_ONLY; no write approval | Record scope and limitations |
| Prepare plan or preview | PREVIEW_READY; no write approval | Do not change external state |
| Create Campaign, Ad Group, or Ad | CREATE_APPROVED for the displayed batch | Create paused/disabled only |
| Enable delivery | ENABLE_APPROVED; separate explicit approval | Refresh preview and state before enable |
| Increase budget | Separate explicit second approval | Refresh budget, currency, object, and recovery path |
| Delete an object | Separate explicit second approval | Re-read state and state irreversibility |
| Upload customer data | Separate explicit second approval | Declared lawful source and required consent conditions |
| Create or customize audience | Separate explicit second approval | Validate source, consent, and platform conditions |

## Errors: correction versus reconciliation

| Error condition | Required response | Category |
| --- | --- | --- |
| Missing required input, target, approval, or lawful basis | Ask the one next decision-changing question; do not write | Correction |
| Invalid or unsupported field, enum, budget, schedule, placement, or eligibility | Re-discover live schema or correct the draft; do not guess | Correction |
| Incompatible currency, event definition, source, population, or attribution window | Refuse the ratio; request compatible measurement inputs | Correction |
| Validation failure before any write is accepted | Correct preview then obtain fresh scoped approval | Correction |
| Timeout after create request, uncertain response, or returned partial object IDs | Preserve returned IDs; re-read and reconcile before any next write | Reconciliation |
| Some hierarchy objects created and later objects failed | Report completed and incomplete objects; stop and ask before resuming | Reconciliation |
| Duplicate-risk response or idempotency uncertainty | Re-read external state; do not blindly retry or delete and recreate | Reconciliation |
