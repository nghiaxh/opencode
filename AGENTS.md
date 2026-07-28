# AGENTS.md — Global Agent Instructions

## Windows Environment

- PowerShell only — never Bash/Zsh
- Use semicolons `;` to chain commands, never `&&`
- Use `$?` to check previous command success
- Paths with spaces must be double-quoted
- Backslashes `\` for native PowerShell paths

## Core Principles

- Search first, always use grep/glob/websearch before guessing
- Follow existing code conventions in the target repo
- No AI slop patterns (no "delve", "leverage", "robust", "seamless")
- No unnecessary comments, code should be self-documenting
- No generic variable names (temp, data, result) without context
- Respond in English or Vietnamese; use Vietnamese if user types Vietnamese
- Never include Chinese characters in responses

## Code Quality

- Handle errors explicitly — no silent catches
- Prefer specific error types over generic `Error`
- Don't abstract prematurely — duplicate first, extract when pattern is clear
- Keep functions focused — one responsibility per function
- Prefer `const` over `let`, never `var`
- Validate inputs at system boundaries

## File Organization

- Prefer editing existing files — never create new files unless required
- Check neighboring files for conventions before creating new ones
- Config files: keep in project root or standard locations (`.config/`, etc.)
- Delete dead code rather than commenting it out

## Testing

- Check README or `package.json`/`Cargo.toml`/`pom.xml` for test commands before assuming
- Run tests after completing changes if a test suite exists
- Don't add tests unless explicitly asked
- Use colocated test files when the project convention does so

## Git Conventions (Conventional Commits)

Format: `<type>[optional scope]: <description>` — lowercase, imperative mood, no period.
Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `ci`, `build`, `chore`, `revert`
Project-level AGENTS.md may override with project-specific conventions.

## Security

- Never commit secrets, API keys, or tokens
- Validate all user inputs at system boundaries

## Verification

Before completing any task:
- Code follows project conventions (check neighboring files first)
- No AI slop patterns in output
- No secrets, keys, or tokens exposed
- Git commit follows conventional format (if committing)
- Lint/typecheck passes (if available: `npm run lint`, `npm run typecheck`)
- Tests pass (if test suite exists)
- No unnecessary comments added
