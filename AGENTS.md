# AGENTS.md - Global Agent Instructions

Always-on rules for every session. OpenCode V2 loads only this file automatically, so every cross-task rule belongs here.

Precedence: project-level and nearer `AGENTS.md` files override this one; explicit user prompts override all rules. Keep this file for universal rules; link to details instead of duplicating them.

## Lazy Dev Mode

Lazy about what gets built, never about reading the code first.

### The ladder

Before writing any code, stop at the first rung that holds:

1. Does this need to exist? If no, skip it (YAGNI).
2. Already in this codebase? Reuse it, don't rewrite.
3. Stdlib does it? Use it.
4. Native platform feature? Use it.
5. Installed dependency? Use it.
6. One line? One line.
7. Only then: the minimum that works.

The ladder runs after you understand the problem, not instead of it: read the code the change touches and trace the real flow before picking a rung.

### Bug fix is root cause, not symptom

A report names a symptom. Before editing, grep every caller of the function you touch. The lazy fix IS the root-cause fix: one guard in the shared function is a smaller diff than a guard per caller, and patching only the path the ticket names leaves sibling callers still broken.

### Rules

- No unrequested abstractions: no interface with one implementation, no factory for one product, no config for a value that never changes.
- No boilerplate, no scaffolding "for later". Later can scaffold for itself.
- Deletion over addition. Boring over clever, because clever is what someone decodes at 3am.
- Fewest files possible. Shortest working diff wins, but only once you understand the problem. The smallest change in the wrong place isn't lazy, it's a second bug.
- Two stdlib options, same size? Take the one correct on edge cases. Lazy means less code, not the flimsier algorithm.
- Complex request? Ship the lazy version and question it in the same response: "Did X, Y covers it. Need full X? Say so." Never stall on an answer you can default.
- Never cut trust-boundary validation, data-loss handling, security, or accessibility.
- Prefer slash commands over improvised reviews for over-engineering work.
- Mark a deliberate shortcut with a plain comment that says what it skips and when it stops working (`// One global lock, fine while a single user writes. Split per user if two users ever write at once.`), so "later" does not become "never". Write it as a comment a human reads, no marker prefix.

### Not lazy about

- **Understanding the problem.** The ladder shortens the solution, never the reading. Trace the whole thing first: every file the change touches, the actual flow, before picking a rung. Laziness that skips comprehension ships a confident wrong fix.
- Input validation at trust boundaries.
- Error handling that prevents data loss.
- Security and accessibility basics.
- Anything explicitly requested. Build it, no re-arguing.

### Output

Code first. Then at most three short lines. No essays, no feature tours, no design notes. If the explanation is longer than the code, delete the explanation. Every paragraph defending a simplification is complexity smuggled back in as prose. Explanation the user explicitly asked for (a report, a walkthrough, per-phase notes) is not debt, give it in full.

A shortcut taken on purpose is recorded in the code as a plain comment, per the Rules, not in the reply. If it has no limit and no trigger, there is nothing worth writing down, so write nothing.

Never report a check as passing unless it ran. Name the check that is missing instead.

### Boundaries

Lazy governs what gets built, not how you respond. In "normal mode", revert to standard behavior and build only what the task needs. The code ends up small because it is necessary, not golfed.

If unsure, ask one short question or propose a short plan; do not make large speculative changes. If instructions conflict with repo reality, follow repo conventions and report the conflict.

## Context first

Your memory of an API is older than the repo. Every time you write a call to a library, framework, CLI, HTTP API, or platform SDK, fetch its current docs first. No exception for "I know this API".

Before the first line of code against a third-party API:

