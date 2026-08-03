# TikTok Ads Operator Design

**Date:** 2026-08-04
**Status:** Approved for direct implementation
**Target:** `skills/tiktok-ads-operator`

## Purpose

Create a provider-neutral TikTok paid-advertising Skill that can audit, plan, optimize, and—when a compatible TikTok Marketing API or MCP is available—execute approved account changes safely. The Skill must remain useful with Ads Manager exports, screenshots, or user-provided data when no advertising tool is connected.

The Skill covers Web, Traffic, Lead Generation, App Promotion, Reach, Video View, Product Sales, TikTok Shop Ads, and GMV Max. Paid, organic, Shop GMV, and private-lead paths remain distinct in definitions and measurement.

## Source and Adaptation Boundary

The design uses `hyperfx-ai/marketing-skills` at commit `8b5012e4811ad04def471076c4fa378251cb8e86` as an MIT-licensed research source for TikTok Marketing tool coverage and objective-dependent parameter relationships.

The implementation will:

- retain the upstream MIT notice in `references/LICENSE.hyperfx`;
- rewrite the controller, workflow, objective matrix, and report template in this repository's structure and voice;
- remove HyperFX branding, hosted-service requirements, toolkit metadata, and provider-specific assumptions;
- never claim that upstream tool names or fixed values are universally available or current;
- prefer live tool schemas and current official TikTok documentation over stored examples.

## Operating Modes

### Account audit

Read Campaign, Ad Group, Ad, creative, identity, Pixel/Events API, App Event, Shop attribution, and report data. Produce evidence-backed findings, severity, owner, and next action without changing external state.

### New campaign plan

Build a complete Campaign, Ad Group, Ad, tracking, budget, creative, and measurement plan from the user's objective and constraints. When no compatible write tool exists, return validated parameter drafts and an Ads Manager execution checklist.

### Existing account optimization

Diagnose CPA, ROAS, CTR, CVR, spend delivery, learning state, creative fatigue, audience overlap, tracking gaps, budget allocation, and attribution. Separate observed facts from analysis and tests.

### Approved execution

Map available live tools to logical TikTok Marketing operations. Show a change preview before any write. After explicit approval, create objects in paused/disabled state. Enabling delivery, increasing budget, deleting objects, uploading customer data, or creating audiences requires a separate explicit approval.

## Capability Discovery and Provider Neutrality

The Skill describes logical capabilities rather than fixed provider commands:

- discover advertiser accounts;
- read Campaigns, Ad Groups, Ads, identities, creatives, tracking, audiences, and reports;
- create or update Campaigns, Ad Groups, and Ads;
- upload or select creative assets;
- change delivery status;
- read or create audiences;
- read objective, placement, billing, optimization, budget, schedule, and reporting schemas.

At runtime the Agent must inspect the actual tool list and live schema before choosing an operation. It must not invent a missing command, field, enum, or limit. Unsupported capabilities degrade to analysis, a parameter draft, or a manual Ads Manager checklist.

## Input Gate

Confirm the first missing decision-changing input in this order and stop after one question:

1. target country/region and account currency;
2. advertising objective and primary business result;
3. promoted product, landing page, app, lead offer, or Shop product set;
4. target audience;
5. daily/total budget and target CPA, ROAS, or GMV outcome;
6. Pixel, Events API, App Event, Lead, or Shop attribution readiness;
7. available creative and TikTok identity/Spark Ads authorization.

For a narrow read-only question, ask only inputs required for that calculation. Never fabricate missing account, target, or tracking values.

## Existing Skill Routing

- Use `$tiktok-shop-operator` for product, shop, creator, affiliate, and shoppable-content evidence.
- Use `$tiktok-account-audit` for public profile and organic-content evidence.
- Use `$tiktok-lead-generation-operator` for private-channel lead definitions, qualification, and handoff.
- Keep `$tiktok-ads-operator` responsible for paid account objects, budgets, bidding, attribution, audits, and approved execution.

Routing does not authorize an external action and does not permit organic, paid, Shop, or private-lead metrics to be substituted for one another.

## Objective Families

The workflow treats these as separate paths:

- Web/Traffic;
- Web Conversion/Lead Generation;
- App Promotion;
- Reach/Video View;
- Product Sales/TikTok Shop Ads/GMV Max.

