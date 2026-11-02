---
name: architecture
description: Writes the Architecture, Summary and Next steps sections of .ai/design/<system>/design.md and appends to decisions.md - drivers, options on a named axis, the chosen design with container diagram and operation paths, cross-cutting concerns, technology picks with alternatives, deferred items. Third phase of system-design:design.
when_to_use: Use when the user wants architecture options, a chosen design, technology recommendations, operation paths or decision records for a system. Needs approved Charter and Requirements in design.md. Not for requirements (system-design:requirements) or implementation planning.
argument-hint: "[system name] [revise <change>] [notes, links, files]"
allowed-tools: Read, Glob, Grep, AskUserQuestion, WebFetch, ToolSearch
---

# Architecture

Derive the design from the requirements: drivers, options, one chosen and elaborated at the depth the charter sets. Output is the Architecture, Summary and Next steps sections of `design.md` and new entries in `decisions.md`.

You MUST NOT present a single option as the only way, name a technology without an alternative and a reason, or design beyond the horizon.

## Inputs

Arguments: $ARGUMENTS

- Directory: `.ai/design/<slug>/`. Called by `system-design:design`: the message gives system, directory and inputs.
- `design.md`: Charter, Context, Requirements. Any of them missing or `draft`: stop and name the owning skill to run first.
- `decisions.md`, the existing-system sources the charter names, `open-questions.md` sections `## Brief` and `## Requirements`. Extra files or links from the arguments. Read only what is named.
- `${CLAUDE_PLUGIN_ROOT}/references/design-template.md` and `${CLAUDE_PLUGIN_ROOT}/references/log-templates.md`.
- Architecture `approved` and no `revise` argument: say so and offer revise or redo. `draft`: resume from it. `stale`: requirements changed; show the drivers again with what changed, keep the chosen option unless a driver changed, then continue from step 3.

## Process

1. **Depth.** Read the charter depth lines for architecture, technology, review and rounds.
2. **Drivers and options.** One chat message, then one `AskUserQuestion` call with two questions.
   - Drivers: 3 to 7 items from Requirements that shape the design most: core operations, quality scenarios, constraints. One line each with why it matters.
   - Options: count per depth. Every option differs from the others on one named axis, for example deployment unit, synchronous versus asynchronous, build versus buy. An option that differs only in a product brand is not an option. Per option: a three-line description, a Mermaid container diagram of at most 12 nodes, fit per driver, main risk, complexity low, medium or high.
   - Comparison table, the recommended option marked with a one-line reason.
   - Questions: "Drivers correct?" with options yes, change, I don't know. "Which option?" with the recommended one first. Drivers wrong: redo the options once.
3. **Elaborate** the chosen option, per depth. Containers and responsibilities, data each container owns, integration points with external systems. Operation paths: one Mermaid sequence diagram per core operation, at most 12 nodes. Cross-cutting: authentication, observability, failure handling, configuration; `mvp` only what a core operation needs. Technology: a pick per container or concern the design depends on, each with one alternative and the reason it lost; a technology from Constraints is a pick with alternative `none - mandated`. Deferred: every `later` feature and everything the design does not solve yet, with its horizon and why deferring is safe. Fit with the existing system, only when the charter names one: what is reused, what changes, the path from the current state. `decision` preset: stop after the pick; write Drivers, Options considered and the decision entries only.
4. **Draft.** Replace the text between the `architecture` marker and the `next-steps` marker, `_Status: draft_`. Only the chosen option's container diagram and the operation paths go to the document; the other diagrams stay in chat. Rounds per the charter, up to 4 questions each, for what the sources leave open: data ownership, integration details, technology constraints, Brief and Requirements open questions this phase can settle. Update the draft after every round.
5. **Decisions.** One entry per significant choice: the option pick and every choice that is hard to reverse or affects a driver. Each with the alternatives and the reason they lost. Status `accepted`. Ids continue the log.
6. **Summary and Next steps.** Summary: goal in one line, the design in at most 5 bullets, key decision ids, open questions count. Next steps: `mvp` and `full`: run `system-design:review <slug>` when no review exists, then start implementation planning from this directory; `decision`: record the decision where the team keeps decisions; all: an owner per open question.
7. **Approve.** Show the three sections and the new decision entries. Wait. If the user asks for changes, apply them and show again; after the second rejection ask one open question about what is missing, then continue. Approval sets the three sections `_Status: approved <date>_` and the document status `designed`.
8. **Log.** Append the decisions. Overwrite `## Architecture` in `open-questions.md`. Questions: what the design assumes without a source, each with why it matters. Risks: facts that threaten a driver, for example a scenario met only under an assumption. Append `[settled in Architecture]` to every earlier question answered here; change nothing else there.
9. **Chat summary.** At most 5 bullets: chosen option and axis, containers, technology picks, decisions added, deferred count. Standalone: say to run `system-design:review <slug>` next.

## Revise

`revise <change>`: read the three sections, apply the change, show them, wait for approval, replace only these sections and set them `approved <date>`. Record a decision when an alternative was rejected. Set the document status `designed` and say that any review is outdated and `system-design:review <slug>` should run again. When the change lists review finding ids, apply each amendment and name the ids in the chat summary.

## Go back

When the user wants to change the requirements or the charter mid-run, keep the draft as it is, stop, and say to run the owning skill in revise mode, then this skill again.

## Rules

- Options differ on a named axis. Rejected alternatives and reasons live in `decisions.md`, not in the document.
- Every technology pick has an alternative and a reason. A user constraint wins over a recommendation.
- Stay within the horizon. What the horizon excludes goes to Deferred with a reason, never into the design.
- Rounds per the charter. Do not loop.
- Write only inside `.ai/design/<slug>/`. Never edit other files.
- Cap 80 lines for Architecture, 20 for Summary, 10 for Next steps. Diagrams, marker and status lines do not count.
- Short messages. The document holds the detail.
