---
name: context-discovery
description: Gathers and validates context for a task or ticket before implementation planning. Produces context-discovery.md with a planning readiness status.
when_to_use: Use when the user gives a ticket, task or feature description and wants to start work, or asks to prepare, analyze or discover context before planning. Do not use for planning or coding itself, or when the user only asks a question about the code.
argument-hint: "[ticket key | file path | task text] [extra context, links, files]"
tools: Read, Grep, Glob, Agent, ToolSearch, Bash(jq:*), Bash(grep:*), Bash(sed:*), Bash(head:*)
---

# Context Discovery

Collect and validate context for a task. Output is the required input for implementation planning.

You MUST NOT plan. No phases, steps, code, or solution design.

## Inputs

Arguments: $ARGUMENTS

- Task source: pasted text, file path, ticket key or URL.
- For a ticket key or URL: fetch it with an available tool. If none exists, ask the user to paste it.
- Read every file and link the user provides. If one is unreadable, record it under Source Inputs as unreadable.
- If no task is given, ask for it.

## Source priority

Higher source wins over lower. Flag conflicts between sources.

1. User instructions and context
2. Task or ticket text
3. Files and links given by the user
4. Repository contents
5. Inference (always an assumption)

## Process

1. **Collect.** Read all inputs.
2. **Detect mode.** Check the repository for source code and docs. If it has none or almost none, use greenfield mode: skip step 3, mark Likely Impacted Areas as `unknown (greenfield)`, and focus on goal, scope, constraints, acceptance criteria and open questions.
3. **Scan repository.** If the task text is not enough to locate impacted areas, delegate to the `codebase-context-scanner` subagent. Pass: task summary, key terms, user-given paths. Keep the result as notes only.
4. **Check gaps.** Verify each item below is known. If not, add it to Open Questions or Assumptions.
   - business goal
   - scope and non-goals
   - acceptance criteria
   - constraints (technical, business, deadline)
   - dependencies and integrations
   - impacted areas
   - conflicting inputs
   - guidelines or specs the work needs (coding, architecture, testing, domain). Do not assume them. Ask the user where they are.
5. **Ask.** Ask the user only what blocks or weakens planning. One batch per round, most important first. Apply the answers and re-check. If the user cannot answer, keep it as an open question. Do not loop.
6. **Set status.** See Planning readiness.
7. **Write.** See Output.

## Planning readiness

- `Ready for planning`: context is enough to plan reliably.
- `Partially ready`: planning can start. Known gaps and assumptions remain. List them.
- `Not ready for planning`: context is too weak or unclear. List missing inputs and the questions to answer.

Status and a short reason are always required.

## Rules

- Facts only in Confirmed Facts. Each fact names its source.
- Never present an assumption as a fact.
- Do not invent missing information.
- Repository signals are supporting evidence only.
- Be technology and framework agnostic. Do not add tech-specific advice.
- Keep it short. Bullets. No narrative. Add a diagram only if it clarifies something text cannot.

## Output

Use the structure in [template.md](template.md).

1. Show the result in chat.
2. Choose the directory name: ticket key, else name given by the user, else current branch name. If none applies, ask.
3. Propose the path `.ai/dev/<name>/context-discovery.md`. If the file already exists, say it will be overwritten.
4. Wait for user approval. Create no directory and write no file before approval.
5. After approval, create the directory if needed and write the file.
6. If the user declines, keep the result in chat only.
