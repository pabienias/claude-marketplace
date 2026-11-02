# system-design

System design from draft notes: charter, requirements, architecture options, decisions and an independent review in one design document.

## Install

```
/plugin marketplace add <path-or-repo-of-this-marketplace>
/plugin install system-design@pb-claude-marketplace
```

## What it produces

All files go to `.ai/design/<system>/`. Nothing else in the repository is touched.

| File | Content |
|---|---|
| `design.md` | Entry point. Summary, Charter, Context, Requirements, Architecture, Next steps. One section per phase, each with a status line. |
| `decisions.md` | One entry per significant decision: context, decision, alternatives with reasons, consequences, status. Append-only. |
| `open-questions.md` | Questions with why they matter and risks as facts, one section per phase. |
| `review.md` | Findings with severity, evidence, amendment, decision and outcome. Done-when check. Round and status. |
| `inputs/notes-<date>.md` | Notes pasted or given inline, saved verbatim so the design can point to them. |

## Contents

| Component | Type | Purpose |
|---|---|---|
| `system-design:design` | skill | Full run: intake, then `brief`, `requirements`, `architecture` and `review` in order, then amendments. Resumes an unfinished design. |
| `system-design:brief` | skill | Readout of the notes, preset proposal, charter Q&A. Writes Charter and Context. |
| `system-design:requirements` | skill | Actors, core operations, features by horizon, quality scenarios, constraints. Writes Requirements. |
| `system-design:architecture` | skill | Drivers, options on a named axis, chosen design, operation paths, technology with alternatives, deferred items. Writes Architecture, Summary, Next steps and decisions. |
| `system-design:review` | skill | Runs the `design-reviewer` agent, records the user's decision per finding, stops. `close` after amendments, `recheck` for an agent re-check. |
| `system-design:design-reviewer` | agent | Fresh-context, read-only check of the design against charter and requirements. Reports findings with evidence. |
| `references/presets.md` | reference | The `mvp`, `full` and `decision` presets and the custom rule. Read by `brief` only. |
| `references/design-template.md` | reference | Skeleton of `design.md`: markers, headings, status lines, caps. |
| `references/log-templates.md` | reference | Skeletons of `decisions.md` and `open-questions.md`. |

## Permissions

Skills pre-approve read-only tools, `AskUserQuestion`, `Skill`, `WebFetch` and `ToolSearch`. Writes to `.ai/design/` follow your own permission settings. To reduce prompts, allow `Write(.ai/design/**)` and `Edit(.ai/design/**)`. The agent inherits your settings.

## Flow

`design` runs everything: intake → `brief` → `requirements` → `architecture` → `review` → amendments → `review close`. Each phase skill also runs alone. It needs the sections before it approved; if they are missing it stops and names the skill to run. Any phase skill runs in revise mode: `<skill> <system> revise <change>`.

## Rules

- The charter comes first. Presets `mvp`, `full` and `decision` set the depth of every later phase.
- Every question carries a recommended answer. Up to 4 questions per round, rounds capped per preset.
- Sections are drafted to the file during Q&A. Approval flips the status line to `approved`. An interrupted run resumes from the draft.
- Options differ on a named axis. Rejected alternatives and reasons go to `decisions.md`.
- Technology is recommended at charter depth, every pick with one alternative and a reason.
- The reviewer reads only the files. Amendments go through the owning skill. Two review rounds at most unless asked.
- Mermaid diagrams only, at most 12 nodes each. Diagrams do not count toward line caps.
- A PRD is input, never output. Without one, requirements are design-level and the charter says so.
- When `.ai/onboarding/` docs exist they are read as a source. The plugin never runs `onboarding`.
