# AGENTS.md - Global Agent Instructions

Always-on rules for every session. In OpenCode V2 this file is the only instruction file loaded automatically, so everything that must apply to every task lives here.

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
- Solo over-engineering ops are slash commands. Prefer them over improvised reviews.
- Mark deliberate simplifications that cut a real corner with a known ceiling (global lock, O(n^2) scan, naive heuristic) with a `lazy:` comment naming the ceiling and upgrade path (`# lazy: global lock, per-account locks if throughput matters`), so "later" does not become "never".

### Self-check

Lazy code without its check is unfinished. Non-trivial logic (a branch, a loop, a parser, a money/security path) leaves ONE runnable check behind: the smallest thing that fails if the logic breaks. An `assert`-based demo or `__main__` guard in the nearest runnable file, or one small `test_*.py`. No frameworks, no fixtures. Trivial one-liners need no test, YAGNI applies to tests too. The check is an assert or demo, not a test suite (see Testing below).

### Not lazy about

- **Understanding the problem.** The ladder shortens the solution, never the reading. Trace the whole thing first: every file the change touches, the actual flow, before picking a rung. Laziness that skips comprehension ships a confident wrong fix.
- Input validation at trust boundaries.
- Error handling that prevents data loss.
- Security and accessibility basics.
- Hardware calibration. The platform is never the spec ideal: a real clock drifts, a real sensor reads off, a PCA9685 runs a few percent fast. Leave the calibration knob, because hardware needs tuning a minimal model can't see.
- Anything explicitly requested. Build it, no re-arguing.

### Output

Code first. Then at most three short lines: what was skipped, when to add it. No essays, no feature tours, no design notes. If the explanation is longer than the code, delete the explanation. Every paragraph defending a simplification is complexity smuggled back in as prose. Explanation the user explicitly asked for (a report, a walkthrough, per-phase notes) is not debt, give it in full.

Pattern: `[code]` then `skipped: [X], add when [Y].`

### Boundaries

Lazy governs what gets built, not how you talk. "normal mode": revert. Build only what the task needs. The code ends up small because it is necessary, not golfed.

## Routing

### Commands

Slash commands live in `commands/`. Prefer them over improvising the equivalent by hand.

| Command | Use when |
|---|---|
| `/lazy-commit` | Splitting working-tree changes into separate conventional commits per change type |
| `/lazy-audit` | Scanning the whole repo for deletable debt, delete-first, read-only |
| `/lazy-review` | Gating the current diff (staged plus unstaged plus untracked) before merge |
| `/lazy-debt` | Harvesting `lazy:` marker comments into a debt ledger |

`/lazy-audit` inventories, `/lazy-review` judges the diff, `/lazy-debt` harvests markers. They do not overlap.

### Skills

No skills live in this repo. Every skill is installed with `npx skills add` into `~/.agents/skills/`, which OpenCode discovers automatically. Load one with the `skill` tool using its **ID**, which is the installed directory name, not the frontmatter `name`. The two often differ: the directory `react-best-practices` declares `name: vercel-react-best-practices`, so the ID is `vercel-react-best-practices`. Never guess the ID from the display name.

| ID | Load when |
|---|---|
| `vercel-react-best-practices` | Writing, reviewing, or refactoring React and Next.js code for performance |
| `java-springboot` | Spring Boot work: project structure, DI, config, REST, JPA, transactions, testing |
| `sql-optimization` | Query tuning, indexing, execution-plan reading, pagination on any SQL database |
| `typescript-advanced-types` | Writing a library, SDK, type-safe API client, or form validation schema. Deep type-level work only, not ordinary app code |

Rules:

- Load **at most one** skill per task. Skills that enforce conflicting styles, such as the design skills in `leonxlnx/taste-skill`, produce incoherent output when stacked.
- `typescript-advanced-types` is hidden from the advertised list. Load it by ID on purpose, it never triggers on its own.
- `vercel-react-best-practices` ships a `rules/` directory of 70 markdown files. Its `SKILL.md` is only the priority overview, so read the specific `rules/` file when a rule matters.
- Installing: `npx skills add <owner/repo> --skill <install name> -g -a opencode --copy -y`. The CLI matches `--skill` against the frontmatter `name`, not the source directory name. Use `--copy`, symlinks break git tracking.

