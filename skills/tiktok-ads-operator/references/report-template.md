# TikTok Ads Report and Execution Ledger Template

Use the following twelve sections in this exact order. Replace bracketed text only with available, scoped information. Do not expose credentials, tokens, personal contact data, or raw customer lists. Every material finding table includes separate `证据类型` and `可信度` columns.

## 1. Objective and three immediate actions

| Objective or action | Owner | Timing | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- |
| [Primary business result and objective family] | [Owner] | [Date or condition] | [Evidence type] | [A/B/C] |
| [Immediate action 1] | [Owner] | [Date or condition] | [Evidence type] | [A/B/C] |
| [Immediate action 2] | [Owner] | [Date or condition] | [Evidence type] | [A/B/C] |
| [Immediate action 3] | [Owner] | [Date or condition] | [Evidence type] | [A/B/C] |

## 2. Mode, inputs, sources, and confidence

| Mode | Confirmed inputs | Missing inputs | Sources and scope | Limitation | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- |
| [Audit, plan, optimization, or approved execution] | [Inputs] | [First blocking input or none] | [Tool/export/screenshot and scope] | [Limitation] | [Evidence type] | [A/B/C] |

## 3. Account and tracking health

| Check | Observed state | Severity | Owner | Next action | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- |
| [Account, Pixel, Events API, App Event, Lead, or Shop attribution check] | [State] | [Severity] | [Owner] | [Action] | [Evidence type] | [A/B/C] |

## 4. Campaign structure

| Campaign | Objective family | Status | Structure finding | Next action | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- |
| [Campaign or proposed campaign] | [Family] | [State] | [Finding] | [Action] | [Evidence type] | [A/B/C] |

## 5. Ad Group, audience, placement, bidding, and budget

| Ad Group | Audience | Placement | Optimization and bid | Budget and currency | Schedule | Finding or action | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Ad Group] | [Audience] | [Placement] | [Validated relationship] | [Amount and currency] | [Schedule] | [Finding or action] | [Evidence type] | [A/B/C] |

## 6. Ad, creative, identity, and Spark Ads

| Ad | Creative | Identity | Spark authorization | Delivery state | Finding or action | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Ad] | [Creative] | [Identity] | [State] | [State] | [Finding or action] | [Evidence type] | [A/B/C] |

## 7. Web/App/Lead/Shop specialized path

| Path | Destination or source | Event or attribution | Specialized check | Limitation or action | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- |
| [Web, App, Lead, or Shop] | [Destination/source] | [Event/attribution] | [Check] | [Limitation/action] | [Evidence type] | [A/B/C] |

## 8. Pixel, Events API, and attribution

| Tracking source | Event definition | Attribution window | Join method | Readiness or gap | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- |
| [Pixel, Events API, App Event, Lead, or Shop] | [Event] | [Window] | [Method] | [State] | [Evidence type] | [A/B/C] |

## 9. KPI, learning state, and creative fatigue

| KPI or signal | Value | Population and data level | Source, timezone, currency, and window | Compatibility finding | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- |
| [Metric, learning state, or fatigue signal] | [Value] | [Population and level] | [Source controls] | [Compatible or limitation] | [Evidence type] | [A/B/C] |

## 10. Scale, adjust, and stop conditions

| Decision | Trigger or test | Guardrail | Owner | Next review | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- | --- |
| [Scale, adjust, stop, or hold] | [Observable trigger] | [Guardrail] | [Owner] | [Date or condition] | [Evidence type] | [A/B/C] |

## 11. Change preview, approval state, and execution result

Change preview: populate before every write. Its approval applies only to the displayed batch.

| operation | advertiser | object/parent | fields | budget/currency | schedule | delivery state | reversible | approval state | evidence type | confidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Logical operation] | [Advertiser] | [Object and parent] | [Changed fields] | [Budget/currency] | [Schedule] | [Expected state] | [Yes/no and recovery path] | [State] | [Evidence type] | [A/B/C] |

Execution ledger: record returned IDs and final states after an approved write. On partial failure, re-read and reconcile before any retry or next approval.

| time | logical operation | actual tool | request summary | returned object ID | final state | retry status | next approval | evidence type | confidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Time] | [Logical operation] | [Actual discovered tool] | [Scoped request] | [Returned ID or none] | [Final/unknown/reconciled] | [Not retried or reconciliation state] | [Required next approval] | [Evidence type] | [A/B/C] |

## 12. Risks, missing information, and next question

| Risk or missing information | Impact | Mitigation or stop rule | Next question | 证据类型 | 可信度 |
| --- | --- | --- | --- | --- | --- |
| [Risk or missing input] | [Impact] | [Mitigation] | [One earliest decision-changing question when blocked] | [Evidence type] | [A/B/C] |

End a blocked full plan or execution report with exactly one earliest missing decision-changing question. A narrow read-only report may ask only for the input required to complete that calculation.
