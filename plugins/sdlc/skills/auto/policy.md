# Auto mode policy

Applies when a stage skill runs with `--auto`. It replaces the approvals of that skill. Everything else in the skill still applies.

## Rules

- Decide by this policy. Ask the human only for an escalation listed here. Ask with `AskUserQuestion`.
- Record every decision the policy makes in `.ai/dev/<name>/auto-run.md` as `<stage> - auto: <decision> (<rule>)`. Record every escalation with its answer.
- Never drop an item silently. `no action` is a human-only decision.
- Never merge. Never push to the target branch.
- A case this policy does not cover is an escalation.
- Do not ask whether to go on to the next stage. `sdlc:auto` decides that.

## Context discovery

- Ask every question the skill asks. This stage is where the human gives context.
- Write the file without approval.
- Escalate: none beyond the questions.
- `Not ready for planning` stops the run.

## Plan

- Q&A: ask only about scope, behaviour and Core changes that discovery does not decide. For every other question take the recommended answer and record it under Assumptions as `confirmed: auto`. If nothing is left to ask, skip the Q&A without asking.
- Escalate when any of these is true. Show the plan summary once and ask: approve, request changes, or reject.
  - `Core changes` is not `none`.
  - An assumption is `confirmed: no`.
  - An open question is not `deferred`.
  - A plan file already exists.
- Otherwise set `Status: approved (auto)`.

## Implement

- Setup: show it in one message. Do not wait for approval.
- Phase start: do not ask.
- Verifier `pass`: commit with the proposed message. Do not ask.
- Escalate:
  - the working tree is not clean
  - no verification command is found
  - a `blocked` or `feedback` report, or any question in a developer report
  - verifier `fail` after 2 fix rounds

## Review

- Run `requirements-reviewer` and `code-reviewer` with `model: opus`.
- Decisions:
  - requirement not `met`: `fix`
  - finding `H` or `M`: `fix`
  - finding `L`: `defer`
- Evidence `not available` is not a failure. Record the reason.
- Escalate:
  - an item marked `incompatible` or `decided`
  - status `blocked`
  - an item reported `not fixed`, or the same finding open in two rounds
  - round 3 ends with a `fix` decision open

## Submit

- Create the MR/PR as a draft. Do not ask for approval of the title and description.
- If an MR/PR for the branch exists, update its description.
- Escalate:
  - the working tree is not clean
  - no CLI or tool can create the MR/PR. Give the final text.
