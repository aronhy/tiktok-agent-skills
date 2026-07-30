# TikTok Account Audit Operations Playbook Design

## Goal

Extend `tiktok-account-audit` so a profile audit can explicitly deliver:

1. evidence-backed operating-logic analysis;
2. an inferred, reusable account-launch process; and
3. a practical commerce guide.

Keep observed facts separate from inferred playbooks and preserve metric-window integrity.

## Scope

Update only:

- `skills/tiktok-account-audit/SKILL.md`
- `skills/tiktok-account-audit/references/workflow.md`
- `skills/tiktok-account-audit/references/report-template.md`
- `skills/tiktok-account-audit/agents/openai.yaml`

Do not change the other TikTok skills, add API wrappers, or modify MCP schemas.

## Behavior

### Identity resolution

When visible link text or a supplied nickname conflicts with a valid canonical profile URL, treat the normalized URL Handle as the target. Record and disclose the conflict; never merge the two identities.

### Data collection

Continue to prefer `creator_profile` and `creator_videos`, with public-browser fallback when those capabilities are unavailable or insufficient.

Allow `creator_search` only as aggregate enrichment after the target creator ID or Handle has been cross-checked against a verified profile or video source. Do not treat fuzzy name search as identity confirmation.

### Sampling

Record pinned posts separately. Exclude pinned posts from recent-content distribution statistics unless their publication dates place them inside an explicitly defined chronological window. Keep pinned posts available as evidence of profile packaging and historical winners.

### Analysis boundaries

Label operating logic as an evidence-backed interpretation derived from repeated patterns across the visible sample.

Label the launch process and commerce guide as a reusable inferred playbook, not a reconstruction of the creator's undocumented history. Tie each recommendation to observed evidence, an explicit assumption, or a validation step.

### Metric integrity

Keep account aggregates, lifetime/cumulative video metrics, and time-windowed commerce metrics separate. If a creator-level sales field has no documented window, report the window as unspecified. Never derive conversion rate by mixing cumulative views with recent sales.

## Output

Add dedicated report sections for:

- operating logic;
- reusable launch process; and
- commerce guide.

Preserve the current result-first ordering, source ledger, missing-data disclosure, and A/B/C confidence scheme.

## Validation

Run the Skill Creator `quick_validate.py` validator on the updated skill folder. Run the repository's existing TikTok skill validation tests if available. Confirm `agents/openai.yaml` remains aligned with the expanded trigger and output.
