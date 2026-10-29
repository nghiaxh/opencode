# AGENTS.md - Global Agent Instructions

Global baseline for every session. Project and nearer `AGENTS.md` files add local rules; explicit user instructions win conflicts. Keep this file universal: project facts, commands, and stack rules belong in the project's own `AGENTS.md`.

OpenCode V2 auto-loads `AGENTS.md`; it does not read `CLAUDE.md`. Nested files load when the agent reads that area.

## Hard boundaries

- Never read, print, or parse `.env`, `.env.*`, or files containing keys, tokens, credentials, or private configuration. Reference variable names only; ask for values or run commands that consume them without printing contents. Never commit secrets, keys, or tokens.
- Never run a destructive command (`rm -rf`, `git reset --hard`, `git clean -fd`, force push) without showing it and getting a yes.
- Never hand-edit generated, vendored, or lock files. Run the generator and include the lockfile update.
- Validate at trust boundaries; escape output for its context. Never cut validation, data-loss handling, security, or accessibility for a smaller diff. No `sudo`, global installs, or broad permissions.
- For web or service code, use TLS, security headers, safe logs, and a dependency audit.

## Default loop

1. Read the code the change touches. Search the repo and trace the callers before editing.
2. Fix the root cause in the shared path, not only the reported path.
3. Make the smallest complete change. No drive-by refactors, no scaffolding.
4. Run the narrowest relevant check. Report what ran and what did not.

Ask one question only when two readings change scope or public behavior. Otherwise act.

## Simplicity ladder

Stop at the first rung that holds:

1. Does this need to exist? If no, skip it.
2. Already in this codebase? Reuse it.
3. Standard library does it? Use it.
4. Platform feature does it? Use it.
5. Installed dependency does it? Use it.
6. One line? One line.
7. Only then: the minimum that works.

The ladder shortens the solution, not the reading. The smallest change in the wrong place is a second bug; when two options cost the same, take the one correct on edge cases.

## Change discipline

- No unrequested abstraction: no interface with one implementation, no factory for one product, no config for a value that never changes.
- No compatibility shims, `_v2` files, or deprecated aliases unless asked.
- Prefer editing an existing file over creating a new one; check neighboring files for conventions.
- Delete dead code; do not comment it out.
- Handle errors explicitly; no silent catches.
- Mark a deliberate shortcut with a plain comment that states what it skips and when it stops working.
- An explicit request wins. Ship the full requested scope; if it is too large, name the deferral and ask, never silently substitute a partial version.
- Follow existing repo conventions. If they conflict with this file, follow the repo and report the conflict.

## Context and APIs

1. Pin the version from the lockfile, or the manifest range if none.
2. Read the installed types or source.
3. Otherwise read the vendor docs for that version.
4. If every source fails, name what failed, follow existing repo usage, and mark `docs unverified`.

State the version and source when they change the answer. Skip this for standard-library or platform APIs already used in the repo.

## Verification

- Find the check in README, the manifest, Makefile, justfile, or CI. Run the narrowest command that covers the change.
- Never invent a check. Never report a pass unless it ran.
- Do not build a test framework, fixtures, or a suite unless asked; use the project's existing runner.
- Report the command, the result, and the paths it did not cover.
- Done means: the affected check ran, the diff holds no secrets, and there are no unrelated changes.

In `~/.config/opencode`, there is no test, lint, or typecheck command; use `git diff` and a secret-pattern scan.

## Git

Format: `<type>[optional scope]: <description>`, lowercase, imperative, no period.

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `ci`, `build`, `chore`, `revert`

One commit per change type. Merge commits follow the same format.

Do not commit, push, or rewrite history unless asked.

## Routing

### Commands

Slash commands live in `~/.config/opencode/commands/` globally and `.opencode/commands/` per project. Use the matching command; do not reimplement its steps in the session.

| Command | Use when |
|---|---|
| `/lazy-ask` | Asking what you actually want, in rounds, before any code is written |
| `/lazy-commit` | Splitting working-tree changes into separate conventional commits per change type |
| `/lazy-audit` | Scanning the whole repo for deletable debt, delete-first, read-only |
| `/lazy-polish` | Polishing the code that stays: names, flow, shape, boundary, scale, fit, depth, locality, reuse |
| `/lazy-test` | Running the check that exists and naming the paths it did not cover |

`/lazy-ask` turns a want into settled decisions, then `/lazy-audit` cuts code, `/lazy-polish` polishes what survives, `/lazy-test` proves it, `/lazy-commit` splits it. They do not overlap.

### Skills

OpenCode discovers skills from `~/.config/opencode/skills` globally and `.opencode/skills` per project; `~/.agents/skills` and `~/.claude/skills` are compatibility sources with lower precedence. `npx skills add <repo> -s <name> -g -a opencode` installs into `~/.config/opencode/skills`. Load one with the `skill` tool using its **ID**, the installed directory name, not the frontmatter `name`. Never guess the ID from the display name.

Review third-party skills and dependencies before installing; prefer audited sources.

Frontend and accessibility rules live in the installed skills, not here. Load the matching skill; if missing, say so and continue without inventing its content.

## Output and language

- Code first, then at most three short lines. A requested report, plan, or walkthrough gets full detail.
- No AI slop words: `delve`, `leverage`, `robust`, `seamless`, `unlock`, `elevate`, `streamline`, `tapestry`, `holistic`, `meticulous`, `it's important to note`.
- No em dashes. No unnecessary comments. Names say what they are.
- Respond in English or Vietnamese; use Vietnamese if the user does.
- Never include Chinese characters.
