---
name: requirements
description: Writes the Requirements section of .ai/design/<system>/design.md - actors, core operations, supportive areas, features by horizon, quality scenarios with measures, constraints, out of scope and a context diagram - at the depth the charter sets. Proposes, then confirms in capped Q&A rounds. Second phase of system-design:design.
when_to_use: Use when the user wants to define or refine what a system must do and how well - actors, core operations, features, quality attributes, constraints. Needs an approved Charter in design.md. Not for the charter (system-design:brief) or the architecture (system-design:architecture).
argument-hint: "[system name] [revise <change>] [notes, links, files]"
allowed-tools: Read, Glob, Grep, AskUserQuestion, WebFetch, ToolSearch
---

# Requirements

Define what the system must do and how well, at the depth the charter sets. Output is the Requirements section of `design.md`.

You MUST NOT design, invent a number or a priority, record out of scope without confirmation, or write product content such as success metrics, user stories or UX.

## Inputs

Arguments: $ARGUMENTS

- Directory: `.ai/design/<slug>/`, slug from the system name. Called by `system-design:design`: the message gives system, directory and inputs.
- `design.md`: Charter and Context. Charter missing or `draft`: stop and say to run `system-design:brief <slug>` first.
- The sources listed in Context, and any extra file or link in the arguments. Read only what is named.
- `open-questions.md`, section `## Brief`: questions this phase can settle.
- `${CLAUDE_PLUGIN_ROOT}/references/design-template.md`: the section structure.
- Requirements already `approved` and no `revise` argument: say so and offer revise or redo. Requirements `draft`: resume the Q&A from it, skip the proposal.

## Process

1. **Depth.** Read the charter depth lines for requirements, quality scenarios and rounds. `decision` preset: write only Quality scenarios as criteria and Constraints, plus Actors and Core operations when the decision depends on them; no Features table, no diagram.
2. **Propose.** Show the section in chat, built from the sources:
   - Actors: people and external systems that interact with the system, one line each.
   - Core operations: `verb noun - trigger -> outcome`. More than 3 is a red flag: say so and ask the user to confirm them or demote some to supportive areas.
   - Supportive areas: what the system needs but is not about, for example authentication, billing, notifications, administration, reporting.
   - Features: table with horizon `now`, `later` or `out`. `out` only when a source or the user confirms it; propose an `out` item as a question. `mvp` preset: `later` rows carry the name only.
   - Quality scenarios: `<quality>: when <stimulus>, <response> within <measure>`. Check performance, scalability, availability, consistency, security, privacy, observability, cost. Every number is a proposal marked `(assumed)` until confirmed.
   - Constraints: team, budget, deadline, compliance, existing system, mandated technology. From sources only.
   - Out of scope: items confirmed by a source or the user.
3. **Q&A.** Rounds as the charter says. One `AskUserQuestion` call per round, up to 4 questions, each with a recommended answer and reason. Ask in this order: core operations, horizon per feature where sources are silent, numbers in quality scenarios, constraints, open conflicts from Context, Brief open questions this phase can settle. A confirmed number loses its `(assumed)` tag. "I don't know" keeps the tag and becomes an open question.
4. **Draft.** After the first round, replace the text between the `requirements` marker and the `architecture` marker with the section, `_Status: draft_`. Update it after every round.
5. **Diagram.** Context diagram: actors, the system, external systems. Mermaid flowchart, at most 12 nodes, labels are names only. Skip for `decision`.
6. **Approve.** Show the section. Wait. If the user asks for changes, apply them and show again; after the second rejection ask one open question about what is missing, then continue. Approval sets `_Status: approved <date>_` and the document status `in progress`.
7. **Log.** Overwrite `## Requirements` in `open-questions.md`. Questions: `(assumed)` items the architecture depends on, "I don't know" answers, each with why it matters. Risks: facts that threaten feasibility, for example a scenario against a constraint. In `## Brief`, append `[settled in Requirements]` to every question answered here; change nothing else there. Append a `decisions.md` entry, status `accepted`, for every conflict between sources the user resolved.
8. **Summary.** At most 5 bullets: core operations, features by horizon as counts, scenarios confirmed and assumed, constraints, path. Standalone: say to run `system-design:architecture <slug>` next.

## Revise

`revise <change>`: read the section, apply the change, show it, wait for approval, replace only the Requirements section and set it `approved <date>`. Set Architecture, Summary and Next steps to `_Status: stale_` and the document status to `in progress`. Say that `system-design:architecture <slug> revise` is needed and that any review is outdated.

## Go back

When the user wants to change the charter or the context mid-run, keep the draft as it is, stop, and say to run `system-design:brief <slug> revise <change>`, then this skill again.

## Rules

- Design-level requirements only. A PRD is the place for success metrics, user stories and UX.
- Every proposal is confirmed or tagged `(assumed)`. Never present an assumption as a fact.
- Rounds per the charter. Do not loop. Unresolved items go to open questions.
- Write only inside `.ai/design/<slug>/`. Never edit other files.
- Cap 60 lines. Diagram, marker and status lines do not count.
- Short messages. The document holds the detail.
