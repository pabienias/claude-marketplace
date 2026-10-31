# onboarding

Project onboarding: tech stack, product brief, architecture and open questions from a read-only scan.

## Install

```
/plugin marketplace add <path-or-repo-of-this-marketplace>
/plugin install onboarding@pb-claude-marketplace
```

## What it produces

All files go to `.ai/onboarding/`, written only after approval. Nothing else in the repository is touched.

| File | Content |
|---|---|
| `overview.md` | Entry point. What the product is, system boundary, units, where things are, documentation index. Documented facts only. |
| `tech-stack.md` | Stack by layer, infrastructure, tooling, environment variables of interest. |
| `product-brief.md` | Purpose, actors, core features, supportive areas, core operations, external systems, scope, glossary, diagrams. |
| `architecture.md` | Style, units and boundaries, module map, how a feature is added, cross-cutting concerns, deployment, diagram. |
| `open-questions.md` | What could not be inferred, and observations, per phase. |

## Contents

| Component | Type | Purpose |
|---|---|---|
| `onboarding:overview` | skill | Inventories the repository with the `repo-surveyor` and `docs-reader` agents, writes `overview.md` and `tech-stack.md` after approval. Documented facts only. Foundation for `product` and `architecture`. |
| `onboarding:repo-surveyor` | agent | Read-only inventory: units, signal files, stack, infrastructure, environment variable names. No source reads. Used by `overview`. |
| `onboarding:docs-reader` | agent | Indexes repository docs and follows external links one level deep. One line per doc. Used by `overview`. |
| `references/scan-budget.md` | reference | Read tiers, caps and stop rules every agent follows. |
| `references/signals.md` | reference | Where to look before reading source: signal files, never-read list, grep hints. |

## Permissions

Skills pre-approve read-only tools and `git ls-files`, `git rev-parse` and `git remote`. Writes to `.ai/onboarding/` follow your own permission settings. To reduce prompts, allow `Write(.ai/onboarding/**)` and `Edit(.ai/onboarding/**)`. `WebFetch` prompts per domain as usual. Agents inherit your settings.

## Rules

- Read-only outside `.ai/onboarding/`. No edits, no suggested changes. Observations go to `open-questions.md` as facts.
- Every claim cites a reference. Claims inferred from names or structure are marked `(inferred)`.
- Documentation is pointed to, never copied. External links are followed one level deep.
- Technology agnostic. `references/signals.md` holds the only technology names, as examples.
