---
name: architecture
description: Writes architecture.md in .ai/onboarding/ - system role and internal structure, units and boundaries, product-to-code module map with start-here paths, how a feature is added, cross-cutting concerns, deployment, two diagrams - from docs and a read-only scan by the architecture-analyst agent. Runs onboarding:overview first when overview.md is missing.
when_to_use: Use when the user asks how the project is structured, what architecture style it has, where a product area lives in the code, or how features are typically added. Not for a question about one module's internals, and not for product scope (onboarding:product).
argument-hint: "[focus area] [links, files, notes]"
allowed-tools: Read, Glob, Grep, Agent, AskUserQuestion, ToolSearch, WebFetch, Skill, Bash(git rev-parse:*), Bash(git log:*)
---

# Architecture

Describe how the project is structured: style, units and their boundaries, where each product area lives, how a feature is typically added, what crosses boundaries, how it deploys. Output is `architecture.md`. Inference is allowed here. Every inferred claim is tagged.

You MUST NOT recommend, assess quality, or edit outside `.ai/onboarding/`.

## Inputs

Arguments: $ARGUMENTS

- Optional from the user: the area they will work on first (focus area), links, files, notes. Record them as reference `User, chat`.
- Foundation: `.ai/onboarding/overview.md`. If missing, say so and invoke `onboarding:overview` with the Skill tool first. It asks its own approval. Then continue.
- Read from `overview.md`: System boundary, Units, Where things are, Documentation rows tagged `architecture` or `tech`.
- Read from `tech-stack.md`: Infrastructure, for the Deployment section; Environment, for signals of other systems.
- Read from `product-brief.md` if present: Core features and Supportive areas are the module map's areas; the Core operations (up to 3) are traced in the operation diagrams. If missing, use unit and top-level directory names as areas, trace the focus area instead, and record a follow-up.
- Called by `onboarding:onboard`: intake facts, Q&A answers and the approval are given. Skip step 3 and the wait in Output.
- References: `${CLAUDE_PLUGIN_ROOT}/references/scan-budget.md` and `${CLAUDE_PLUGIN_ROOT}/references/signals.md`. Pass both absolute paths to the agent.

## Process

1. **Commit.** `git rev-parse --short HEAD` for the document header. Not a git repository: write `no git`.
2. **Foundation.** See Inputs.
3. **Ask.** Up to 2 questions with `AskUserQuestion`, one call, only for what the inputs leave open. Every question has an "I don't know" option, which becomes a question in `open-questions.md`.
   - Focus: "Which area will you work on first?" Options from units or areas, plus none.
   - System boundary, only when `overview.md` says `unknown`: "Is this repository the whole system?" Options: whole system, part of a larger system, I don't know.
4. **Delegate.** Run `architecture-analyst` with: units, where things are, docs tagged `architecture` or `tech` with paths or URLs, product areas, the core operations (up to 3), focus area, reference paths.
5. **Dates.** Per module map row, the most recent `git log -1 --format=%cs -- <path>` over its `Where` paths. Dates only, no judgment. Not a git repository: `unknown`.
6. **Synthesize.** Fill the template.
   - System role. What this repository is within the whole system. `System boundary` in `overview.md` comes first (documented), then the agent's runtime dependencies on other systems (inferred): environment names pointing at hosts, clients, topics, contracts, deployment siblings. Any of those makes the repository one part of a larger system, never a monolith. Weak evidence: `unclear` and a question.
   - Internal structure. Per unit, from the agent: by layer, by feature, flat, mixed. Weak evidence: `unclear`. Never pick a pattern name for completeness; "structured by feature" is a valid answer.
   - Module map. One row per product area. `Where` holds paths. `Start here` holds 1 to 3 paths, up to 5 for the focus area, whose row comes first. `Last change` holds the date from step 5.
   - How a feature is added. The observed pattern only, from 2 or more examples, with their paths. Describe, never instruct.
   - Deployment. Deployables and targets from `tech-stack.md` Infrastructure and the agent.
   - Diagrams. Container level: units, data stores, queues, external systems, main edges; Mermaid `flowchart`, at most 12 nodes. Core operations through the system: one diagram per distinct path from the agent's operation paths, up to 3; operations that share a path share one diagram, named on its title line; the focus area when the brief is missing. Mermaid `sequenceDiagram` or `flowchart`, at most 12 participants each. Labels are names only.
7. **Challenge.** Drop every claim without evidence. Tag `(inferred)` where the evidence is names or structure. Search the draft for "should", "consider", "recommend", "better", "improve"; remove the sentence or turn it into a follow-up phrased as a fact. Check the agent's `Reads` line against the scan budget; a breach goes to Follow-ups.
8. **Approve.** See Output.
9. **Write.** See Output.

## Rules

- Read-only outside `.ai/onboarding/`. Never edit other files. Never suggest changes; an observation becomes a follow-up, phrased as a fact.
- Every claim cites `[[n]](#references)`, a link to the References section. References list every artefact used once, numbered in order of first use. Before writing, check that every citation has an entry and every entry is cited.
- Never present an inference as documented. `unclear` beats a guess.
- Do not copy documentation. Point to it.
- Technology agnostic. The project's own structure defines the vocabulary. No general-pattern advice, no conventions, no guidelines.
- Short. Bullets. No narrative. At most 150 lines, Mermaid blocks excluded.

## Output

Read `${CLAUDE_PLUGIN_ROOT}/skills/architecture/template.md` and use its structure.

1. Show a concise summary in chat, at most 6 bullets: system role, internal structure, units, module map rows, deployment, questions.
2. Propose the paths `.ai/onboarding/architecture.md` and `.ai/onboarding/open-questions.md`. Say which exist and will be overwritten. Say the user can ask for the full content before approving.
3. Wait for approval. Write nothing before approval. If the caller already approved the directory, skip the wait.
4. Write `architecture.md`.
5. `open-questions.md`: if missing, create it with the sections `## Overview`, `## Product`, `## Architecture`, each with `### Questions` and `### Follow-ups` set to `none`. Then overwrite only the `## Architecture` section. Each question has one line on why it matters. Each follow-up is a fact with its evidence path or URL.
6. If the user declines, keep the summary in chat only.