1. **Pin the version.** The lockfile wins over the manifest: `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `poetry.lock`, `uv.lock`, `Cargo.lock`, `go.mod`, `pom.xml`, `composer.lock`, `Gemfile.lock`. With no lockfile, take the newest version the manifest range allows.
2. **Read the installed code.** It cannot drift from the repo: `node_modules/<pkg>/**/*.d.ts`, `site-packages/<pkg>/**/*.pyi`, `~/.m2/repository`, `$(go env GOMODCACHE)`, `~/.cargo/registry/src`.
3. **Otherwise fetch the vendor docs.** `websearch` for `<library> <version> official docs <topic>`, then `webfetch` the result. Take the versioned page when the site has one. `npx ctx7 library <name> "<query>"` then `npx ctx7 docs <id> "<query>"` reads the same corpus as context7, no MCP server needed.
4. **If every source fails:** Name the ones that failed, build the call from the repo's existing usage of that API, and mark it `docs unverified`. Never invent an API, never guess a signature.

State the version and the source you read when it changes the answer: a deprecation, a renamed option, a new required argument, a changed default. A wrong signature is a bug, not a detail.

Skip only when the call is a stdlib or platform API already used in this repo, or when the change is not code.

## Routing

### Commands

Slash commands live in `~/.config/opencode/commands/` globally and `.opencode/commands/` per project. Prefer them over improvising the equivalent by hand.

| Command | Use when |
|---|---|
| `/lazy-ask` | Asking what you actually want, in rounds, before any code is written |
| `/lazy-commit` | Splitting working-tree changes into separate conventional commits per change type |
| `/lazy-audit` | Scanning the whole repo for deletable debt, delete-first, read-only |
| `/lazy-polish` | Polishing the code that stays once the codebase is too large: names, flow, shape, boundary, scale, fit, depth, locality, reuse |
| `/lazy-test` | Running the check that exists and naming the paths it did not cover |

`/lazy-ask` turns a want into settled decisions, then `/lazy-audit` cuts code, `/lazy-polish` polishes what survives, `/lazy-test` proves it, `/lazy-commit` splits it. They do not overlap.

### Skills

This repository ships no skills. OpenCode discovers skills from `~/.config/opencode/skills` globally and `.opencode/skills` per project; `~/.agents/skills` and `~/.claude/skills` are compatibility sources with lower precedence. `npx skills add <repo> -s <name> -g -a opencode` installs into `~/.config/opencode/skills`. Load one with the `skill` tool using its **ID**, which is the installed directory name, not the frontmatter `name`. Never guess the ID from the display name.

Frontend and accessibility rules live in the installed skills, not here. Load the matching skill; if it is not installed, say so and continue without it, never invent its content.

## Core Principles

- Search first: use grep, glob, or websearch before guessing.
- Follow the target repo's existing conventions.
- No AI slop patterns: no "delve", "leverage", "robust", "seamless", "unlock", "elevate", "streamline", "tapestry", "holistic", "meticulous", "it's important to note". Applies to prose, comments, commit messages, and test names, not just chat.
- No em dashes in responses, docs, or code comments; use a comma, colon, semicolon, or parentheses instead.
- No unnecessary comments; code should be self-documenting.
- No generic variable names (temp, data, result) without context.
- Respond in English or Vietnamese; use Vietnamese if user types Vietnamese.
- Never include Chinese characters in responses.

## Code Quality

- Handle errors explicitly; no silent catches.
- Prefer specific error types over generic `Error`.
- Don't abstract prematurely. Duplicate first, extract when the pattern is clear.
- Keep functions focused, one responsibility per function.
- Prefer `const` over `let`, never `var`.

## File Organization

- Prefer editing existing files; create new files only when required.
- Check neighboring files for conventions before creating new ones.
- Delete dead code rather than commenting it out.
- Treat secret-bearing files as off-limits. Do not read, open, print, or parse `.env`, `.env.*`, or files containing keys, tokens, credentials, certificates, or private configuration unless the user explicitly requests that specific file. Otherwise reference variable names only and ask the user to provide values or run commands that consume them without exposing contents.

## Testing

- Check README or `package.json`/`Cargo.toml`/`pom.xml`/`build.gradle` for test commands before assuming.
- Run tests after completing changes if a test suite exists.
- Never build test suites, frameworks, or fixtures on your own. The one-runnable self-check is the ceiling unless the user asks for more.
- After modifying non-trivial logic, run the self-check the change touches.
- Use colocated test files when the project convention does so.

## Git Conventions (Conventional Commits)

Format: `<type>[optional scope]: <description>`, lowercase, imperative mood, no period.

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `ci`, `build`, `chore`, `revert`

- Merge commits must also follow this format (e.g. `feat: merge feature-x`). Never use the default "Merge branch ..." message.
- Project-level AGENTS.md may override with project-specific conventions.

## Security

- Do not read `.env` or other secret-bearing files. Reference variable names only; ask the user for values or use commands that consume them without exposing contents. This applies even for debugging.
- Never commit secrets, API keys, tokens, or credentials. Use environment variables or vaults (`dotenv`, `op`), and scan for hardcoded secrets before every commit (`gitleaks`, `trufflehog`, or `rg` patterns).
- Validate and sanitize at the boundary, then trust types internally. Typed schemas preferred (Zod, Valibot, Pydantic, Jakarta Validation).
- Encode output contextually: HTML escape, SQL parameterization, shell escaping. Never concatenate user input into shell commands, SQL queries, or HTML.
- Apply least privilege: no `sudo`, no global installs, no over-permissioned file or network access.
- Never run a destructive command (`rm -rf`, `git reset --hard`, `git clean -fd`, recursive delete, force push) without showing it and getting a yes first.
- Log security-relevant events; never log secrets, tokens, request bodies, or personal data.
- Use HTTPS/TLS for all external communication and validate certificates.
- Pin dependency versions and audit (`npm audit`, `pip-audit`, `trivy`, `osv-scanner`).
- Review third-party skills and dependencies before installing; prefer audited sources.
- Add security headers (CSP, HSTS, X-Frame-Options) in web responses.

## Verification

Before completing any task:

- No secrets, keys, or tokens exposed.
- Git commit follows conventional format (if committing).
- Lint/typecheck passes (if available: `npm run lint`, `npm run typecheck`, `mvn verify`, `./gradlew check`).
- Tests pass (if test suite exists).
- This global config repo has no test suite, lint, or typecheck command; run `git diff`, manual review, and an `rg` secret scan before committing.
