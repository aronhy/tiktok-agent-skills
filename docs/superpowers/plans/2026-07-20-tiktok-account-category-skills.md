# TikTok Account and Category Skills Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add three validated Codex Skills that analyze arbitrary public TikTok accounts, research target categories, and combine both into an evidence-backed 30-day growth plan.

**Architecture:** Keep `tiktok-shop-operator` as the shared commerce-data foundation. Add `tiktok-account-audit`, `tiktok-category-strategy`, and `tiktok-growth-plan` as focused workflow Skills; account data prefers optional `creator_profile` and `creator_videos` MCP tools and falls back to public browser reading. Put detailed workflows and report templates in each Skill's `references/` directory so `SKILL.md` stays concise.

**Tech Stack:** Markdown Agent Skills, YAML `agents/openai.yaml`, KSS MCP, Codex in-app browser, Python skill-creator validation scripts, Git.

## Global Constraints

- Analyze arbitrary public TikTok accounts, including competitors.
- Prefer `creator_profile` and `creator_videos`; use public browser reading only when MCP is unavailable, fails, or lacks key fields.
- Automatically classify accounts as content, commerce, brand, or mixed; allow primary and secondary types.
- Ask one key question at a time when missing information would change the conclusion.
- Put conclusions and prioritized actions before supporting analysis.
- Never bypass login, CAPTCHA, anti-bot, or access controls.
- Never invent missing data or treat missing values as zero.
- Never request that users paste credentials into chat or persist real credentials in the repository.
- Preserve the existing `tiktok-shop-operator` behavior and references.

---

## File Map

**Create:**

- `docs/superpowers/validation/2026-07-20-skill-pressure-tests.md` — RED/GREEN pressure-test evidence.
- `skills/tiktok-account-audit/SKILL.md` — account-audit trigger and compact execution rules.
- `skills/tiktok-account-audit/agents/openai.yaml` — UI metadata.
- `skills/tiktok-account-audit/references/workflow.md` — account data collection, classification, fallback, completeness gate, and analysis workflow.
- `skills/tiktok-account-audit/references/report-template.md` — results-first account report format.
- `skills/tiktok-category-strategy/SKILL.md` — category-strategy trigger and compact execution rules.
- `skills/tiktok-category-strategy/agents/openai.yaml` — UI metadata.
- `skills/tiktok-category-strategy/references/workflow.md` — product/shop/video/creator/trend research workflow.
- `skills/tiktok-category-strategy/references/report-template.md` — results-first category report format.
- `skills/tiktok-growth-plan/SKILL.md` — combined account/category growth trigger and orchestration rules.
- `skills/tiktok-growth-plan/agents/openai.yaml` — UI metadata.
- `skills/tiktok-growth-plan/references/workflow.md` — gap analysis and 30-day planning workflow.
- `skills/tiktok-growth-plan/references/report-template.md` — results-first growth plan format.

**Modify:**

- `skills/tiktok-shop-operator/references/mcp-tools.md` — document optional account tools and availability/fallback rules without inventing an undocumented schema.
- `README.md` — introduce the three Skills, data hierarchy, examples, installation, and limits in Chinese-first copy.

---

### Task 1: Record RED Baseline Pressure Tests

**Files:**

- Create: `docs/superpowers/validation/2026-07-20-skill-pressure-tests.md`

**Interfaces:**

- Consumes: the approved design spec and the repository without the three new Skills.
- Produces: recorded baseline failures and exact expectations for GREEN retests.

- [ ] **Step 1: Run baseline account-fallback scenario without loading a new Skill**

Prompt an isolated agent with:

```text
分析这个任意公开 TikTok 竞争对手账号：https://www.tiktok.com/@example
假设 creator_profile 成功，但 creator_videos 不可用。请说明接下来如何做，并给出运营报告。
```

Record whether it explicitly uses browser fallback, avoids inventing video metrics, reports missing fields, and asks a focused question if the missing data blocks a conclusion.

- [ ] **Step 2: Run baseline missing-market scenario**

Prompt an isolated agent with:

```text
我想做美妆类目，帮我制定 TikTok 运营策略。
```

Record whether it asks for the target country before using market-specific trends, prices, policy, or competition claims.

- [ ] **Step 3: Run baseline mixed-account growth scenario**

Prompt an isolated agent with:

```text
一个账号既发知识内容，也发带货内容。请判断它是否适合进入家居类目，并制定 30 天计划。现有数据不完整，但用户要求继续。
```

Record whether it recognizes a mixed account, distinguishes observed facts from assumptions, and labels the partial report's confidence.

- [ ] **Step 4: Create the pressure-test record**

