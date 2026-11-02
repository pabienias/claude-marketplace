# Logs

Structure of `decisions.md` and `open-questions.md`. Keep every section. Write `none` if empty. Short bullets.

## decisions.md

Append-only. One entry per significant decision: a choice that is hard to reverse, affects a driver, or resolved a conflict between sources. Entries are written at section approval, so they are `accepted` at birth. A changed decision gets a new entry and the old one becomes `superseded by D<m>`. A blocker the user accepts in review becomes an entry with status `accepted risk`. Entry cap: 10 lines. Ids never change.

```md
# Decisions: <system>

## D<n>: <title>
- Status: accepted | superseded by D<m> | accepted risk
- Context: <one line>
- Decision: <one line>
- Alternatives: <option> - rejected because <reason>
- Consequences: <one or two lines>
```

## open-questions.md

Four sections in this order. Each phase skill overwrites only its own section. A later phase that settles an earlier question appends `[settled in <section>]` to it in place and changes nothing else. Cap: 80 lines.

```md
# Open Questions: <system>

## Brief
### Questions
- <question> - <why it matters> [settled in <section>]
### Risks
- <risk as a fact> - <what it affects>

## Requirements
### Questions
- none
### Risks
- none

## Architecture
### Questions
- none
### Risks
- none

## Review
### Questions
- none
### Risks
- none
```
