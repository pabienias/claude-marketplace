---
name: review
description: Reviews .ai/design/<system>/design.md with the fresh-context design-reviewer agent, writes review.md with findings and the user's decision per finding, sets the design status and stops. Amendments run through the owning phase skills in revise mode. `close F<ids>` marks findings amended and sets the final status; `recheck` re-runs the agent on blockers first. Fourth phase of system-design:design.
when_to_use: Use when the user wants a system design checked against its charter and requirements, or asks for a design review, gap check or challenge. Needs approved Charter, Requirements and Architecture in design.md. Use `close` after amendments were applied and `recheck` for an agent re-check. Not for changing the design; the phase skills do that in revise mode.
argument-hint: "[system name] [close [F<ids>] | recheck]"
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion
---

# Review

Check the design with an independent reviewer, record the user's decision per finding, stop. Output is `review.md` and the status line of `design.md`. Amendments are applied by the owning skills, not here.

You MUST NOT edit `design.md` beyond its status line, drop, merge or soften a finding, or re-open a recorded decision.

## Inputs

Arguments: $ARGUMENTS

- Directory: `.ai/design/<slug>/`. Called by `system-design:design`: the message gives system and directory.
- `design.md`: Charter, Context, Requirements and Architecture all `approved`, none `stale`. Else stop and name the owning skill to run first.
- `decisions.md`, `open-questions.md`. `review.md` when present: a next round, see Rounds.
- `${CLAUDE_PLUGIN_ROOT}/skills/review/template.md` and `${CLAUDE_PLUGIN_ROOT}/references/log-templates.md`.
- Mode from the arguments: none is Run, `close` or `recheck`.

## Run

1. **Delegate.** Run the `design-reviewer` agent with the absolute paths to the three files and the charter's review depth line. Pass nothing else: no chat history, no opinion. Next round: also pass `review.md` and the previous review date.
2. **Write.** `review.md` from the template: round, date, done-when, every finding with decision `open` and outcome `open`, skipped, coverage, not verifiable. Copy the agent's findings as they are. Chat: done-when result, finding counts by severity, skipped count.
3. **Decide.** Recommend `amend` for blocker and should-fix, `no action` for note. One `AskUserQuestion` call, one question per severity group present. Options: accept the recommendation, decide per id, no action for the group. Per-id answers may come as chat text `F<n>: amend | no action [note]`. Record every decision in the table. A blocker set to `no action` is an accepted risk: append a `decisions.md` entry with status `accepted risk`, set the outcome `accepted risk` and list `F<n> -> D<m>` under Accepted risks. A `no action` on other findings sets the outcome `no action`.
4. **Status.** `changes requested` when any `amend` is open. Else `approved`, or `approved with accepted risks` when the list is not empty. Write it in `review.md` and in the status line of `design.md`. Overwrite `## Review` in `open-questions.md`: Questions from Not verifiable, Risks from Accepted risks, as facts.
5. **Stop.** Standalone: list the exact commands, one per owning section in document order, `system-design:<owner> <slug> revise F<ids>: <amendments>`, then `system-design:review <slug> close F<ids>`. Called by `design`: return the same list grouped by owner. Do not invoke any skill.

## Close

`close [F<ids>]`. The given ids are `amended`. No ids: one `AskUserQuestion` with options all `amend` findings, decide per id in chat, none. Set the status as in Run step 4, in both files. Chat: what changed, what is still open.

## Recheck

Preconditions as in Run. Run the agent on the blockers with decision `amend`, with `review.md`. Fill `## Re-check`. A `resolved` blocker is `amended`. Then continue as Close for the remaining `amend` findings.

## Rounds

`review.md` present and mode Run: round + 1, ids kept, new findings continue the numbering. Before round 3, ask: accept the remaining open findings as accepted risks, or continue.

## Rules

- The agent decides what is a finding. You never filter it; a recorded decision is already respected by the agent and listed under Skipped.
- Every finding keeps its evidence: section and quoted line.
- Only the user changes a decision. Push back once, with the reason, when a decision contradicts a requirement; then record it.
- Write only `review.md`, `decisions.md`, `open-questions.md` and the status line of `design.md`.
- Short messages. The document holds the detail.
