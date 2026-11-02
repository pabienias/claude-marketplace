# Design Document

Structure of `design.md`. Keep every section, marker and heading. Write `none` if empty. Short bullets.

Every section starts with a marker line, then its heading, then a status line `_Status: not started | draft | approved <date> | stale_`. A section ends where the next marker starts. A skill replaces only the text between its own marker and the next one, and never renames a heading.

| Section | Marker | Owner | Cap |
|---|---|---|---|
| Summary | `summary` | architecture | 20 |
| Charter | `charter` | brief | 15 |
| Context | `context` | brief | 40 |
| Requirements | `requirements` | requirements | 60 |
| Architecture | `architecture` | architecture | 80 |
| Next steps | `next-steps` | architecture | 10 |

Caps exclude Mermaid blocks, marker lines and status lines. Document status, first line under the title: `in progress` when any of Charter, Context, Requirements or Architecture is not approved or is stale; `designed` when all four are approved and no current review exists; after a review, its result. Diagrams: Mermaid, at most 12 nodes each.

````md
# System Design: <system>

Status: in progress | designed | changes requested | approved | approved with accepted risks. Updated <date>.

<!-- section: summary -->
## Summary
_Status: not started_
- Goal: <one line>
- Design: <at most 5 bullets>
- Key decisions: D<n>, D<m>
- Open questions: <count>

<!-- section: charter -->
## Charter
_Status: not started_
- Purpose: <why we design, one line>
- Preset: mvp | full | decision | custom
- Horizon: MVP | v1 | current state | target state
- Audience: <who reads the design>
- Depth:
  - requirements: <preset row>
  - quality scenarios: <preset row>
  - architecture: <preset row>
  - technology: <preset row>
  - review: <preset row>
  - rounds: <n per phase>
- Done when: <one line>
- PRD: <path or link> | none - requirements are design-level, not a product specification
- Existing system: <path to onboarding docs> | none

<!-- section: context -->
## Context
_Status: not started_
### Sources
- <short name> - <path or link> - covers <one line>
### Known
- **<Idea | Scope | Features | Constraints | Technology | Out of scope>**: <fact> (<source>) - one topic per bullet group; Out of scope only when a source states it
### Assumptions
- <item nobody stated> (assumed)
### Unknowns
- <what no source covers>
### Conflicts
- <source A says X, source B says Y> - <resolution | open>

<!-- section: requirements -->
## Requirements
_Status: not started_
### Actors
- <actor> - <what they do>
### Core operations
- <verb noun> - <trigger -> outcome>
### Supportive areas
- <area> - <one line>
### Features
| Feature | Horizon: now, later, out | Note |
|---|---|---|
### Quality scenarios
- <quality>: when <stimulus>, <response> within <measure> (assumed)
### Constraints
- <team | budget | deadline | compliance | existing system | mandated technology> - <constraint>
### Out of scope
- <item> - confirmed by <source | user>
### Context diagram
```mermaid
flowchart LR
  %% Actors, the system, external systems. At most 12 nodes. Labels are names only.
```

<!-- section: architecture -->
## Architecture
_Status: not started_
### Drivers
1. <requirement, scenario or constraint> - <why it shapes the design>
### Options considered
| Option | Axis | Fit with drivers | Main risk | Complexity |
|---|---|---|---|---|
Chosen: <option> - see D<n>
### Design
- <container> - <responsibility> - owns <data>
- Integration: <external system> - <how>
```mermaid
flowchart LR
  %% Chosen option: containers, data stores, external systems, main edges. At most 12 nodes.
```
### Operation paths
```mermaid
sequenceDiagram
  %% One diagram per core operation. At most 12 nodes each.
```
### Cross-cutting
- <auth | observability | failure handling | config> - <approach>
### Technology
| Pick | Alternative | Why |
|---|---|---|
### Deferred
| Item | Horizon | Why safe to defer |
|---|---|---|
### Fit with the existing system
- <only when onboarding docs exist: what is reused, what changes, path from the current state>

<!-- section: next-steps -->
## Next steps
_Status: not started_
- <step> - <who or which skill>
````
