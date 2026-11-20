# Implementation Plan

Structure of `implementation-plan.md`. Keep every section. Write `none` if empty. Use short bullets and plain language. No code. Paths only if confirmed to exist (mark new files as `new`).

```md
# Implementation Plan: <feature name> (<ticket key if any>)

Status: draft | approved | approved (auto)

## Overview
- <2-3 bullets: what is built and why>

## Scope
- In scope: <list>
- Out of scope: <list>

## Planning Basis
- Discovery status: Ready for planning | Partially ready
- Source: `context-discovery.md`
- Guidelines to follow: <paths or "none">

## Core changes
- <public contract, integration with another system or dependency, data or schema change>

## Assumptions
- <assumption> (confirmed: yes/no/auto)

## Risks
- <risk> - <what to do about it>

## Suggested Branch Name
- `<type>/<slug>`

## Phases

### Phase N: <title>

<1-2 sentences: what the user or system gains after this phase.>

**Steps:**
- [ ] <small, reviewable change in business language> - Affected area: <module, layer, area or confirmed path>
- [ ] ...

**Depends on:** <phase or step refs, or none>

**Tests:** <kinds and level expected: unit, integration, e2e, manual>

**Evidence:** checks | runtime: <what to observe in the running system, each state>

**Acceptance criteria:**
- [ ] <testable, specific criterion>

## Open Questions
- <question> - <owner or "deferred">
```
