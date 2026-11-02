# Presets

A preset fixes the depth of every phase. `brief` proposes one, the user confirms or edits it, and the charter carries the chosen rows as explicit depth lines. Later phases read the charter, never this file.

## Table

| | `mvp` | `full` | `decision` |
|---|---|---|---|
| Purpose | start building fast | a design the team implements over several iterations | choose between approaches for one concern |
| Horizon | MVP | v1 | current or target state |
| Requirements | core operations and hard constraints; later features as a bare list | all features by horizon, actors, constraints | criteria only: constraints and quality scenarios; actors and core operations only when the decision depends on them; no features table, no diagram |
| Quality scenarios | only those that change the architecture | measurable scenarios for every quality that matters | the criteria to weigh the options |
| Architecture | one recommended option plus one alternative; containers only | 2 or 3 options; containers and key components; operation paths; cross-cutting | options compared against the criteria, then stop after the pick |
| Technology | what is needed to start, each pick with an alternative | main technology per container, each pick with an alternative | only if the decision is about it |
| Review | blockers and hard-to-reverse decisions | full | criteria complete, every option scored against every criterion, missing options |
| Rounds per phase | 1 | 2 | 1 |
| Done when | every core operation has a path, no blocker open | every requirement marked `now` has an owner in the design, no blocker open | one option chosen and recorded with reasons |

## Choosing

- Propose the preset whose Purpose matches what the user wants from the design. Give the reason in one line.
- Show every option with its Horizon and Done when line, so the user can pick without reading this file.
- `custom`: start from the nearest preset and let the user edit any row in a second round. Every row needs a value.

## Writing the charter

Copy the chosen rows into the charter depth lines, one per row: requirements, quality scenarios, architecture, technology, review, rounds. Copy Horizon and Done when into their own lines. The user may edit any line; the edited text is what later phases follow.
