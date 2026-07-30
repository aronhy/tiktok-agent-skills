# TikTok Account Audit Operations Playbook Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend `tiktok-account-audit` with evidence-bounded operating-logic analysis, a reusable inferred launch process, and a practical commerce guide.

**Architecture:** Keep orchestration and trigger rules in `SKILL.md`, add deterministic evidence and sampling rules to `references/workflow.md`, and extend the fixed output contract in `references/report-template.md`. Keep UI metadata aligned through `agents/openai.yaml`; do not change MCP schemas or other TikTok skills.

**Tech Stack:** Markdown Agent Skills, YAML interface metadata, Skill Creator `quick_validate.py`, shell assertions.

## Global Constraints

- Update only the four files named in the approved design.
- Treat the normalized canonical profile URL as identity authority when adjacent label text conflicts.
- Use `creator_search` only as aggregate enrichment after verified identity cross-checking.
- Separate pinned posts from recent-content distribution statistics.
- Label launch and commerce guidance as inferred reusable playbooks, not undocumented creator history.
- Never mix account aggregates, cumulative views, and time-windowed sales to derive conversion.

---

### Task 1: Extend the account-audit contract

**Files:**

- Modify: `skills/tiktok-account-audit/SKILL.md`
- Modify: `skills/tiktok-account-audit/references/workflow.md`
- Modify: `skills/tiktok-account-audit/references/report-template.md`
- Modify: `skills/tiktok-account-audit/agents/openai.yaml`

**Interfaces:**

- Consumes: canonical profile URL, optional adjacent label/nickname, optional account MCP results, public browser observations, optional identity-verified `creator_search` aggregates.
- Produces: a result-first audit with operating logic, reusable launch process, commerce guide, source ledger, missing-data states, and A/B/C confidence.

- [ ] **Step 1: Verify the new contract is absent**

Run:

```bash
rg -n "可复用起号流程|带货指南|置顶.*近期|creator_search.*补充" \
  skills/tiktok-account-audit/SKILL.md \
  skills/tiktok-account-audit/references/workflow.md \
  skills/tiktok-account-audit/references/report-template.md
```

Expected before implementation: exit `1` or incomplete matches that do not cover all four required behaviors.

- [ ] **Step 2: Update trigger, orchestration, and UI metadata**

In `SKILL.md`, expand the description to trigger on operating-logic research, launch-process reverse engineering, and commerce guidance. Add control-flow requirements that:

- disclose label/URL identity conflicts and use the normalized URL Handle;
- allow identity-verified `creator_search` aggregate enrichment;
- separate pinned and recent samples;
- distinguish observed operating patterns from inferred launch and commerce guidance.

Update `agents/openai.yaml` so its display description and default prompt mention operating logic, reusable launch process, and commerce guidance.

- [ ] **Step 3: Add deterministic workflow rules**

In `references/workflow.md`:

- add adjacent-label conflict handling to input normalization;
- add a `creator_search` enrichment step after browser/account-tool collection, requiring Handle or creator-ID cross-checking and forbidding fuzzy-name identity confirmation;
- record pinned status during sampling and exclude pinned items from recent distributions unless an explicit chronological window includes them;
- add an analysis section requiring repeated evidence for operating logic and explicit inference labels for launch/commerce playbooks;
- require unspecified windows to remain unspecified and prohibit cumulative-view/recent-sales conversion calculations.

- [ ] **Step 4: Extend the report template**

Add dedicated sections after evidence and commerce performance for:

1. 运营逻辑;
2. 可复用起号流程（明确不是历史事实复原）;
3. 带货指南.

Renumber later sections while preserving result-first ordering, source ledger, missing-data disclosure, and the seven-day action table.

- [ ] **Step 5: Run focused contract assertions**

Run:

```bash
rg -n "链接文字|creator_search|置顶|可复用起号流程|不是.*历史|带货指南|累计.*近 30 天|转化率" \
  skills/tiktok-account-audit/SKILL.md \
  skills/tiktok-account-audit/references/workflow.md \
  skills/tiktok-account-audit/references/report-template.md \
  skills/tiktok-account-audit/agents/openai.yaml
```

Expected: every required behavior appears in the owning file, with no conflicting instruction.

- [ ] **Step 6: Validate the Skill and repository diff**

Run:

```bash
PYTHONPATH=.validation-deps python3 \
  /Users/hy/.codex/skills/.system/skill-creator/scripts/quick_validate.py \
  skills/tiktok-account-audit

git diff --check
git status --short
```

Expected: `Skill is valid!`, `git diff --check` exits `0`, and status contains only the four planned Skill files plus this implementation-plan commit history.

- [ ] **Step 7: Commit the implementation**

```bash
git add \
  skills/tiktok-account-audit/SKILL.md \
  skills/tiktok-account-audit/references/workflow.md \
  skills/tiktok-account-audit/references/report-template.md \
  skills/tiktok-account-audit/agents/openai.yaml
git commit -m "feat: add account audit operations playbook"
```
