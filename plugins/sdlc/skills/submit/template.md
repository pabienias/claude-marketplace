# MR/PR Description

A starting point. Add, drop or adapt sections to fit the scope. Always keep Overview, Key changes and Worth testing. Write other sections only when they have content. Short bullets. No code.

Title: the main commit subject, in the project's commit convention.

```md
## Overview
<one sentence on the goal; two or three only if the scope is complex>

## Ticket
<verified link>

## Key changes
- <area>: <what changed> (`path/or/Class`)
- <area>: <what changed> (`path/or/Class`) - review focus: <why>

## Deviations
- <planned> → <done> - <reason>

## Decisions
- <decision a reviewer may question, e.g. new pattern, refactor, restructure> - <reason>

## Worth testing
- <concrete scenario, including edge cases and failure paths; setup if needed>

## Impact
- <breaking change, migration, config or env change, feature flag, deploy order, dependent MR/PR, follow-up>

## References
- <screenshot, doc, spec, related MR/PR or code>: <link>
```

For a change with no behaviour change, Worth testing is one line: no behaviour change, and how it was verified.
