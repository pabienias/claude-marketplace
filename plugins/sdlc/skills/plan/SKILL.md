---
name: plan
description: Creates a phased implementation plan from the context discovery result. Runs a short Q&A to remove assumptions, then writes implementation-plan.md.
when_to_use: Use when the user wants an implementation plan for a ticket, task or feature, or asks to plan the work, break it into phases or steps, or prepare it for implementation. Needs context-discovery.md from sdlc:context-discovery. Do not use for gathering context, writing code or implementing.
argument-hint: "[path to context-discovery.md | feature name] [extra notes, links, files]"
allowed-tools: Read, Glob, Grep, Agent, WebFetch, Bash(git branch:*)
---

# Implementation Plan

Turn discovered context into a plan of small, reviewable phases. Output is the input for implementation.

Do not write code. No function bodies, signatures or snippets. Pseudocode only when a concept is hard to explain otherwise. File paths are allowed under the rule in Plan rules.

## Inputs

Arguments: $ARGUMENTS

1. Find `context-discovery.md`: the path in the arguments, else `.ai/dev/<name>/context-discovery.md` for the ticket key, name or current branch. Read it fully.
2. If it does not exist: stop. Tell the user to run `sdlc:context-discovery` first. Do not plan from scratch.
3. Read every extra file or link in the arguments. Record anything unreadable as an open question.
4. Do not repeat discovery. Treat its Confirmed Facts, Constraints and Likely Impacted Areas as given.

## Readiness gate

Read `Planning Readiness` in the discovery file.

- `Ready for planning`: go on.
- `Partially ready`: go on. Copy its assumptions, risks and open questions into the plan. Resolve as many as possible in Q&A.
- `Not ready for planning`: stop. List the missing inputs and questions. Suggest re-running discovery. Continue only if the user explicitly overrides, and then mark every dependent item as an assumption.

## Process

1. **Summarize.** Give 3-5 bullets: the goal, scope, and what you take from discovery.
2. **Close scan gaps.** Skip this if impacted areas are already known. If a gap remains, delegate to the `codebase-context-scanner` subagent with the exact question and paths. Ask the user first if the scan would be broad. Use the result as notes only.
3. **Q&A.** See below.
4. **Draft.** Read `${CLAUDE_PLUGIN_ROOT}/skills/plan/template.md` and build the plan with it.
5. **Review and write.** See Output.

## Q&A

- Start from the discovery Open Questions and Assumptions. Do not re-ask what discovery already confirmed.
- Ask 3-5 questions per round. Group related ones.
- Give each question a recommended answer with a short reason, so the user can approve or override.
- Cover what is still unknown: scope boundaries (MVP vs later), main flows, data, integrations, edge cases and failures, non-functional needs, test expectations, rollout.
- Ask where the guidelines are (coding, architecture, testing) if discovery did not find them. Do not assume them. Record them under Guidelines to follow.
- After each round, list the decisions made.
- Stop when you can write the plan with no hidden assumptions, or when the user says to wrap up. Unresolved items go to Assumptions or Open Questions.
- If context is already complete, propose skipping Q&A. Skip only if the user agrees.

## Plan rules

- Business language. Say what the user or system gains, not how to code it.
- Each phase is a small, independently reviewable and testable increment. Order phases by dependency.
- Each phase has its own acceptance criteria. They must be testable and specific. Where useful, trace them to requirements from discovery.
- Each phase states expected tests (kinds and level), not test code.
- Steps are plain items inside a phase. Name the affected area for each step.
- Paths: name a file or directory only if discovery or a scan confirmed it exists. Mark a file that does not exist yet as `new`. If a step touches many files, name the directory or module. Never guess a path.
- Scale to the task. A small change is 1-2 phases. An end-to-end feature is 4-6.
- Stay in scope. No refactors or adjacent work unless the feature requires them (or user asks explicitly).
- Facts and instructions. Reasoning only where it is needed to understand a decision.
- Be technology and framework agnostic. Follow the guidelines the user pointed to.
- Add a diagram only if it clarifies something text cannot.

## Output

1. Show the plan in chat, with phase count.
2. Use the directory of `context-discovery.md`. If it is not in `.ai/dev/`, use the ticket key, else the user's name, else the current branch name.
3. Propose the path `<dir>/implementation-plan.md`. If the file exists, say it will be overwritten and offer to revise it instead.
4. Wait for approval. Write no file before approval.
5. If the user asks for changes, apply them, show the result and ask again.
6. After approval, write the file.
7. If the user declines, keep the plan in chat only.

## Revising an existing plan

On revise, read the existing plan and the new context. Propose only the changes, apply them after approval, and keep unchanged parts as they are.

## Guideline attachments

Only if a topic is too detailed for the plan but needed for implementation (for example an interface contract or data notes), create `<dir>/guidelines/<topic>.md`. Link it from the plan. Do not create attachments in advance. No code.
