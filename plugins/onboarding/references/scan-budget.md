# Scan budget

Rules for every agent that reads the repository. The goal is a shallow, cheap read: names and signal files first, source last.

## Tiers

Move to the next tier only when the previous one leaves a question open that you can name.

1. **Inventory.** Never print the full file list. Count: `git ls-files | wc -l`. Tree to depth 3: `git ls-files | cut -d/ -f1-3 | sort -u`. Weight per directory: `git ls-files | cut -d/ -f1-2 | sort | uniq -c | sort -rn | head -50`. Manifests at any depth: `git ls-files | grep -E '(^|/)(<manifest names from signals.md>)$'`. Narrow with a path: `git ls-files -- <dir>`. Not a git repository: Glob with the never-read list from `signals.md` excluded.
2. **Signal files.** Manifests, build, CI, container, infrastructure, environment examples, docs. Read fully. They do not count toward the budget. See `signals.md`.
3. **Names.** Directory, file, route string, class, entity, migration, event, queue and environment variable names. Write down what the names tell you before reading anything.
4. **Grep.** Patterns from `signals.md`. Start with `files_with_matches` or `count`. Then `content` with `head_limit` 30 and no context lines.
5. **Sampled reads.** At most 15 source files per unit. First 80 lines each. Only entry points, module indexes and composition roots.

## Never read

Lockfiles, dependency and vendor directories, build output, generated code, minified assets, binaries, fixtures, snapshots, test bodies, data files, translation bundle bodies (grep them for labels instead). Full list in `signals.md`.

## Docs and links

- A doc of at most 300 lines: read fully. Longer: headings (`grep -n '^#'`) and the first 100 lines.
- External link: follow once with an available tool, read the linked page only, never recurse. Unreadable: record the reason.

## Stop

Stop when every top-level directory of every unit has a one-line purpose and the questions in your brief are answered. Do not read to confirm what a name already says. When the budget is spent, report what is unknown. Never exceed it.

## Evidence

Every finding carries its evidence: `path`, `path:line` or URL. A finding from names or structure, not from a statement in a doc or by the user, is marked `(inferred)`. No evidence, no finding.
