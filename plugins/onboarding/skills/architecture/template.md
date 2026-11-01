# Architecture

Structure of `architecture.md`. Keep every section. Write `none` if empty. Short bullets. Every fact cites `[[n]](#references)`. `(inferred)` on every claim whose evidence is names or structure. Cap: 150 lines.

````md
# Architecture

Generated from commit <sha> on <date>.

## Style
- System role: <whole system | one service of a larger system | library | frontend for a separate backend | infrastructure | monorepo of several deployables | unclear> (confidence: high / medium / low) - <evidence, one line> [[n]](#references)
- Internal structure: <unit>: <by layer | by feature | flat | mixed | unclear> (confidence) - <evidence, one line> [[n]](#references)

## Units and boundaries
- <unit> -> <unit> - <why it depends> [[n]](#references)
- Depends on other systems: <system> - <signal: environment name, client, topic, contract, deployment> [[n]](#references)
- Shared code: <paths> [[n]](#references)

## Module map
| Product area | Where | Start here |
|---|---|---|
| <area from product-brief.md> | <paths> | <1-3 paths> |

## How a feature is added
- <observed pattern, one line, from 2+ examples> [[n]](#references)

Observed, not recommended.

## Cross-cutting
- <auth | persistence | messaging | config | observability | errors> - <where> [[n]](#references)

## Deployment
- <deployable> -> <target> [[n]](#references)

## Diagram

```mermaid
flowchart LR
  %% Container level: units, data stores, queues, external systems, main edges. At most 12 nodes. Labels are names only.
```

## References
- [1] <type: doc | link | file | user answer> - <`path`, URL or "User, chat"> (mark unreadable ones)
````
