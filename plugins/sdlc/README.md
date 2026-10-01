# sdlc

Tools for the Software Development Lifecycle: planning, implementation, review and delivery.

## Install

```
/plugin marketplace add <path-or-repo-of-this-marketplace>
/plugin install sdlc@pb-claude-marketplace
```

## Contents

| Component | Type | Purpose |
|---|---|---|
| `sdlc:context-discovery` | skill | Gathers task context, sets a planning readiness status, writes `.ai/dev/<name>/context-discovery.md` after approval. Does not plan. |
| `sdlc:plan` | skill | Reads `context-discovery.md`, honours its readiness status, runs a short Q&A, writes `.ai/dev/<name>/implementation-plan.md` after approval. Does not code. |
| `sdlc:codebase-context-scanner` | agent | Read-only repository scan used by `context-discovery` and `plan`. |
