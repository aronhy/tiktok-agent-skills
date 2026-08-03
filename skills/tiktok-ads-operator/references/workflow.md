# TikTok Ads Operating Workflow

Use this provider-neutral contract for every audit, plan, optimization, and approved execution. It describes logical advertising operations, not a particular API or tool. Paid, organic, Shop GMV, and private-lead evidence remain separate.

## 1. Input gate and narrow-question exception

For a full plan or execution, confirm the first missing decision-changing input in this exact order, then stop after asking **one** question:

1. target country/region and account currency;
2. advertising objective and primary business result;
3. promoted product, landing page, app, lead offer, or Shop product set;
4. target audience;
5. daily/total budget and target CPA, ROAS, or GMV outcome;
6. Pixel, Events API, App Event, Lead, or Shop attribution readiness;
7. available creative and TikTok identity/Spark Ads authorization.

For a narrow read-only question, request only inputs necessary for that calculation. Do not infer an account, market, objective, audience, tracking state, budget, KPI, or performance value.

## 2. Mode selection and existing-Skill routing

Select exactly one mode:

- **Account audit:** read account objects and reporting; make no external changes.
- **New campaign plan:** return a complete parameter draft and, without compatible write capability, a manual Ads Manager checklist.
- **Existing account optimization:** diagnose delivery and performance, distinguish facts from tests, and prioritize changes.
- **Approved execution:** perform only the displayed, approved batch through verified compatible capabilities.

Route adjacent evidence deliberately:

- `$tiktok-shop-operator` handles product, Shop, creator, affiliate, and shoppable-content evidence.
- `$tiktok-account-audit` handles public-profile and organic-content evidence.
- `$tiktok-lead-generation-operator` handles private-lead definitions, qualification, and handoff.

Routing does not authorize a write or allow organic, paid, Shop, and private-lead measurements to substitute for each other.

## 3. Evidence labels and confidence

Label every material finding, recommendation, and execution claim with one evidence type and a confidence grade.

| Evidence type | Meaning | Confidence rule |
| --- | --- | --- |
| User-provided fact | Information supplied by the user and not independently verified | A only when corroborated; otherwise B or C |
| Tool-returned fact | Data returned by a connected tool or live account schema | A when scope and completeness are recorded |
| Verified public evidence | Current official documentation or attributable public source | A when directly applicable; otherwise B |
| Analysis | Interpretation of recorded facts | B unless a direct deterministic calculation supports A |
| Assumption | Explicit provisional value used only to describe a branch | C; never use for execution |
| Test | Proposed experiment with a pass or stop condition | B before results; A only for observed results |

Use **A** for directly supported and complete evidence, **B** for supported but incomplete or interpretive evidence, and **C** for uncertain, assumed, unavailable, or unverified evidence. State the source, date range, level, timezone, and limitation whenever they affect confidence.

## 4. Capability discovery and logical-operation mapping

Before selecting a tool or field, inspect the available tools, permissions, advertiser access, and live schemas. Map only capabilities actually exposed at runtime to these logical operations:

If a request directs use of an assumed tool or asks to fill unspecified fields with defaults, begin the response by stating that capability and live-schema discovery is required. Do this before describing missing plan inputs, a parameter draft, or a manual fallback.

| Logical operation | Required discovery | Compatible fallback |
| --- | --- | --- |
| Discover advertiser accounts | Account-list capability and access scope | Ask for account export or account identifier |
| Read objects and reports | Read scope, object level, dimensions, pagination, and date controls | Analyze supplied export or screenshots with limitations |
| Create or update Campaign, Ad Group, or Ad | Write scope, hierarchy, required fields, and live enums | Produce a validated parameter draft and Ads Manager checklist |
| Select or upload creative | Asset capability, permitted formats, identity relationship, and authorization | List manual asset and authorization steps |
| Change delivery status | Status values, current state, and write scope | Provide a paused-state checklist only |
| Read or create audiences | Audience scope, source, consent requirements, and approval | Use audience hypothesis without creating or uploading data |
| Read schemas | Objective, placement, billing, optimization, budget, schedule, and report schema availability | Mark value as unverified and request official documentation or UI confirmation |

Never invent a command, field, enum, limit, eligibility rule, or current platform behavior. Capability absence changes the output to analysis, a parameter draft, or a manual checklist; it never authorizes guessing.

## 5. Audit and planning workflow

