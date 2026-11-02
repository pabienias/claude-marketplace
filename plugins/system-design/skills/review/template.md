# Design Review

Structure of `review.md`. Keep every section. Write `none` if empty. Short bullets. Cap: 80 lines.

Decision values: `open`, `amend`, `no action`. Outcome values: `open`, `amended`, `accepted risk`, `no action`. Ids never change; a next round continues the numbering. Status values match the document status line of `design.md`.

```md
# Design Review: <system>

Round <n>, reviewed <date>. Status: changes requested | approved | approved with accepted risks.

## Done-when check
- <charter line> - met | not met - <why>

## Findings
| Id | Severity | Section | Finding | Evidence | Amendment | Owner | Decision | Outcome |
|---|---|---|---|---|---|---|---|---|
| F1 | blocker | <section> | <what is wrong> | "<quoted line>" | <amendment> | brief, requirements or architecture | open | open |

## Skipped
- <finding> - explained by D<n>

## Coverage
- <core operation or now feature> - covered by <design element> | not covered
- <quality scenario> - met by <design element> | at risk - <why>

## Re-check
- F<n> - resolved | still open - <why>

## Accepted risks
- F<n> -> D<m>

## Not verifiable
- <item> - <why>
```
