---
name: auto
description: Runs the whole sdlc flow for one task with few approvals - context discovery, plan, implement, review and the fix loop - and ends on a draft MR/PR. A written policy replaces most approvals. The human answers discovery questions and the escalations the policy lists.
when_to_use: Use only when the user explicitly asks to run the whole sdlc flow automatically or end to end, or to resume such a run. Do not use for a single stage; use that stage skill instead.
argument-hint: "[ticket key | file path | task text | feature name to resume] [extra context, links, files]"
allowed-tools: Read, Glob, Grep, Agent, Skill, AskUserQuestion, ToolSearch, WebFetch, Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git branch:*)
disable-model-invocation: true
---

# Auto

Run the stage skills in order for one task. Stop on a draft MR/PR. You are the orchestrator: you start the stages, apply the policy and keep the run log.

Do not do the work of a stage yourself. Do not edit source files. Do not merge.

## Preconditions

- The project is a git repository. If not, stop.
- A task is given, or a run to resume exists. If neither, ask for the task.

## Inputs

Arguments: $ARGUMENTS

1. Read `${CLAUDE_PLUGIN_ROOT}/skills/auto/policy.md`. It decides every approval in this run.
2. Find the run directory: `.ai/dev/<name>/` for the ticket key, the given name or the current branch.
3. If `auto-run.md` or a stage artifact exists there, see Resume. Otherwise this is a new run.

## Stages

Invoke each stage with the `Skill` tool. Pass `--auto` first, then the name and the user's arguments. Go on only when the exit condition holds.

| # | Stage | Exit condition | Otherwise |
|---|---|---|---|
| 1 | `sdlc:context-discovery` | `context-discovery.md` has status `Ready for planning` or `Partially ready` | stop: `Blocked` |
| 2 | `sdlc:plan` | `implementation-plan.md` has status `approved` or `approved (auto)` | stop: `Blocked` |
| 3 | `sdlc:implement` | `implementation-summary.md` has status `done` | stop: `Blocked` |
| 4 | `sdlc:review` | review status `approved` or `approved with follow-ups` | `changes requested`: run stage 3 on `implementation-review.md`, then stage 4 again |
| 5 | `sdlc:submit` | a draft MR/PR exists | stop: `Waiting`, with the final text |

- Create `auto-run.md` from `${CLAUDE_PLUGIN_ROOT}/skills/auto/template.md` after stage 1 fixed the directory name. Until then keep the decisions in memory.
- Update `Stage` and `State` in `auto-run.md` when a stage starts and ends.
- Between stages, say in one line what finished and what starts.
- A stage that suggests a next step does not decide it. This table does.

## Escalations

- A stage asks the human only what the policy lists for it. Record the question and the answer in `auto-run.md`.
- While a question is open, set `State: waiting`.
- If the human says stop, record it under Stops and finish with the message below.

## Limits

- Review rounds: 3. After that the policy escalates.
- If the same stage fails its exit condition twice with no change in its artifact, stop: `Blocked`.

## Resume

1. Read `auto-run.md` and the stage artifacts in the run directory. If `auto-run.md` is missing, create it and note that the earlier stages ran outside this run.
2. Show in a few lines: the stage reached, the state and the last stop reason.
3. Continue from the first stage whose exit condition does not hold. Do not repeat a finished stage.
4. With review status `changes requested`: run stage 3 on `implementation-review.md` if a fix batch is not `[done]`, else stage 4.

## Finish

Set `Stage` and `State` in `auto-run.md`. Then send one message in this form:

```
State: Ready | Waiting | Blocked
MR/PR: <link, or none>
Evidence: <level from the review>

Try it:
- <step the human can do to check the result>

Decided for you:
- <auto decision worth knowing>

Deferred:
- <id> - <item>

Needs you: (only when Waiting or Blocked)
- <what, and how to resume>
```

- `Ready`: the draft MR/PR exists and the review is approved.
- `Waiting`: the run needs an answer or a manual step.
- `Blocked`: a stage cannot meet its exit condition.
- Take `Try it` from the plan acceptance criteria. Add updating the project documentation when the change affects it.

## Rules

- The policy file is the only source of auto decisions. Do not add rules of your own.
- Keep your own context small. Read the status lines and summaries of the artifacts, not whole agent reports.
- Every question goes through `AskUserQuestion`.
- Short messages. Facts only. No narration.
