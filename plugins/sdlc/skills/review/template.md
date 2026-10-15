# Implementation Review

Structure of `implementation-review.md`. Keep every section. Write `none` if empty. One line per item. Facts and `path:line`. No code.

```md
# Implementation Review: <feature name> (<ticket key if any>)

- Plan: `implementation-plan.md`
- Summary: `implementation-summary.md`
- Branch: `<branch>` against `<base>`
- Reviewed at: `<commit>`
- Round: <n>
- Verification: `<command>` - pass | fail
- Status: blocked | changes requested | approved | approved with follow-ups

## Requirements
| Id | Requirement | Source | Result | Evidence | Decision | Status |
|---|---|---|---|---|---|---|
| R1 | <requirement or acceptance criterion> | <discovery, plan, ticket or reference> | met \| partial \| missing \| differs \| deviates (<decision>) | `path:line` or gap | - \| open \| fix \| defer \| owner <who> | - \| open \| fixed `<commit>` |

## Findings
| Id | Area | Finding | Evidence | Proposed fix | Decision | Status |
|---|---|---|---|---|---|---|
| H1 | <area> | <one line> | `path:line` | <one line>; `incompatible: <convention>` when it conflicts | open \| fix \| no action \| defer \| owner <who> | open \| fixed `<commit>` \| rejected |

Ids: `H` high, `M` medium, `L` low. Ids never change. New items continue the numbering.

## Fix batches

### Batch N: <title>

Closes: <ids>

**Steps:**
- [ ] <change> - Affected area: <module, area or confirmed path>

**Tests:** <kinds and level expected>

**Acceptance criteria:**
- [ ] <testable, specific criterion>

## Follow-ups
- <id> - <deferred item or owner decision> - <owner>

## Rounds
- Round <n> `<date>` at `<commit>`: <opened> opened, <closed> closed
```
