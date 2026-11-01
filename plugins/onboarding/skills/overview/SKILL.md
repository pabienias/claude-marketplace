---
name: overview
description: Inventories the repository with a read-only scan and writes overview.md and tech-stack.md in .ai/onboarding/ - units, stack, where things are, documentation index. Documented facts only. Foundation for onboarding:product and onboarding:architecture.
when_to_use: Use when the user asks for a first overview of the repository, which units or packages it has, what the tech stack is, or where things are. Not for a quick dependency lookup or a question about one file.
argument-hint: "[what the product is] [links, files, notes]"
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion, ToolSearch, WebFetch, Bash(git ls-files:*), Bash(git rev-parse:*), Bash(git remote:*), Bash(git log:*)
---

# Overview

Inventory the repository: units, stack, where things are, which docs exist. Output is `overview.md` and `tech-stack.md`. Documented facts only.

You MUST NOT analyse, infer, recommend or edit anything outside `.ai/onboarding/`.

## Inputs

Arguments: $ARGUMENTS

- Optional from the user: a product one-liner, links, files, notes. Record them as reference `User, chat`.
- Called by `onboarding:onboard`: intake facts, Q&A answers and the approval are given. Skip steps 2, 7 and 8.
- References: `${CLAUDE_PLUGIN_ROOT}/references/scan-budget.md` and `${CLAUDE_PLUGIN_ROOT}/references/signals.md`. Pass both absolute paths to every agent.

## Process

1. **Commit.** `git rev-parse --short HEAD` for the document header. Not a git repository: write `no git`.
2. **Initial scan.** Read the root `README*` (first 200 lines). Nothing else.
3. **Delegate.** Run two agents in parallel. Do not pass one result to the other.
   - `repo-surveyor`: user-given context, reference paths.
   - `docs-reader`: external links from the user and the README, reference paths.
4. **Dates.** `git log -1 --format=%cs` for the repository, then `git log -1 --format=%cs -- <path>` per unit and per repository doc. A doc is `possibly stale` when its date is more than 6 months before the last change of its unit, or of the repository for a root doc. Not a git repository: `unknown`.
5. **Synthesize.** Fill both templates.
   - `What it is` and `System boundary` come from docs and user answers only. Nothing documented: write `unknown` and add a question.
   - Units, stack, infrastructure, tooling and environment come from the surveyor. Versions only when pinned.
   - Documentation comes from the docs reader, with its relevance tags. Unreadable links stay listed with the reason.
   - Drop every item without an evidence path. Check the `Reads` lines against the scan budget; a breach goes to Follow-ups.
6. **Challenge.** Anything that needed reading source or interpreting names does not belong here. Move it to a question for `product` or `architecture`.
7. **Ask.** Up to 2 questions with `AskUserQuestion`, one call, only when the product one-liner or a unit's kind is unclear. Every question has an "I don't know" option. "I don't know" becomes a question in `open-questions.md`.
8. **Approve.** See Output.
9. **Write.** See Output.

## Rules

- Read-only outside `.ai/onboarding/`. Never edit other files. Never suggest changes; an observation becomes a follow-up, phrased as a fact.
- Facts from signal files, docs and the user are documented. No `(inferred)` items in these two documents.
- Every fact cites `[[n]](#references)`, a link to the References section. References list every artefact used once, numbered in order of first use. Before writing, check that every citation has an entry and every entry is cited.
- Do not copy documentation. One line per doc.
- Technology agnostic. The examples in `signals.md` are not a checklist.
- Short. Bullets. No narrative. Each document at most 100 lines.

## Output

Read `${CLAUDE_PLUGIN_ROOT}/skills/overview/template.md` and use its structure.

1. Show a concise summary in chat, at most 6 bullets: what it is, units, main stack, docs found, unreadable links, questions.
2. Propose the paths `.ai/onboarding/overview.md`, `.ai/onboarding/tech-stack.md` and `.ai/onboarding/open-questions.md`. Say which exist and will be overwritten. Say the user can ask for the full content before approving.
3. Wait for approval. Create no directory and write no file before approval. If the caller already approved the directory, skip the wait.
4. Write `overview.md` and `tech-stack.md`.
5. `open-questions.md`: if missing, create it with the sections `## Overview`, `## Product`, `## Architecture`, each with `### Questions` and `### Follow-ups` set to `none`. Then overwrite only the `## Overview` section. Each question has one line on why it matters. Each follow-up is a fact with its evidence path or URL.
6. If the user declines, keep the summary in chat only.
