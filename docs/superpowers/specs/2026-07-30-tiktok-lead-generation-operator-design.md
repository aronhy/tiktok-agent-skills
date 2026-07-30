# TikTok Lead Generation Operator Design

## Goal

Create a standalone Skill for TikTok accounts whose primary commercial goal is content-led customer acquisition and private-channel conversion rather than TikTok Shop sales.

The Skill must support:

1. building a lead-generation account from zero; and
2. converting an existing public TikTok account into a measurable lead-generation system.

## Skill Identity

- Skill name: `tiktok-lead-generation-operator`
- UI name: `TikTok 内容获客运营`
- Primary trigger: a user wants TikTok content to generate inquiries, qualified leads, appointments, consultations, form submissions, or movement into a configured private channel.

Do not use this Skill as the primary workflow when the user's main goal is TikTok Shop product sales, shop operations, or affiliate commerce. Use it for an explicitly lead-generation or private-conversion goal. If the user has both goals, keep the Shop transaction path and lead-generation path separate and ask which business outcome is primary when that choice changes the plan.

## Required Inputs

Collect four required inputs in this order:

1. target market;
2. product or service;
3. target customer;
4. desired private-conversion action and channel.

An input counts as received only when the user states it directly or supplies a prior brief that states it unambiguously. Do not infer a required input from an account bio, content sample, adjacent wording, or a typical industry default.

Use these focused questions:

1. “目标国家或地区是什么？它决定语言、受众、合规和承接渠道。”
2. “你希望通过 TikTok 获客的具体产品或服务是什么？”
3. “你最希望吸引的客户是谁？请描述客户类型、问题或购买场景。”
4. “希望客户完成什么私域动作，并进入哪个渠道？例如 TikTok 私信、WhatsApp、微信、表单或预约链接。”

The public TikTok account URL is optional:

- URL supplied: validate it through `tiktok-account-audit`, then use verified account evidence.
- URL absent: operate in from-zero mode and label account assumptions as hypotheses.

When a required input is missing, ask only the first missing item and stop. Do not output a formal positioning, content calendar, funnel, KPI target, or generic substitute. After the user supplies it, ask the next missing item. If the user explicitly cannot provide an item and still asks to continue, return only a bounded conditional framework with C confidence and state what cannot be decided.

Optional constraints include language, offer price, sales capacity, geography within the target market, team size, production capacity, budget, lead-handling hours, existing CRM or analytics, compliance restrictions, current account performance, and reference accounts.

## Supported Conversion Channels

Treat the destination as a configurable channel:

- TikTok direct message;
- WhatsApp;
- WeChat;
- Telegram;
- email;
- website or landing-page form;
- appointment or consultation booking link.

The Skill designs profile entry points, CTAs, qualification questions, response drafts, handoff stages, tracking fields, and follow-up timing. It never sends messages, submits forms, scrapes contact details, imports contacts, or changes external accounts.

## Operating Modes

### From-zero mode

Use the completed business brief to produce:

- audience and problem definition;
- positioning and value promise;
- profile name, bio, pinned-content, and link architecture;
- content pillars and proof assets;
- initial content experiments;
- channel-specific CTA and handoff;
- lead qualification and sales-capacity checks;
- measurement and review cadence.

All positioning and content claims remain hypotheses until supported by observed audience or lead data.

### Existing-account mode

Reuse `tiktok-account-audit` rather than duplicating its profile normalization, public-browser fallback, evidence ledger, sampling, representative-video analysis, and confidence rules.

Map verified account assets into:

- retain: audience, formats, proof, trust assets, recurring themes;
- change: positioning, profile packaging, weak or mismatched CTAs;
- add: missing funnel stages, qualification, tracking, follow-up, and content experiments;
- stop or pause: unmeasurable, conflicting, unsupported, or non-compliant tactics.

Do not infer private-message performance, form conversion, appointments, or sales from public views and engagement.

## Funnel Model

Build a traceable funnel with separate definitions and windows:

`content exposure → profile visit → CTA intent → channel arrival → qualified lead → appointment/opportunity → sale`

For each stage, specify:

- event definition;
- source system;
- attribution window;
- owner;
- available baseline;
- next experiment;
- expand, adjust, or stop condition.

