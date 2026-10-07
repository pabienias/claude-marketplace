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
| `sdlc:implement` | skill | Reads `implementation-plan.md`, runs a readiness gate and a short Q&A, implements phase by phase with the `developer` and `verifier` agents, commits one phase at a time after approval, writes `.ai/dev/<name>/implementation-summary.md`. Does not plan or review. |
| `sdlc:codebase-context-scanner` | agent | Read-only repository scan used by `context-discovery` and `plan`. |
| `sdlc:developer` | agent | Implements one phase: code and tests, runs the project's verification command. Used by `implement`. Does not commit. |
| `sdlc:verifier` | agent | Checks one implemented phase: verification command, acceptance criteria, tests, scope. Used by `implement`. Does not edit. |

## Permissions

Skills pre-approve read-only tools only. File writes, branch changes and commits follow your own permission settings and prompt as usual. Subagents inherit those settings. To reduce prompts in `implement`, allow `Edit(.ai/dev/**)` for plan and summary updates, and `Bash(git add:*)` and `Bash(git commit:*)` for the per-phase commit.
