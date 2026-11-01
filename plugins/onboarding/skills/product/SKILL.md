---
name: product
description: Writes product-brief.md and glossary.md in .ai/onboarding/ - purpose, actors, core features, supportive areas, core operations, external systems, scope, two diagrams, and a glossary of domain terms, abbreviations and internal names - from docs and a read-only scan by the domain-analyst agent. Runs onboarding:overview first when overview.md is missing.
when_to_use: Use when the user asks what the product does, who uses it, what its core features or business areas are, or wants a product brief. Not for a question about one feature, and not for technical structure (onboarding:architecture).
argument-hint: "[what the product is] [links, files, notes]"
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion, ToolSearch, WebFetch, Skill, Bash(git rev-parse:*)
---

# Product Brief

Describe the product: purpose, who uses it, what it does, what is core and what supports it. Output is `product-brief.md` and `glossary.md`. Inference is allowed here. Every inferred claim is tagged.

You MUST NOT describe technical structure, recommend anything, or edit outside `.ai/onboarding/`.

## Inputs

Arguments: $ARGUMENTS

- Optional from the user: a product one-liner, links, files, notes. Record them as reference `User, chat`.
- Foundation: `.ai/onboarding/overview.md`. If missing, say so and invoke `onboarding:overview` with the Skill tool first. It asks its own approval. Then continue.
- Read from `overview.md`: What it is, System boundary, Units, Where things are, Related repositories and systems, Documentation rows tagged `product`.
- Called by `onboarding:onboard`: intake facts, Q&A answers and the approval are given. Skip step 3 and the wait in Output.
- References: `${CLAUDE_PLUGIN_ROOT}/references/scan-budget.md` and `${CLAUDE_PLUGIN_ROOT}/references/signals.md`. Pass both absolute paths to the agent.

## Process

1. **Commit.** `git rev-parse --short HEAD` for the document header. Not a git repository: write `no git`.
2. **Foundation.** See Inputs.
3. **Ask.** Up to 3 questions with `AskUserQuestion`, one call, only for what `overview.md` and the arguments leave open. Every question has an "I don't know" option, which becomes a question in `open-questions.md`.
   - Product: "I read the product as <one-liner>. Correct?" Options: yes, partly, I don't know.
   - System boundary: "Is this repository the whole system?" Options: whole system, part of a larger system, I don't know.
   - Core areas: "Which of these are core business?" Multi-select from up to 3 unit or top-level names, plus I don't know.
4. **Delegate.** Run `domain-analyst` with: units, where things are, docs tagged `product` with paths or URLs, user answers, reference paths.
5. **Synthesize.** Fill the template.
   - Core vs supportive. Order of evidence: documented statement, user answer, structure signals (where routes and entities concentrate, names matching the product name). Typical supportive areas: authentication, billing unless billing is the product, notifications, administration, reporting, integrations. Core operations are ordered by importance: documented emphasis, then user answer, then signal strength. More than 3 core operations means the classification is too loose; move the weakest to supportive or raise a question.
   - Scope. `In` from docs, user answers and core features. `Out` only when a doc or the user states it. Never infer out of scope.
   - External systems. From the agent. Add the link from `Related repositories and systems` in `overview.md` when the names match.
   - Glossary. Domain terms, abbreviations and internal names of systems and units, from the agent's Terms. Alphabetical. A meaning from docs is documented. A meaning read from usage is `(inferred)`. No meaning found is `meaning unknown`. All unknown terms become one aggregated question in `open-questions.md`.
   - Diagrams. Context: actors, product, external systems. One flow per core operation, up to 3, at business level: nodes are actors, actions and outcomes. A node that names a component, endpoint, service or table belongs to `onboarding:architecture`; remove it. Mermaid `flowchart`, at most 12 nodes each, labels are names only.
6. **Challenge.** Drop every claim without evidence. Tag `(inferred)` where the evidence is names or structure. A feature with one weak signal becomes a question, not a feature. Check the agent's `Reads` line against the scan budget; a breach goes to Follow-ups.
7. **Approve.** See Output.
8. **Write.** See Output.

## Rules

- Read-only outside `.ai/onboarding/`. Never edit other files. Never suggest changes; an observation becomes a follow-up, phrased as a fact.
- Every claim cites `[[n]](#references)`, a link to the References section. References list every artefact used once, numbered in order of first use. Before writing, check that every citation has an entry and every entry is cited.
- Never present an inference as documented. Never infer out of scope.
- Do not copy documentation. Point to it.
- Technology agnostic. No layers, patterns or module structure; that is `onboarding:architecture`.
- Short. Bullets. No narrative. At most 120 lines, Mermaid blocks excluded.

## Output

Read `${CLAUDE_PLUGIN_ROOT}/skills/product/template.md` and use its structure.

1. Show a concise summary in chat, at most 6 bullets: purpose, actors, core features, supportive areas, terms with unknown meaning, questions.
2. Propose the paths `.ai/onboarding/product-brief.md`, `.ai/onboarding/glossary.md` and `.ai/onboarding/open-questions.md`. Say which exist and will be overwritten. Say the user can ask for the full content before approving.
3. Wait for approval. Write nothing before approval. If the caller already approved the directory, skip the wait.
4. Write `product-brief.md` and `glossary.md`.
5. `open-questions.md`: if missing, create it with the sections `## Overview`, `## Product`, `## Architecture`, each with `### Questions` and `### Follow-ups` set to `none`. Then overwrite only the `## Product` section. Each question has one line on why it matters. Each follow-up is a fact with its evidence path or URL.
6. If the user declines, keep the summary in chat only.