1. Apply the input gate appropriate to the requested mode.
2. Record available sources, access level, data level, date range, timezone, currency, pagination or completeness, and attribution window.
3. Discover logical capabilities and validate the live schema before selecting an operation or conditional parameter relationship.
4. For an audit or optimization, read Campaign, Ad Group, Ad, creative, identity, tracking, audience, Shop attribution, and report information that is actually available. Identify facts, gaps, severity, owner, and next action.
5. For a plan, select the objective family in [objective-matrix.md](objective-matrix.md), then validate the destination, optimization, billing, placement, identity, tracking, product source, creative, schedule, budget, currency, and eligibility relationships.
6. Separate observations, analysis, assumptions, and tests. Include a measurement limitation instead of calculating an incompatible KPI.
7. Produce the fixed output from [report-template.md](report-template.md), including the next single decision-changing question when blocked.

## 6. Write preview and approval state machine

Planning and previews are read-only. Before each external write, present the complete change preview and obtain an explicit approval whose scope exactly matches the displayed batch.

```text
READ_ONLY → PREVIEW_READY → CREATE_APPROVED → CREATED_PAUSED
CREATED_PAUSED → ENABLE_APPROVED → ENABLED
ANY_STATE → PARTIAL_FAILURE → RECONCILED → NEXT_APPROVAL
```

`CREATE_APPROVED` requires the advertiser target, hierarchy, changed fields, budget and currency, schedule, objective, optimization, placement, identity, tracking, creative, expected delivery state, reversibility, recovery path, and approval scope. Approval to plan, inspect, or preview is not approval to write.

## 7. Paused creation and second-approval operations

Create Campaigns, Ad Groups, and Ads in `paused/disabled` delivery state. A creation approval applies only to that paused batch.

Require a separate explicit second approval, after a refreshed preview, to:

- enable delivery;
- increase budget;
- delete an object;
- upload customer data;
- create or customize an audience.

Do not combine an enable, budget increase, delete, audience operation, or customer-data upload with a generic creation approval.

## 8. Partial failure, idempotency, reconciliation, and stop rules

Never blindly retry a potentially successful create. Preserve returned IDs, request identifiers, timestamps, parent IDs, and final state reported by the tool. On timeout, uncertain response, or partial failure:

1. stop further writes for the affected batch;
2. re-read the relevant external account state and hierarchy;
3. reconcile completed, incomplete, duplicate-risk, and unknown objects against the intended preview;
4. report the reconciliation and the available recovery paths;
5. ask for the next approval before resuming, repairing, deleting, enabling, or retrying.

If identity, advertiser target, scope, required schema, approval, tracking consent, currency, or measurement compatibility is unknown, stop at the first blocking condition and request the one next decision-changing input. Never delete or recreate merely to recover from an uncertain create.

## 9. Privacy and audience-data boundary

Do not scrape, enrich, expose, or store personal contact data. Do not put access tokens, advertiser credentials, customer data, or account exports in repository files or examples. Uploading a customer list or creating/customizing an audience requires explicit authorization, a declared lawful source, and the platform-required consent and compliance conditions, followed by the separate second approval.

## 10. Compatible measurement and attribution rules

Maintain distinct definitions for impressions, clicks, landing-page events, leads, purchases, app events, Shop orders, attributed GMV, spend, CPA, ROAS, and conversion rate. Calculate a ratio only when numerator and denominator have all of the following:

- a compatible population or trackable cohort;
- compatible source systems or an explicit trustworthy join or attribution method;
- the same currency basis;
- aligned statistics and attribution windows;
- compatible event definitions and attribution settings.

If any condition is incompatible or missing, refuse the ratio and request aligned inputs. Never calculate or display illustrative, rough, or proxy arithmetic from incompatible inputs, even when labeling it non-comparable.

Record source system, timezone, currency, data level, date range, attribution window, pagination or completeness, and join method. Do not call a paid spend-to-Organic result, Shop GMV, or private-lead count a unified ROAS, CPA, or conversion rate without an explicit compatible measurement contract.

## 11. Current-schema and current-documentation rule

Stored examples and this workflow are conditional guidance only. The live account schema and current official documentation override every stored example. If the live schema and documentation disagree, preserve the evidence, do not guess, and ask for confirmation or use the supported manual flow.

## 12. Fixed output and next-question behavior

Use the twelve ordered sections in [report-template.md](report-template.md). Include no external mutation unless the state machine has reached the relevant approved state. When full planning or execution is blocked, end with exactly one earliest missing decision-changing question. For a narrow read-only response, ask only the missing input required for that calculation and state any resulting limitation.