Write one section per scenario with: prompt, baseline response summary, observed failures, required post-Skill behavior, and final GREEN result expressed as an unchecked checklist rather than a free-text completion marker.

- [ ] **Step 5: Verify RED evidence exists**

Run:

```bash
rg -n "Baseline failures|Required GREEN behavior|\[ \] GREEN" docs/superpowers/validation/2026-07-20-skill-pressure-tests.md
```

Expected: all three scenarios contain those headings or checklist markers.

- [ ] **Step 6: Commit**

```bash
git add docs/superpowers/validation/2026-07-20-skill-pressure-tests.md
git commit -m "test: record TikTok skill baseline scenarios"
```

### Task 2: Create `tiktok-account-audit`

**Files:**

- Create: `skills/tiktok-account-audit/SKILL.md`
- Create: `skills/tiktok-account-audit/agents/openai.yaml`
- Create: `skills/tiktok-account-audit/references/workflow.md`
- Create: `skills/tiktok-account-audit/references/report-template.md`
- Modify: `skills/tiktok-shop-operator/references/mcp-tools.md`

**Interfaces:**

- Consumes: TikTok profile URL; optional market, date window, max video count, objective, and comparison accounts.
- Produces: normalized handle, source ledger, account-type classification, representative-video analysis, results-first report, missing-data questions, and confidence grade.

- [ ] **Step 1: Scaffold the Skill with the official initializer**

Run:

```bash
python3 /Users/hy/.codex/skills/.system/skill-creator/scripts/init_skill.py tiktok-account-audit --path skills --resources references --interface 'display_name=TikTok 账号诊断' --interface 'short_description=读取公开账号与视频，生成结果前置的运营诊断报告' --interface 'default_prompt=使用 $tiktok-account-audit 分析这个公开 TikTok 账号并生成运营诊断报告。'
```

Expected: the Skill directory, `references/`, and `agents/openai.yaml` are created.

- [ ] **Step 2: Replace the generated `SKILL.md` with the minimal trigger and control flow**

Use this frontmatter contract:

```yaml
---
name: tiktok-account-audit
description: Use when a user provides a TikTok profile or account link and asks for account analysis, competitor research, content performance, commerce performance, operational diagnosis, or growth opportunities.
---
```

The body must require loading `references/workflow.md` and `references/report-template.md`, validate that the input is a profile URL, prefer the two account MCP tools, use browser fallback, enforce the completeness gate, and present results before evidence.

- [ ] **Step 3: Write the detailed workflow reference**

Include exact sections for: input normalization; tool availability discovery; `creator_profile`; `creator_videos`; pagination to at most 50 recent public videos by default; public-browser fallback; source ledger; completeness gate; content/commerce/brand/mixed classification; representative video selection; `caption_extract`; metric calculations; missing-data rules; safety; and stop conditions.

Define engagement rate only when the denominator exists:

```text
(likes + comments + shares) / views
```

Never compare rates computed from incompatible denominators.

- [ ] **Step 4: Write the account report template**

Use this top-level order:

```text
1. 一句话诊断
2. 立即执行的三个动作
3. 账号类型与阶段
4. 优势与主要问题
5. 数据范围、来源和可信度
6. 内容支柱与发布节奏
7. 爆款/低表现视频对比
8. Hook、结构、人设、卖点与 CTA
9. 商品和带货表现（仅在有数据时）
10. 可复用内容公式
11. 未来 7 天行动
12. 缺失信息与影响
```

- [ ] **Step 5: Add optional account tools to the shared MCP reference**

Add a clearly labeled section stating that `creator_profile` and `creator_videos` are optional capabilities whose actual parameters must be read from the connected MCP schema. Document the expected semantic outputs from the design spec, the availability check, and the browser fallback. Do not assign endpoint paths or parameter names not present in actual MCP documentation.

- [ ] **Step 6: Validate the Skill**

Run:

```bash
python3 /Users/hy/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/tiktok-account-audit
rg -n "creator_profile|creator_videos|browser|浏览器|one.*question|一个.*问题|可信度|结果前置" skills/tiktok-account-audit skills/tiktok-shop-operator/references/mcp-tools.md
```

Expected: `Skill is valid!` and all required concepts are present.

- [ ] **Step 7: Commit**

```bash
git add skills/tiktok-account-audit skills/tiktok-shop-operator/references/mcp-tools.md
git commit -m "feat: add TikTok account audit skill"
```

### Task 3: Create `tiktok-category-strategy`

**Files:**

- Create: `skills/tiktok-category-strategy/SKILL.md`
- Create: `skills/tiktok-category-strategy/agents/openai.yaml`
- Create: `skills/tiktok-category-strategy/references/workflow.md`
- Create: `skills/tiktok-category-strategy/references/report-template.md`