Never substitute views, likes, followers, or raw messages for qualified leads. Never calculate downstream conversion when numerator and denominator come from incompatible windows, populations, or systems.

## Content System

Build content around the customer's decision journey rather than a fixed viral formula:

1. problem recognition;
2. diagnosis or education;
3. method or proof;
4. case evidence;
5. objection handling;
6. offer and next action.

For each pillar, provide a limited experiment matrix covering audience, problem, format, proof, CTA, channel, and measurement. Do not invent testimonials, client outcomes, credentials, scarcity, earnings, health results, or legal guarantees.

## Lead Handoff

Design a minimal handoff that includes:

- a single visible CTA per asset;
- the destination channel;
- an opening response draft;
- qualification questions;
- lead-status definitions;
- owner and response expectation;
- follow-up drafts and stop conditions;
- CRM or spreadsheet fields that avoid unnecessary personal data.

Drafts are reviewable content, not authorization to contact anyone.

## Output Contract

The formal report uses this order:

1. one-sentence acquisition position;
2. three immediate actions;
3. mode, inputs, assumptions, and confidence;
4. audience, problem, offer, and proof;
5. existing-account diagnosis or from-zero profile architecture;
6. funnel and channel handoff;
7. content pillars and experiment matrix;
8. profile, CTA, qualification, and follow-up drafts;
9. 30-day execution and review plan;
10. KPI dictionary and measurement plan;
11. compliance, privacy, capacity, and attribution risks;
12. missing information and next question.

The output must distinguish user-provided facts, verified public evidence, analysis, assumptions, and tests.

## Confidence

- **A:** complete business brief, verified account evidence when an account is in scope, and compatible downstream lead or CRM evidence.
- **B:** complete business brief and usable public account evidence, but downstream lead attribution or conversion data is incomplete.
- **C:** from-zero hypotheses, bounded output after an explicit missing-input exception, or materially limited account/lead evidence.

Confidence applies to the supported conclusion, not to the amount of prose.

## Safety and Compliance

- Do not send bulk or individual messages, submit forms, publish, comment, follow, change profiles, or operate external channels.
- Do not scrape, infer, expose, or enrich private contact information.
- Minimize personal-data fields and redact direct personal identifiers from examples and reports.
- Do not recommend spam, fake engagement, platform-rule evasion, deceptive redirects, fabricated proof, prohibited claims, or unverified urgency.
- Identify regulated or sensitive claims that require professional or jurisdiction-specific review.
- Treat platform rules, advertising rules, privacy law, and channel capabilities as time-sensitive; verify current requirements from authoritative sources when a recommendation depends on them.

## Files

Create:

- `skills/tiktok-lead-generation-operator/SKILL.md`
- `skills/tiktok-lead-generation-operator/agents/openai.yaml`
- `skills/tiktok-lead-generation-operator/references/workflow.md`
- `skills/tiktok-lead-generation-operator/references/report-template.md`

Update:

- `README.md`
- a compact validation record under `docs/superpowers/validation/`

Do not add scripts or assets in the first version.

## RED/GREEN Validation

Use fresh, isolated subagents with no access to the intended answers.

### RED controls

Run representative prompts without the new Skill and record exact omissions or boundary failures:

1. from-zero request missing all four required inputs;
2. existing-account request with a private-conversion goal and complete brief;
3. request that confuses views or messages with qualified leads;
4. request to scrape contacts and automatically send outreach.

For the input-gating wording, run at least five fresh-context control samples and manually inspect every output.

### GREEN

Run the same prompts with the new Skill:

- missing-input cases ask one question and stop;
- existing-account cases reuse account-audit evidence and do not invent private-funnel results;
- metric cases preserve stage definitions and compatible windows;
- unsafe automation cases refuse execution while providing compliant planning or drafts.

Run at least five fresh-context samples for the gating wording and manually inspect all outputs. Record concise results rather than full model transcripts when the evidence can be summarized without losing the tested behavior.

### Structural validation

- initialize the Skill with Skill Creator `init_skill.py`;
- validate it with `quick_validate.py`;
- confirm `agents/openai.yaml` field lengths and `$tiktok-lead-generation-operator` default prompt;
- validate all repository Skills;
- scan for placeholders, credential material, and unintended files;
- run `git diff --check`.
