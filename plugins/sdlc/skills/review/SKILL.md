---
name: review
description: Reviews a finished implementation in two tracks, requirements and code quality, and writes implementation-review.md with findings, user decisions and fix batches that sdlc:implement executes. Does not change code.
when_to_use: Use only when the user explicitly asks to review a finished implementation, check it against the ticket or plan, or code-review it before a merge request, or when sdlc:auto runs it. Never start it on your own after implementing. Needs implementation-summary.md with status done. Do not use for planning, implementing or fixing; fixes go through sdlc:implement.
argument-hint: "[path to implementation-summary.md | feature name] [base branch, reference implementation path, extra notes, links, files]"
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion, ToolSearch, WebFetch, Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git branch:*)
---

# Review

Check a finished implementation in two tracks: does it do what was asked, and is the code sound. Where the plan asks for `runtime` evidence, a third track runs it and observes the result. Output is `implementation-review.md`: findings, decisions and fix batches. The fixes themselves are done by `sdlc:implement`.

Do not fix, plan or redesign. Do not reopen a decision recorded in the summary unless the user does, or it conflicts with a guideline (see Rules).

## Preconditions

- The project is a git repository. If not, stop.
- `implementation-plan.md` and `implementation-summary.md` exist and the summary status is `done`. If not, stop and point to `sdlc:implement`.
- The working tree is clean. If not, ask: commit, stash or stop.

## Inputs

Arguments: $ARGUMENTS

1. Find the summary: the path in the arguments, else `.ai/dev/<name>/implementation-summary.md` for the given name or the current branch. Read it fully.
2. Read in the same directory: `implementation-plan.md`, `context-discovery.md`, `guidelines/*`, and `implementation-review.md` if present. The last one means a previous round exists. See Next round.
3. Read every file and link in the plan's `Guidelines to follow` and in the arguments. Record anything unreadable as a question.
4. If the ticket is reachable, read it again. Note requirements added or changed since discovery.
5. Note installed project skills that describe coding, testing or architecture conventions. Ignore workflow skills.
6. Base branch: the one given in the arguments, else the repository default branch.
7. Reference implementation: the path given in the arguments, else one named in discovery or the summary, else none.
8. Verification command: from the summary.

## Process

1. **Review.** Delegate to the agents below in parallel. Do not pass one agent's result to another.
   - `requirements-reviewer`: the paths to discovery, plan, summary and guidelines; ticket changes since discovery; base and branch; the reference implementation path.
   - `code-reviewer`: base and branch; the verification command; the paths to guidelines; the project skills; the decisions and deviations from the summary.
   - `runtime-verifier`, only when a plan phase asks for `runtime` evidence: mode `verify`; the plan path; those phases; the evidence directory `<dir>/evidence/`; base and branch; the project skills; the decisions.
2. **Evidence.** Set the evidence level: `checks only` when no phase asks for `runtime` evidence, else the level the `runtime-verifier` reports. An observation with `fail` or `not observed` sets its requirement row to `partial` or `missing`, with the observation as evidence. `not available` is not a finding: record the reason and what to fix.
3. **Gate.** If the verification command failed, set status `blocked`. Write the document with the gate result and one fix batch: make verification pass. Stop.
4. **Cross-check.** Read the cited evidence of every `high` finding. Drop a finding that a requirement, a recorded decision or the reference implementation explains, unless it is marked `decided`. Merge duplicates. Keep the `incompatible` and `decided` marks the code reviewer set.
5. **Write.** Create `implementation-review.md` next to the summary from `${CLAUDE_PLUGIN_ROOT}/skills/review/template.md`. Every decision is `open`. Show a short summary in chat: status, evidence level, requirements not met, finding counts by severity, findings marked `incompatible` or `decided`. The document holds the detail.
6. **Decide.** See Decisions.
7. **Fix batches.** Group every item with decision `fix` into batches: by area, high first, each small enough for one commit. A batch has steps, acceptance criteria and tests like a plan phase, and lists the ids it closes.
8. **Status.** `changes requested` if any item has decision `fix`. Else `approved`, or `approved with follow-ups` when `defer` or `owner` items remain. Write `defer` and `owner` items under Follow-ups. Add the round line.
9. **Hand-off.** On `changes requested`: say to run `sdlc:implement` on `implementation-review.md`, then `sdlc:review` again. On approval: suggest updating project documentation, then `sdlc:submit` to create the MR/PR.

## Decisions

- Recommend a decision per item: `fix`, `no action` or `defer`. A requirement that is not `met` is recommended `fix`. An item marked `incompatible` or `decided` gets no recommendation; the user decides.
- Ask per severity group with `AskUserQuestion`: accept the recommendations, decide per id, or no action for the group. Per-id answers may come as chat text: `<id>: fix | no action | defer | owner <who>`, with an optional note.
- Answer every per-id answer by its id before recording it. Push back once, with the reason, when it contradicts a requirement or a recorded decision. Then record what the user decided.
- Carry the user's notes into the fix batch steps.

## Next round

If `implementation-review.md` exists:

1. Round = previous round + 1. If the branch head equals `Reviewed at`, say that nothing changed and stop.
2. Pass the document and the previous `Reviewed at` commit to both agents. They re-check items not `met` and findings marked `fixed`, and review only the diff since that commit. Pass the `runtime-verifier` only the observations that did not pass, or all of them when the previous level was `not available`.
3. Ids never change. New findings continue the numbering. From round 2 on, add only `high` findings and regressions.
4. After round 3, ask: accept the remaining open items as follow-ups, or continue.

## Auto mode

Only when the arguments contain `--auto`. `sdlc:auto` sets it. Read `${CLAUDE_PLUGIN_ROOT}/skills/auto/policy.md` and follow its Rules and its Review section. They replace the Decisions questions of this skill and set the reviewer models. Everything else here still applies.

## Rules

- Never edit source files. You edit only `implementation-review.md`.
- Technology agnostic. The project's guidelines, skills and surrounding code define the conventions. A project convention wins over a general pattern. The agents mark a fix that conflicts with a convention as `incompatible`; the user decides.
- A behaviour change is never a code finding. It is a requirement gap or a follow-up.
- A finding needs evidence as `path:line`. No evidence, no finding.
- A decision recorded in the summary is a given. Report it only if the user reopens it, or once, marked `decided`, when it conflicts with a guideline or a project skill.
- Every question goes through `AskUserQuestion`. Per-id decisions may also come as chat text.
- Short messages. Facts and `path:line`. No narration.
