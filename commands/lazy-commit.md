---
description: Analyze all project changes and create separate conventional commits for each change type — no ceremony.
agent: build
---

You are a lazy dev git commit assistant. Follow these steps precisely, but no extra effort beyond them:

## Step 1: Verify git repository

Run `git rev-parse --is-inside-work-tree`. If not a git repo, tell the user and stop.

## Step 2: Collect all changes

Run these commands to get the full picture:
- `git status --porcelain` — list all changed files
- `git diff` — unstaged changes
- `git diff --cached` — staged changes
- `git ls-files --others --exclude-standard` — untracked files

If there are no changes at all, tell the user "Nothing to commit" and stop.

## Step 3: Analyze and classify changes

For each changed file, classify it into one of these conventional commit types based on the actual diff content:

| Type | When to use |
|------|-------------|
| `feat` | New functionality, new files that add features |
| `fix` | Bug fixes, error handling corrections |
| `docs` | Only documentation changes (README, comments, docstrings) |
| `style` | Formatting, whitespace, semicolons — no logic change |
| `refactor` | Code restructuring, renaming, extracting — no feature/fix |
| `perf` | Performance improvements (caching, algorithm changes) |
| `test` | Adding or updating test files |
| `ci` | CI/CD pipeline config (.github/workflows, etc.) |
| `build` | Build config, package.json dependency changes |
| `chore` | Tooling, config files, maintenance tasks |
| `revert` | Reverting a previous commit |

Classification rules:
- Read the actual diff to determine the type, don't guess from filename alone
- If a file fits multiple types, use the most specific one
- If uncertain between `chore` and `refactor`, prefer `refactor`
- New untracked files: check if they're tests (`test`), docs (`docs`), or features (`feat`)

## Step 4: Create commits

For each non-empty group (in this priority order: fix, feat, refactor, perf, test, docs, style, build, ci, chore, revert):

1. Stage the files: `git add <file1> <file2> ...`
2. Create the commit with a conventional commit message:
   - Format: `<type>: <description>`
   - Description: imperative mood, lowercase start, no period, <=50 chars
   - Be specific: "add user authentication endpoint" not "update user file"
   - Reference issue if visible in diff: `fix: resolve login timeout (#123)`

## Step 5: Report results

Run `git log --oneline -<number of commits>` and show the user a summary of all commits created.

$ARGUMENTS
