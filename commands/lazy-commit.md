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

Read the actual diff, then put each file in one type. AGENTS.md holds the full list and the format rules. A file the diff does not show, an untracked file, is read directly.

- One file, one type. A file carrying two types gets split by hunk.
- Uncertain between chore and refactor: refactor.
- Never commit a file the diff shows holding a secret: keys, tokens, `.env`, service credentials.

## Commit

Order: fix, feat, refactor, perf, test, docs, style, build, ci, chore, revert. For each group, stage its files with `git add <files>`, then commit as `<type>: <description>`, the format AGENTS.md gives. Never `git add -A`, `git add .`, or `git commit -a`: they stage files outside the group. Reference an issue when the branch name or the diff shows one: `fix: resolve login timeout (#123)`.

The index holds the group and nothing else: unstage the rest with `git reset -- <paths>`, the working tree untouched. Changes staged before the run are grouped like any other change and land in the commit of their type.

A file carrying two types is split by hunk: `git add <file>`, `git apply --cached --reverse <patch holding the other type's hunks>`, commit, then `git add <file>` again for what is left.

The patch is written by hand from `git diff`. Two changes of different types within three lines of each other land in one hunk: split that hunk by hand and fix the header counts to match. Write the patch with LF endings, a CRLF patch does not apply. A patch `git apply` refuses is fixed, not worked around by staging the file whole.

## Report

`git log --oneline -<n>`, one line per commit created. Name what stayed unstaged and why, and say the index ends empty.
