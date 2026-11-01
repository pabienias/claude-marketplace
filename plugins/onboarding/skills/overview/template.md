# Overview and Tech Stack

Structure of `overview.md` and `tech-stack.md`. Keep every section. Write `none` if empty. Short bullets. Every fact cites `[[n]](#references)`. Cap: 100 lines each.

```md
# Overview

Generated from commit <sha> on <date>.

## What it is
- <1-3 bullets: purpose, users. From docs or the user only> [[n]](#references)

## System boundary
- <whole system | part of a larger system: other systems named in docs or by the user> [[n]](#references)

## Units
| Unit | Path | Kind | Stack |
|---|---|---|---|
| <name> | <path> | <service / app / library / infra / docs> | <language, framework, one line> [[n]](#references) |

## Where things are
- <unit>: entry <paths>; api <paths>; models <paths>; config <paths>; tests <paths>; docs <paths>

## Documentation
| Doc | Covers | Relevant to |
|---|---|---|
| <path or URL> | <one line> | <product / tech / architecture / process> |

Unreadable links are listed with the reason.

## Read next
- product-brief.md, glossary.md and architecture.md (run `onboarding:product` and `onboarding:architecture` if missing), tech-stack.md, open-questions.md

## References
- [1] <type: doc | link | file | user answer> - <`path`, URL or "User, chat"> (mark unreadable ones)
```

```md
# Tech Stack

Generated from commit <sha> on <date>.

## Stack by layer
- <unit, one block per unit when they differ>
  - UI: <name - role> [[n]](#references)
  - API: <name - role> [[n]](#references)
  - Data: <name - role> [[n]](#references)
  - Messaging: <name - role> [[n]](#references)

Versions only when pinned in a manifest.

## Infrastructure
- <container | orchestration | IaC | deploy target> - <what> [[n]](#references)

## Tooling
- <build | test | lint | CI | runtime pin> - <what> [[n]](#references)

## Environment
- <VARIABLE_NAME> - <what it hints at> [[n]](#references)

## References
- [1] <type: file | doc> - <`path`>
```
