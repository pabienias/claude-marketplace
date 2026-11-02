---
name: design
description: Full system design in one run - intake, then system-design:brief, system-design:requirements, system-design:architecture and system-design:review in order, then amendments through the owning skills and review close. Resumes an unfinished design from its section status lines. Writes nothing itself; output is .ai/design/<system>/design.md, decisions.md, open-questions.md and review.md.
when_to_use: Use when the user wants to design a system or a part of one, has notes or an idea and asks for a system design, an architecture proposal or a design document, or wants to continue an unfinished design. Not for a question about one component, and not for implementation planning. When only one section is wanted, use that phase skill instead.
argument-hint: "[system name] [notes, files, links]"
allowed-tools: Read, Glob, Grep, AskUserQuestion, Skill, WebFetch, ToolSearch
---

# Design

Run the whole design process: intake, the four phases in order, amendments, close. Each phase runs its own Q&A, drafts and approval. Output is the four files in `.ai/design/<slug>/`.

You MUST NOT design, analyse the inputs, run agents or write files. The phase skills do the work.

## Inputs

Arguments: $ARGUMENTS

- From the user: a system name, notes, files, links. Check that every file and link is readable; do not analyse them. Unreadable items go to `brief` as unknowns.
- Directory: `.ai/design/<slug>/`, slug from the system name in lowercase with hyphens.
- No system name: Glob `.ai/design/*/design.md`. Existing designs found: ask which one to continue, or new. None: go to `brief`, which asks the name.

## Process

1. **Existing design.** When `design.md` exists, read its status line and every section status line. Ask with `AskUserQuestion`: continue from the first unfinished section, revise a named section, or restart.
   - Continue: go to step 2 and skip every phase whose sections are `approved`. A `draft` or `stale` section resumes inside its skill.
   - Revise: invoke the owning skill with `revise <change>` and stop.
   - Restart: say to delete or move the directory, or to use a new system name. The plugin deletes nothing. Stop.
   - Document status `approved` or `approved with accepted risks`: nothing to run; show the summary from step 4 and stop.
2. **Phases.** Invoke with the Skill tool, in order: `system-design:brief`, `system-design:requirements`, `system-design:architecture`, `system-design:review`. Tell each: "Called by design. System: <slug>. Directory: <dir>. Inputs: <paths and links>. Unreadable: <items>." Do not repeat a phase's Q&A or approval; the skill owns them. When a skill stops with a pointer, repeat the pointer and stop.
3. **Amendments.** When `review` ends with `changes requested`: take its amendment list grouped by owner. Invoke the owning skill once per section, in document order, with `revise F<ids>: <amendments>`. Collect the ids each skill reports as applied. Then invoke `system-design:review <slug> close F<ids>`.
4. **Summary.** At most 8 bullets, read from the files: purpose and horizon, chosen option, decision count, findings amended and accepted risks, open questions count, document status, paths. End with: read `design.md` first.

## Rules

- Invoke no agents. Analyse nothing. Write nothing. Only the phase skills read sources, ask questions and write.
- One skill at a time, in order. Never invoke a skill from inside another skill's run.
- Every question goes through `AskUserQuestion`. The orchestrator asks only the questions in step 1 and the Inputs section.
- Short messages. The documents hold the detail.