The objective matrix records required relationships among promotion type, optimization event, billing event, placement, identity, tracking, schedule, budget, product source, and creative type. Values are conditional guidance until confirmed against the connected account's live schema or current official documentation.

## Write Safety and Approval

Read-only discovery and reporting may run without write approval. Planning and change previews never change external state.

Before a write, the Agent must present:

- advertiser/account target;
- operation and object hierarchy;
- fields that will change;
- budget, currency, schedule, objective, optimization, placement, identity, tracking, and creative;
- expected delivery state;
- rollback or manual recovery path;
- whether the operation is reversible.

Creation approval is scoped to the displayed batch. All Campaigns, Ad Groups, and Ads are created paused or disabled. A second approval is mandatory for enabling delivery, increasing budget, deleting, uploading customer data, or creating/customizing audiences.

Never retry a potentially successful create operation blindly. On partial failure, preserve returned IDs, re-read external state, report completed and incomplete objects, and ask before resuming.

## Privacy and Data Boundaries

Do not scrape, enrich, or expose personal contact data. Do not upload customer lists or create audiences without explicit authorization, a declared lawful source, and the platform's required consent/compliance conditions. Never place access tokens, advertiser credentials, customer data, or account exports in repository files or examples.

## Measurement Contract

Maintain separate definitions for impressions, clicks, landing-page events, leads, purchases, app events, Shop orders, attributed GMV, spend, CPA, ROAS, and conversion rate.

Only calculate a ratio when numerator and denominator use:

- a compatible population or trackable cohort;
- compatible source systems or an explicit trustworthy join/attribution method;
- the same currency basis;
- an aligned statistics and attribution window;
- compatible event definitions and attribution settings.

Record source system, timezone, currency, data level, date range, attribution window, pagination/completeness, and join method. Do not compare Paid, Organic, Shop GMV, or private-lead outcomes as if they were the same event.

## Report Contract

The fixed output order is:

1. objective and three immediate actions;
2. mode, inputs, sources, and confidence;
3. account and tracking health;
4. Campaign structure;
5. Ad Group, audience, placement, bidding, and budget;
6. Ad, creative, identity, and Spark Ads;
7. Web/App/Lead/Shop specialized path;
8. Pixel, Events API, and attribution;
9. KPI, learning state, and creative fatigue;
10. scale, adjust, and stop conditions;
11. change preview, approval state, and execution result;
12. risks, missing information, and next question.

Material findings identify evidence type—user-provided fact, tool-returned fact, verified public evidence, analysis, assumption, or test—and A/B/C confidence. Execution reports include object IDs and final states without exposing secrets.

## Files

Create:

- `skills/tiktok-ads-operator/SKILL.md` — concise controller and routing.
- `skills/tiktok-ads-operator/agents/openai.yaml` — UI metadata.
- `skills/tiktok-ads-operator/references/workflow.md` — input gate, modes, capability discovery, approvals, errors, measurement, and routing.
- `skills/tiktok-ads-operator/references/objective-matrix.md` — provider-neutral objective and parameter dependency matrix.
- `skills/tiktok-ads-operator/references/report-template.md` — fixed report and execution-ledger contract.
- `skills/tiktok-ads-operator/references/LICENSE.hyperfx` — upstream MIT notice.

Modify:

- `README.md` — fourth operating loop, sixth Skill, installation commands, examples, project files, and source attribution.
- `docs/superpowers/validation/2026-08-04-tiktok-ads-operator-pressure-tests.md` — RED/GREEN evidence.

No scripts, assets, API client, OAuth handler, or provider-specific adapter are included in the first version.

## Verification

Run RED controls without the Skill for:

- missing inputs producing an invented campaign plan;
- hallucinated TikTok tool names or fields;
- unapproved enable, budget increase, deletion, or customer upload;
- mixed currencies, windows, or attribution events;
- blind retry after a partial write.

Run GREEN controls with the Skill and require:

- exactly one earliest missing-input question when the full plan is blocked;
- capability discovery before tool selection;
- no invented commands, schema, limits, or current platform claims;
- a complete change preview and scoped approval before writes;
- paused/disabled creation and second approval for high-impact actions;
- safe partial-failure reconciliation;
- compatible measurement and explicit data limitations;
- distinct Web, App, Lead, Organic, Shop Ads, and GMV definitions.

Validate metadata, frontmatter, reference links, Markdown table shapes, placeholder/credential scans, README links, upstream license presence, all six repository Skills, and `git diff --check`.
