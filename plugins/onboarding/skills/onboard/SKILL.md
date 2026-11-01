---
name: onboard
description: Full project onboarding in one run - intake, a short Q&A, one approval, then onboarding:overview, onboarding:product and onboarding:architecture. Writes overview.md, tech-stack.md, product-brief.md, glossary.md, architecture.md and open-questions.md in .ai/onboarding/.
when_to_use: Use when the user wants to be onboarded to the project, asks for a full project overview, or asks what the product is and how it is built. Not for a quick question about one file or feature. When only one document is wanted, use that phase skill instead.
argument-hint: "[what the product is] [links, files, notes]"
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion, ToolSearch, WebFetch, Skill, Bash(git ls-files:*), Bash(git rev-parse:*), Bash(git remote:*), Bash(git log:*)
---

# Onboard

Full onboarding in one run: intake, a short Q&A, one approval, then the three phase skills. Output is the six documents in `.ai/onboarding/`.

You MUST NOT analyse the repository yourself, run agents directly or read source files. The phase skills do the work.

## Inputs

Arguments: $ARGUMENTS

- Optional from the user: a product one-liner, links, files, notes. Record them as reference `User, chat`.
- Read every file the user gives. An unreadable file or link becomes a question.

## Process

1. **Intake.** Record the arguments.
2. **Initial scan.** Read the root `README*` (first 200 lines). Glob documentation and agent context files at depth 2 and in documentation directories (see the Docs and Agent context categories in `${CLAUDE_PLUGIN_ROOT}/references/signals.md`), names only. Read nothing else. Extract: a product one-liner candidate, external links, names of other systems or repositories.
3. **Q&A.** Pick up to 4 questions from the list below, in this order, skipping what the arguments and the scan already answer. One `AskUserQuestion` call. Every question has an "I don't know" option. "I don't know" becomes a question in `open-questions.md`, owned by `Product` for the first four and by `Architecture` for the last.
   - Product: "I read the product as <one-liner>. Correct?" Options: yes, partly, I don't know. No one-liner found: ask what the product is and who uses it.
   - System boundary: "Is this repository the whole system?" Options: whole system, part of a larger system, I don't know.
   - Core areas: "Which of these are core business?" Multi-select from up to 3 unit or top-level names, plus I don't know.
   - Docs: "Where is documentation outside the repository?" Options: none, I don't know; links come as free text. Skip when the README already links them.
   - Focus: "Which area will you work on first?" Options from units or top-level names, plus none.
4. **Approval.** List the six paths: `overview.md`, `tech-stack.md`, `product-brief.md`, `glossary.md`, `architecture.md`, `open-questions.md`, all in `.ai/onboarding/`. Mark the files that exist; they will be overwritten. Wait. If declined, stop.
5. **Phases.** Invoke with the Skill tool, in this order: `onboarding:overview`, then `onboarding:product`, then `onboarding:architecture`. Tell each: "Called by onboard. Q&A done, directory approved, do not ask again." Pass the one-liner, links, the user's answers and the "I don't know" items. Each skill shows its own short summary and writes its files.
6. **Assemble.** Read `open-questions.md`. Check it has the three sections in the order of `${CLAUDE_PLUGIN_ROOT}/skills/onboard/template.md`. Add any "I don't know" item from step 3 that is not yet recorded to the Questions of its owning section.
7. **Summary.** At most 8 bullets: what the product is, units, style, open questions per section, file paths. End with: read `overview.md` first.

## Rules

- Invoke no agents directly. Read no source files. Do not analyse; the phases do.
- Read-only outside `.ai/onboarding/`. Never edit other files. Never suggest changes.
- Every question goes through `AskUserQuestion`. One round. Do not loop.
- Short messages. The documents hold the detail.
