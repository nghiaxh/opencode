---
description: Split the working tree into one conventional commit per change type
agent: build
---

One commit per change type. No ceremony, no rebase, no push.

## Scope

$ARGUMENTS, when given, narrows the split: a path commits only that path, a type commits only that group. Otherwise every change in the tree.

## Collect

`git status --porcelain`, `git diff`, `git diff --cached`, `git ls-files --others --exclude-standard`. No changes at all: say "Nothing to commit" and stop.

## Group

Read the actual diff, then put each file in one type: fix, feat, refactor, perf, test, docs, style, build, ci, chore, revert. AGENTS.md holds the full list and the format rules.

- One file, one type. A file carrying two types gets split by hunk.
- Uncertain between chore and refactor: refactor.
- Never commit a file the diff shows holding a secret: keys, tokens, `.env`, service credentials.

## Commit

Order: fix, feat, refactor, perf, test, docs, style, build, ci, chore, revert. For each group, `git add <files>`, then commit as `<type>: <description>`: imperative, lowercase, no period, under 50 chars. Reference an issue when the diff shows one: `fix: resolve login timeout (#123)`.

## Report

`git log --oneline -<n>`, one line per commit created. Name what stayed unstaged and why.
