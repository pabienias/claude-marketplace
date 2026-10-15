---
name: implement
description: Implements an approved implementation plan phase by phase, or the fix batches of an implementation review. Runs a readiness gate and a short Q&A, then for each phase delegates coding to the developer agent, checks the result with the verifier agent, and commits after user approval. Tracks progress in the source document and writes implementation-summary.md.
when_to_use: Use when the user wants to implement, build or execute an implementation plan, apply the fix batches from sdlc:review, or resume an interrupted implementation. Needs implementation-plan.md from sdlc:plan or implementation-review.md from sdlc:review. Do not use for planning, for ad-hoc changes without a plan, or for code review.
argument-hint: "[path to implementation-plan.md or implementation-review.md | feature name] [branch name, extra notes, links, files]"
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion, WebFetch, Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git branch:*)
disable-model-invocation: true
---

# Implement

Turn an approved plan into committed code, one phase at a time. The phase is the unit of work: it is coded, verified, approved and committed as a whole.

Do not plan, redesign or widen the scope. Do not review beyond the phase's acceptance criteria; that is the next SDLC stage.

## Preconditions

- The project is a git repository. If not, stop.
- A phase source exists: `implementation-plan.md`, or `implementation-review.md` with status `changes requested`. If neither, stop and point to `sdlc:plan`.

## Inputs

Arguments: $ARGUMENTS

1. Find the phase source: the path in the arguments. Else, in `.ai/dev/<name>/` for the given name or the current branch, `implementation-review.md` when its status is `changes requested`, otherwise `implementation-plan.md`. Read it fully. The fix batches of a review are its phases.
2. Read, if present in the same directory: `implementation-plan.md`, `context-discovery.md`, `guidelines/*`, `implementation-summary.md`. The last one means a previous run exists. See Resume.
3. Read every file and link in the plan's `Guidelines to follow` and in the arguments. Record anything unreadable as a question.
4. Do not repeat discovery or planning. Treat the plan's Scope, Phases and Acceptance criteria as given.

## Readiness gate

Check in this order. Collect what fails into questions for the Q&A.

1. **Source.** Every phase or fix batch has steps, acceptance criteria and tests. No phase depends on an Open Question that is not `deferred`, or on an Assumption with `confirmed: no`.
2. **Working tree.** It is clean. If not, ask right away: commit, stash or stop.
3. **Verification command.** Find how the project verifies changes. Look in project docs (`CLAUDE.md`, README, contributing guide), then the manifest or build config (e.g. `package.json`, `Makefile`), then CI config. Prefer one command that runs everything. Never invent or narrow one. If none exists, ask.
4. **Commit convention.** Detect it from repository config or docs, else from recent `git log`. Default: `<type>(<scope>): <summary>`.
5. **Project skills.** Note installed skills that describe coding, testing or verification conventions for this project. Ignore workflow skills.
6. **Branch.** The one given in the arguments, else the plan's `Suggested Branch Name`.

## Q&A and setup

- Ask only what blocks implementation. Do not re-ask what the plan decided.
- For a review source, the review's decisions are given. Ask only if a batch is unclear.
- Ask with `AskUserQuestion`. Max 4 questions per round. Give each a recommended answer with a short reason.
- Ask about behaviour, data, interfaces and test expectations. Decide naming, file layout and local structure yourself, following repository conventions.
- If nothing blocks, say so in one line and go on. Do not ask permission to skip.
- Show the setup: phase count, branch, verification command, commit convention, project skills, decisions. Wait for approval.
- After approval: check out the branch, or create it from the current one. For a plan source, create `implementation-summary.md` next to the plan from `${CLAUDE_PLUGIN_ROOT}/skills/implement/template.md` and write the decisions into it. For a review source, reuse the existing summary.

## Phase loop

Run phases in plan order. Respect `Depends on`. For each phase:

1. **Start.** Mark the phase `[in progress]`. Show its title, gain, steps, acceptance criteria and tests. Ask: start, or add notes first.
2. **Develop.** Delegate to the `developer` agent. Pass: the phase text, the decisions, the paths to guidelines, discovery and extra context, the project skills, and the verification command. Use one developer agent per phase. Continue the same agent for fixes and pass it the findings.
   - On `blocked`: show the reason and the proposed alternative. Ask the user to decide. Record the decision in the summary. Continue the agent with it.
3. **Verify.** Delegate to the `verifier` agent. Pass: the phase text, the verification command, the guideline paths, the project skills and the decisions. Do not pass the developer's report.
   - On `fail`: send the findings to the developer. Max 2 fix rounds, then ask the user.
4. **Approve.** Show the developer's report, the verifier's report and the proposed commit message. Ask: approve and commit, request changes, or stop.
   - On request changes: send the feedback to the developer. Go back to Verify.
   - With a review source the user may also choose `auto`: commit the remaining batches without asking while the verifier passes. A `fail` after the fix rounds or a `blocked` report still asks.
5. **Commit.** Stage the files from the developer's report. Commit with the approved message. One commit per phase.
6. **Record.** Tick the steps and mark the phase `[done]`. Update the summary: status, commit, deviations, follow-ups. For a review source, also set the status of every item the batch closes to `fixed <commit>` in `implementation-review.md`, and record the batch under Phases in the summary as `Review <round> / Batch N`.

## Progress markers

Only these change in the plan or review file:

- Phase or batch heading suffix: `### Phase N: <title> [in progress]`, then `[done]`.
- Steps: `- [ ]` to `- [x]`.
- In the review file also: the `Status` of a closed item to `fixed <commit>`.

## Resume

If the plan has markers or the summary exists:

1. Show what is done and where it stopped.
2. If a phase is `[in progress]`, run `git status`. With uncommitted changes, ask: continue the phase with them, discard them, or stop. Tell the developer that partial changes exist.
3. Continue the loop from the first phase not `[done]`.

## Completion

1. Set the summary status to `done`. Complete Follow-ups.
2. Show the summary in a few lines.
3. Suggest the next step: `sdlc:review`. For a review source, say to run `sdlc:review` again.

## Rules

- No assumptions. A gap in the plan or a `blocked` report goes to the user. Never resolve it silently.
- Never edit source files yourself. Code changes go only through the `developer` agent. You edit only the plan or review markers, item statuses and the summary.
- Stay in scope. Only the current phase. Anything else noticed goes to Follow-ups.
- Technology agnostic. Follow the guidelines the plan names and the patterns in the repository.
- Verification is the project's own command, run verbatim by the agents.
- Every question and approval goes through `AskUserQuestion`.
- Short messages. Show the agents' reports as they are. Do not narrate.