## Core Principles

- Search first, always use grep/glob/websearch before guessing
- Follow existing code conventions in the target repo
- No AI slop patterns (no "delve", "leverage", "robust", "seamless")
- No unnecessary comments, code should be self-documenting
- No generic variable names (temp, data, result) without context
- Respond in English or Vietnamese; use Vietnamese if user types Vietnamese
- Never include Chinese characters in responses

## Code Quality

- Handle errors explicitly, no silent catches
- Prefer specific error types over generic `Error`
- Don't abstract prematurely. Duplicate first, extract when the pattern is clear
- Keep functions focused, one responsibility per function
- Prefer `const` over `let`, never `var`

## File Organization

- Prefer editing existing files, never create new files unless required
- Check neighboring files for conventions before creating new ones
- Config files: keep in project root or standard locations (`.config/`, etc.)
- Delete dead code rather than commenting it out
- `./projects` folder: if the user mentions it (planning a project or just asking a question), do not read any folders inside it

## Testing

- Check README or `package.json`/`Cargo.toml`/`pom.xml`/`build.gradle` for test commands before assuming
- Run tests after completing changes if a test suite exists
- Never build test suites, frameworks, or fixtures on your own. The one-runnable self-check is the ceiling unless the user asks for more
- After modifying non-trivial logic, run the self-check the change touches
- Use colocated test files when the project convention does so

## Web Development

- Validate and sanitize at the boundary, then trust types internally. Parse once at the edge, keep the core total
- Escape on output, every time. Prefer parameterized queries and templating over string concatenation
- Accessibility is not optional: semantic HTML first, keyboard reachable, visible focus, labels tied to inputs, WCAG AA contrast
- Animate only `transform` and `opacity`, and honor `prefers-reduced-motion`
- Load a skill above instead of restating its rules. Do not hand-roll a framework, a router, or a build tool that an installed dependency already provides

## Git Conventions (Conventional Commits)

Format: `<type>[optional scope]: <description>`, lowercase, imperative mood, no period.

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `ci`, `build`, `chore`, `revert`

- Merge commits must also follow this format (e.g. `feat: merge feature-x`). Never use the default "Merge branch ..." message
- Project-level AGENTS.md may override with project-specific conventions

## Security

- Never commit secrets, API keys, tokens, or credentials. Use environment variables or vaults (e.g., `dotenv`, `op`)
- Scan code for hardcoded secrets before every commit (e.g., `gitleaks`, `trufflehog`, or `rg` patterns)
- Validate and sanitize all user inputs at system boundaries. Typed schemas preferred (Zod, Valibot, Pydantic, Jakarta Validation)
- Encode outputs contextually to prevent injection (HTML escape, SQL parameterization, shell escaping)
- Apply least privilege: no `sudo`, no global installs, no over-permissioned file/network access
- Never concatenate user input into shell commands, SQL queries, or HTML. Use APIs, parameterized queries, templates
- Log security-relevant events; never log secrets, tokens, request bodies, or personal data
- Use HTTPS/TLS for all external communication; validate certificates
- Pin dependency versions and audit with `npm audit`, `pip-audit`, `trivy`, `osv-scanner`, or similar
- Add security headers (CSP, HSTS, X-Frame-Options) in web responses
- Review third-party skills and dependencies before installing. Prefer audited sources, and check what a skill actually contains

## Verification

Before completing any task:

- Code follows project conventions (check neighboring files first)
- No AI slop patterns in output
- No secrets, keys, or tokens exposed
- Git commit follows conventional format (if committing)
- Lint/typecheck passes (if available: `npm run lint`, `npm run typecheck`, `mvn verify`, `./gradlew check`)
- Tests pass (if test suite exists)
- No unnecessary comments added