**Interfaces:**

- Consumes: category and target market; optional price range, account type, objective, budget, language, and planning period.
- Produces: go/no-go conclusion with conditions, opportunity map, product/shop/video/creator evidence, content matrix, differentiation, 30-day plan, and risks.

- [ ] **Step 1: Scaffold the Skill**

Run:

```bash
python3 /Users/hy/.codex/skills/.system/skill-creator/scripts/init_skill.py tiktok-category-strategy --path skills --resources references --interface 'display_name=TikTok 类目策略' --interface 'short_description=研究目标类目、竞品、商品、内容和达人机会' --interface 'default_prompt=使用 $tiktok-category-strategy 研究这个类目并生成 30 天运营方案。'
```

- [ ] **Step 2: Replace `SKILL.md` with the minimal trigger and control flow**

Use this frontmatter contract:

```yaml
---
name: tiktok-category-strategy
description: Use when a user wants to evaluate, enter, position, or operate a TikTok or TikTok Shop category and needs market, product, shop, content, creator, competition, or category strategy.
---
```

Require target category and market, load both references, reuse KSS MCP tool facts from `tiktok-shop-operator`, and present conclusions before evidence.

- [ ] **Step 3: Write the category workflow reference**

Include exact sections for: input gate; category normalization; `product_search`; `shop_search`; `video_search`; `creator_search`; `caption_extract`; pagination and quota limits; TikTok Creative Center official trend supplement; cross-source validation; opportunity scoring without invented benchmarks; content pillars; differentiation; policy checks; browser limitations; and stop conditions.

- [ ] **Step 4: Write the category report template**

Use this top-level order:

```text
1. 是否建议进入及适用条件
2. 最优切入方向与三个优先动作
3. 数据口径、来源和可信度
4. 商品机会与价格带
5. 代表店铺和竞争强度
6. 爆款内容模式
7. 达人地图
8. 用户痛点与购买理由
9. 内容支柱和选题矩阵
10. 差异化定位
11. 30 天运营计划
12. 风险与缺失信息
```

- [ ] **Step 5: Validate and commit**

Run:

```bash
python3 /Users/hy/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/tiktok-category-strategy
rg -n "product_search|shop_search|video_search|creator_search|caption_extract|Creative Center|目标.*地区|国家|反问|结果前置" skills/tiktok-category-strategy
```

Expected: validation passes and required workflow concepts are present.

Commit:

```bash
git add skills/tiktok-category-strategy
git commit -m "feat: add TikTok category strategy skill"
```

### Task 4: Create `tiktok-growth-plan`

**Files:**

- Create: `skills/tiktok-growth-plan/SKILL.md`
- Create: `skills/tiktok-growth-plan/agents/openai.yaml`
- Create: `skills/tiktok-growth-plan/references/workflow.md`
- Create: `skills/tiktok-growth-plan/references/report-template.md`

**Interfaces:**

- Consumes: account URL, category, market, and business objective; optional budget, team capacity, inventory, available time, and benchmarks.
- Produces: account/category fit decision, gap matrix, keep/stop/start actions, three growth paths, 30-day calendar, metrics, and copy-ready next AI prompts.

- [ ] **Step 1: Scaffold the Skill**

Run:

```bash
python3 /Users/hy/.codex/skills/.system/skill-creator/scripts/init_skill.py tiktok-growth-plan --path skills --resources references --interface 'display_name=TikTok 增长计划' --interface 'short_description=匹配账号能力与类目机会，生成 30 天增长行动计划' --interface 'default_prompt=使用 $tiktok-growth-plan 分析我的账号与目标类目并制定 30 天增长计划。'
```

- [ ] **Step 2: Replace `SKILL.md` with the orchestration trigger and rules**

Use this frontmatter contract:

```yaml
---
name: tiktok-growth-plan
description: Use when a user provides or discusses both a TikTok account and a target category and wants fit analysis, repositioning, transition strategy, growth planning, or an executable operating roadmap.
---
```

Require loading both references and reusing the account-audit and category-strategy workflows instead of duplicating their detailed tool instructions.

- [ ] **Step 3: Write the growth workflow reference**

Define: required-input gate; account-audit handoff; category-strategy handoff; evidence alignment; fit assessment; gap matrix; keep/stop/start decisions; product/content/creator growth paths; resource-aware scheduling; 30-day calendar; KPI selection; review cadence; next-prompt generation; partial-data handling; and stop conditions.

- [ ] **Step 4: Write the growth report template**

Use this top-level order:

