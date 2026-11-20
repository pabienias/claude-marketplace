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
| `sdlc:plan` | skill | Reads `context-discovery.md`, honours its readiness status, runs a short Q&A, writes `.ai/dev/<name>/implementation-plan.md` as a draft, shows a short summary and marks the plan approved on approval. Does not code. |
| `sdlc:implement` | skill | Reads `implementation-plan.md` (refuses a `draft` plan), or the fix batches of `implementation-review.md`, runs a readiness gate and a short Q&A, implements phase by phase with the `developer` and `verifier` agents, commits one phase at a time after approval, writes `.ai/dev/<name>/implementation-summary.md`. Does not plan or review. |
| `sdlc:review` | skill | Reads the summary, plan and discovery, reviews the finished implementation with the `requirements-reviewer` and `code-reviewer` agents, and the `runtime-verifier` agent when the plan asks for `runtime` evidence, collects decisions per finding, writes `.ai/dev/<name>/implementation-review.md` with fix batches for `implement`. Does not change code. |
| `sdlc:submit` | skill | Drafts the MR/PR title and description from the branch diff and the sdlc artifacts, shows them for approval, then pushes the branch and creates the MR/PR. Platform agnostic. Does not change code. |
| `sdlc:auto` | skill | Runs all stages in order with `--auto` and ends on a draft MR/PR. The policy in `skills/auto/policy.md` replaces most approvals. Keeps a run log in `.ai/dev/<name>/auto-run.md` and can resume. User-invoked only. Does not merge. |
| `sdlc:codebase-context-scanner` | agent | Read-only repository scan used by `context-discovery` and `plan`. |
| `sdlc:runtime-verifier` | agent | Checks the local setup, runs the project locally and observes what the plan phases name as `runtime` evidence. Saves captures to `.ai/dev/<name>/evidence/`. Used by `review` and, for a baseline, by `implement`. Does not edit. |
| `sdlc:developer` | agent | Implements one phase: code and tests, runs the project's verification command. When you answer one of its tool calls directly, it stops and reports with status `feedback` before going on. Used by `implement`. Does not commit. |
| `sdlc:verifier` | agent | Checks one implemented phase: verification command, acceptance criteria, tests, scope. Used by `implement`. Does not edit. |
| `sdlc:requirements-reviewer` | agent | Checks the finished implementation against ticket, discovery, plan criteria, recorded decisions and an optional reference implementation. Used by `review`. Does not edit. |
| `sdlc:code-reviewer` | agent | Runs the verification command, reviews the diff against the project's conventions first, marks fixes that conflict with them as `incompatible` and recorded decisions that conflict with them as `decided`. Used by `review`. Does not edit. |

## Permissions

Skills pre-approve read-only tools only. File writes, branch changes and commits follow your own permission settings and prompt as usual. Subagents inherit those settings. To reduce prompts in `plan`, `implement` and `review`, allow `Edit(.ai/dev/**)` (covers writing new files too) for plan, summary and review files, and `Bash(git add:*)` and `Bash(git commit:*)` for the per-phase commit. `submit` prompts for the push and the MR/PR creation.

For an unattended `sdlc:auto` run, also allow the push and the MR/PR creation command of your platform. The `runtime-verifier` runs the project's own start commands; allow those per project.

## Flow

Manual: `context-discovery` → `plan` → `implement` → `review` → `implement` (fix batches) → `review` … until the review status is `approved` → `submit`.

Auto: `sdlc:auto <task>` runs the same stages. You answer the discovery questions. After that it asks only what `skills/auto/policy.md` lists as an escalation, for example a plan with core changes or a finding that conflicts with a project convention. It stops on a draft MR/PR with the state `Ready`, `Waiting` or `Blocked`.

## Evidence

Each plan phase states its evidence: `checks` (verification command and tests) or `runtime: <what to observe>`. For `runtime`, `review` runs the `runtime-verifier`. The review and the MR/PR description state the level reached: `checks only`, `runtime`, `runtime with captures` or `not available: <reason>`. A missing local setup gives `not available`, not a failed review.

## Models

| Agent | Model |
|---|---|
| `codebase-context-scanner` | `haiku` |
| `developer` | `sonnet`. A new `opus` developer takes over in the second fix round of a phase. |
| `verifier` | `sonnet` |
| `requirements-reviewer`, `code-reviewer` | `sonnet`. `opus` in auto mode. |
| `runtime-verifier` | `sonnet` |
