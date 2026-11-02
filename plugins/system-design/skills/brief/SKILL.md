---
name: brief
description: Starts a system design in .ai/design/<system>/design.md - reads the user's notes, files and links, proposes a preset, runs one charter Q&A round and writes the Charter and Context sections. Context is a grouped digest of the notes with sources. First phase of system-design:design.
when_to_use: Use when the user wants to start a system design, set its goal, horizon, audience or depth, or restate what is known about a system before designing it. Not for requirements (system-design:requirements) or architecture (system-design:architecture).
argument-hint: "[system name] [revise <change>] [notes, files, links]"
allowed-tools: Read, Glob, Grep, AskUserQuestion, WebFetch, ToolSearch
---

# Brief

Agree the charter and digest what is known. Output is the Charter and Context sections of `design.md`, plus the log files when they do not exist yet.

You MUST NOT design, pick a preset or a horizon without asking, or copy a durable source into the document.

## Inputs

Arguments: $ARGUMENTS

- From the user: a system name, notes, files, links. Read every file and link. An unreadable item becomes an unknown and an open question.
- Called by `system-design:design`: the message gives system, directory and inputs. Do not ask for the system name again.
- `${CLAUDE_PLUGIN_ROOT}/references/presets.md`: the preset table and how to write the charter.
- `${CLAUDE_PLUGIN_ROOT}/references/design-template.md` and `${CLAUDE_PLUGIN_ROOT}/references/log-templates.md`: file skeletons.
- Directory: `.ai/design/<slug>/`, slug from the system name in lowercase with hyphens. An existing `design.md` with an approved Charter: say so and offer revise or restart. A `draft` Charter: resume from it, skip the readout.

## Process

1. **Sources.** Give every source a short name: `notes`, `prd`, a file name, a link title, `chat` for what the user says in the conversation. A source that describes a system this design extends is an existing-system source. Notes pasted in chat or passed inline that are longer than a few lines: save them verbatim to `<dir>/inputs/notes-<date>.md` and use that path as the source.
2. **Readout.** At most 8 bullets in chat: what the system is, what each source covers, what no source covers, conflicts between sources, whether a PRD was found, whether the design extends an existing system and which source describes it, the proposed preset with a one-line reason.
3. **Q&A.** One `AskUserQuestion` call, up to 4 questions, each with a recommended answer and reason:
   - System name, when missing. Options: up to 3 candidates from the sources.
   - Preset. Options: `mvp`, `full`, `decision`, `custom`, recommended first. Each description shows its horizon and done-when line from `presets.md`.
   - Audience. Options: me only, my team, team and stakeholders.
   - Source role, one question per source whose role is unclear: "How should I treat <source>?" Options: PRD, describes an existing system this design extends, notes.
   More than 4 questions apply: a second call. `custom`: another call for the depth rows, starting from the nearest preset. An "I don't know" answer becomes an open question.
4. **Create.** When missing, create `design.md` from the design template with every section `_Status: not started_` and the document status `in progress`, and create `decisions.md` and `open-questions.md` from the log templates. Keep every marker line.
5. **Draft.** Replace the text between the `charter` marker and the `context` marker, and between the `context` marker and the `requirements` marker. Both sections `_Status: draft_`.
   - Charter: copy the chosen preset rows into the depth lines, plus Horizon and Done when, as `presets.md` says. PRD line: the path or link, else `none - requirements are design-level, not a product specification`. Existing system: the short names of the existing-system sources, else `none`.
   - Context. Sources: one line each. Known: one bullet per fact, grouped by topic in this order: Idea, Scope, Features, Constraints, Technology, Out of scope; every bullet ends with its source short name. Out of scope only when a source states it. Assumptions: items no source states but the design will need, tagged `(assumed)`. Unknowns: what no source covers, checked against this list: actors, core operations, scale and load, integrations, deadline, team, budget, compliance. Conflicts: one line per disagreement between sources, with the resolution from the Q&A or `open`.
6. **Approve.** Show both sections. Wait. If the user asks for changes, apply them and show again; after the second rejection ask one open question about what is missing, then continue. Approval sets both sections `_Status: approved <date>_`.
7. **Log.** Overwrite `## Brief` in `open-questions.md`. Questions: unknowns that a later phase needs, "I don't know" answers, unreadable sources, open conflicts, each with why it matters. Risks: facts that threaten the design, for example a deadline against the scope.
8. **Summary.** At most 5 bullets: preset and horizon, done-when, source count, unknowns count, paths. Standalone: say to run `system-design:requirements <slug>` next.

## Revise

`revise <change>`: read Charter and Context, apply the change, show both sections, wait for approval, replace only these two sections and set them `approved <date>`. Mark nothing stale. When Preset, Horizon, Depth or Done when changed, say that Requirements and Architecture may need a revise and name the skills. Record a decision in `decisions.md` only when the user rejected an alternative.

## Rules

- Point to durable sources such as a PRD or an existing-system document. Digest the notes; never paste them into the document.
- Read only what the user named. Never look for inputs in the repository on your own.
- Never present an assumption as a fact. Never infer Out of scope.
- One Q&A round, two for `custom`. Do not loop.
- Write only inside `.ai/design/<slug>/`. Never edit other files.
- Charter cap 15 lines, Context cap 40. Marker and status lines do not count.
- Short messages. The document holds the detail.