```text
1. 一句话运营定位
2. 是否建议进入该类目
3. 三个立即执行动作
4. 账号与类目适配证据
5. 保留、停止和新增
6. 选品、视频和达人增长路径
7. 每周内容数量与比例
8. 30 天行动日历
9. KPI、复盘与调整条件
10. 可直接复制给 AI 的下一步任务
11. 数据范围、可信度和缺失信息
```

- [ ] **Step 5: Validate and commit**

Run:

```bash
python3 /Users/hy/.codex/skills/.system/skill-creator/scripts/quick_validate.py skills/tiktok-growth-plan
rg -n "tiktok-account-audit|tiktok-category-strategy|账号|类目|适配|30 天|反问|可信度|结果前置" skills/tiktok-growth-plan
```

Expected: validation passes and both upstream workflows are referenced.

Commit:

```bash
git add skills/tiktok-growth-plan
git commit -m "feat: add TikTok growth planning skill"
```

### Task 5: Update the Chinese-First Project Introduction

**Files:**

- Modify: `README.md`

**Interfaces:**

- Consumes: all four available repository Skills and the two-layer account-data design.
- Produces: an accurate public project introduction and copy-ready examples.

- [ ] **Step 1: Update the top-level value proposition**

State that the repository now supports two complementary loops:

```text
商品运营：选品 → 店铺 → 视频 → 达人 → 字幕 → 行动方案
账号增长：账号链接 → 账号诊断 → 类目研究 → 差距匹配 → 30 天计划
```

- [ ] **Step 2: Add a Skill catalog**

List `tiktok-shop-operator`, `tiktok-account-audit`, `tiktok-category-strategy`, and `tiktok-growth-plan`, with one sentence describing when to use each.

- [ ] **Step 3: Document the data hierarchy and limitations**

Explain MCP-first, browser-fallback, A/B/C confidence, public-only access, missing-data questions, and the fact that actual availability of `creator_profile` and `creator_videos` depends on the connected KSS MCP schema.

- [ ] **Step 4: Add copy-ready prompts**

Include at least these examples:

```text
分析这个公开 TikTok 竞争对手账号，找出它的内容支柱、爆款公式和未来 7 天可以改进的动作：<账号链接>

研究美国区宠物用品类目，分析商品、店铺、爆款视频和达人，并生成 30 天运营方案。

分析这个账号是否适合做美国区家居类目，并生成从定位到选品、内容和达人合作的 30 天计划：<账号链接>
```

- [ ] **Step 5: Verify and commit**

Run:

```bash
rg -n "tiktok-account-audit|tiktok-category-strategy|tiktok-growth-plan|creator_profile|creator_videos|浏览器|可信度|30 天" README.md
```

Expected: all new Skills and key data rules are discoverable.

Commit:

```bash
git add README.md
git commit -m "docs: introduce account and category skills"
```

### Task 6: Run GREEN Pressure Tests and Final Validation

**Files:**

- Modify: `docs/superpowers/validation/2026-07-20-skill-pressure-tests.md`

**Interfaces:**

- Consumes: completed Skills, report templates, MCP reference, and README.
- Produces: repeatable evidence that the Skills changed agent behavior and a clean repository ready for user review.

- [ ] **Step 1: Re-run all three baseline prompts with the relevant new Skill loaded**

Use the exact prompts from Task 1. For each response, record compliance with browser fallback, missing-market questioning, mixed-account classification, partial-report labeling, and results-first output.

- [ ] **Step 2: Close GREEN checklists**

Mark a scenario GREEN only when every required behavior is present. If an agent rationalizes around a requirement, tighten the relevant `SKILL.md` or workflow reference and rerun that scenario.

- [ ] **Step 3: Run official validation on all Skills**

Run:

```bash
for skill in skills/*; do python3 /Users/hy/.codex/skills/.system/skill-creator/scripts/quick_validate.py "$skill"; done
```

Expected: `Skill is valid!` four times.

- [ ] **Step 4: Scan for placeholders, secrets, and formatting errors**

Run:

```bash
rg -n "TO[D]O|TB[D]|\[TO[D]O|ghp_|sk-[A-Za-z0-9]|api[_-]?key\s*[:=]\s*['\"]?[A-Za-z0-9]" README.md skills docs --glob '!docs/superpowers/plans/*.md'
git diff --check
```

Expected: no placeholder or credential matches and no whitespace errors.

- [ ] **Step 5: Check final scope**

Run:

```bash
git status --short
git diff --stat HEAD~5..HEAD
git log --oneline -8
```

Expected: only planned repository files changed; commits are scoped by Skill or documentation concern.

- [ ] **Step 6: Commit validation evidence**

```bash
git add docs/superpowers/validation/2026-07-20-skill-pressure-tests.md
git commit -m "test: verify TikTok skill workflows"
```

Do not push to GitHub until the user explicitly confirms the local result and requests publication.
