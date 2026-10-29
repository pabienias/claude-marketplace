---
name: submit
description: Drafts the title and description of a merge request or pull request from the branch diff and the sdlc artifacts, shows them for approval, then pushes the branch and creates the MR/PR. Platform agnostic.
when_to_use: Use when the work on a branch is done and the user wants to open a merge request, pull request, MR or PR, or asks to write or update its description. Works best after sdlc:review. Do not use for reviewing or changing code.
argument-hint: "[feature name | path to .ai/dev/<name>/] [target branch, ticket link, references, extra notes]"
allowed-tools: Read, Glob, Grep, AskUserQuestion, ToolSearch, WebFetch, Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git branch:*), Bash(git remote:*), Bash(git rev-parse:*)
disable-model-invocation: true
---

# Submit

Write the MR/PR title and description, get approval, then create the MR/PR. The template is a starting point. Add, drop or adapt sections to fit the scope.

## Preconditions

- The project is a git repository and the current branch is not the target branch. If not, stop.
- The working tree is clean. If not, ask: commit, stash or stop.
- If `implementation-review.md` exists and its status is `changes requested` or `blocked`, say so and ask: continue or stop.

## Inputs

Arguments: $ARGUMENTS

1. Platform: infer from the git remote and the context. Find its CLI or a connected tool. If none is available, you will hand the draft to the user.
2. Target branch: the one given in the arguments, else the repository default branch.
3. Artifacts: read `.ai/dev/<name>/` for the given name or the current branch, if present: `context-discovery.md`, `implementation-plan.md`, `implementation-summary.md`, `implementation-review.md`.
4. Changes: the commits and the diff against the target branch.
5. Ticket: from the arguments, the artifacts or the branch name. Verify the link. Never build a link from a ticket key.
6. Project template: look for an MR/PR template in the repository. If one exists, use it and fit the sections of `${CLAUDE_PLUGIN_ROOT}/skills/submit/template.md` into it.
7. If an MR/PR for the branch already exists, offer to update its description instead.

## Process

1. **Draft.** Write the title and description from `${CLAUDE_PLUGIN_ROOT}/skills/submit/template.md`.
   - Overview, Ticket and Key changes: from discovery, plan and diff.
   - Deviations and Decisions: from the summary and the review decisions.
   - Worth testing: from the plan acceptance criteria and the diff.
   - Impact: from the diff (migrations, config, flags, breaking changes) and the follow-ups in summary and review.
   - References: from the arguments and the artifacts.
2. **Approve.** Show the title and description. Ask with `AskUserQuestion`: create, create as draft, change, or stop. Apply changes and ask again.
3. **Create.** Push the branch if needed. Create the MR/PR against the target branch with the approved title and description. Without a CLI or tool, give the user the final text.
4. **Hand-off.** Show the MR/PR link.

## Rules

- Never edit source files or commit.
- Every link is user-provided or verified. Leave out what you cannot verify.
- High level. Name files, classes and core functions. No code and no line-by-line changes.
- Write a conditional section only when it has content.
- Every question goes through `AskUserQuestion`.
- Short sentences. Facts only.
