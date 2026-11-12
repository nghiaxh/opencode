---
description: Turn a task into per-branch implementation prompts a fresh agent can run without this conversation
agent: plan
---

Read only. The output is prompts, not code: never edit a file, create a branch, commit, or run a build that writes.

## Task

$ARGUMENTS. Empty: the task already in this conversation, including decisions from /lazy-ask or a report the user pasted. Everything the prompts need must come from the code or the task; nothing is "as discussed above".

## Research

Read the code the task touches and trace its callers before writing a word.

- Cite only paths, symbols, and lines you verified. A path that does not exist is a defect.
- Find the real checks: README, package.json scripts, Makefile, justfile, pom.xml, CI. Never invent a command or a test name.
- Detect the base branch and the branch convention from git and docs. Do not assume a `dev` branch exists.
- Read the repo's AGENTS.md for stack rules the prompt must carry: commit format, secrets, generated files, verification.

## Paths

Every path in the output is relative to the repo root of the session that runs the prompt (`git rev-parse --show-toplevel`), with `/` separators: `src/app/router.ts:42`. No drive letter, no home directory, no absolute prefix. The prompt must survive being handed to a session in another checkout or worktree.

## Seams

Split the task into small branches that can ship alone. Manageability beats one wide branch: two areas, two layers, or two file clusters mean two branches.

- One branch per independently shippable piece, named for intent (`fix/cart-auth-pricing`), cut from the base branch.
- Prefer more, smaller branches over one wide branch. Only a task with a single real seam stays one branch.
- Branches never merge into each other and never depend on each other's prompts. If two prompts would need each other's code, the seam is wrong: reseam, or move the shared work into the branch that ships first.
- Files two branches both edit are shared: order the pull requests to the base branch and name which branch rebases, with the reason.
- Flag work that belongs to another branch's scope: keep it out, name it.

## Each prompt

Self-contained: a fresh session with no access to this conversation must execute it. Test: delete this conversation, does the prompt still stand?

Order, labels translated into the task's language:

1. Role, repo root, stack, branch, base. The repo root is the working directory of the session that runs the prompt.
2. Numbered goal, one line each.
3. READ FIRST: verified file list.
4. WORK: lettered groups, exact edits, including what to remove or rename.
5. CONSTRAINTS: secrets, generated files, schema and enums, scope, UI language, comments.
6. CHECK: existing commands plus the manual flows, and which paths stay unproven.
7. COMMITS: conventional commits in order. Commits only. Never push, merge another branch, merge its own pull request, or rewrite history unless the task says to.
8. FINAL ANSWER: files, commands run, results, what was not covered, commits made.

Quote exact behaviour, not intent: "quote the submitted item list, not the stored cart", not "fix pricing". State the reason for a non-obvious instruction in one clause, so the implementer does not undo it.

## Open decisions

The task will be ambiguous somewhere. Do not ask and do not stop: list each assumption and each decision the implementer must settle, with the default the prompt assumes.

## Output

In the language of the task, in this order:

1. Findings: one line per area, `<repo-relative path>:<line>, <what is there>`. Facts only.
2. Branch table: branch, scope, main files (repo-relative paths), planned commits.
3. Git flow: base branch, branch commands, pull request order, and which branch rebases.
4. Open decisions.
5. One `~~~text` block per branch, in full. Triple backticks inside stay intact.
6. One closing line: no branch or file touched, and which branch to start.

Boundaries: research and writing only. Running the prompts, creating branches, and editing code belong to the session the prompts are handed to.
