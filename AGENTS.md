# AGENTS.md — Global Agent Instructions

## Windows Environment

- Use PowerShell commands, not Bash/Zsh
- Use `Get-ChildItem` instead of `ls`/`find`
- Use `Select-String` instead of `grep`
- Use `Test-Path` instead of `test -f`
- Use `Set-Location` instead of `cd`
- Use `Remove-Item` instead of `rm`
- Use `Copy-Item` instead of `cp`
- Use `Move-Item` instead of `mv`
- Use `Get-Content` instead of `cat`/`head`/`tail`
- Use `Set-Content` or `Out-File` instead of `echo >`
- Path separators: use backslashes `\` in native PowerShell
- Enclose paths with spaces in double quotes
- Use semicolons `;` to chain commands, never `&&`
- Use `$?` to check previous command success
- Never use `2>&1`; use `-RedirectStandardError` or `-ErrorAction` instead

## Core Principles

- Search first, always use grep/glob/websearch before guessing
- Follow existing code conventions in the target repo
- No AI slop patterns (no "delve", "leverage", "robust", "seamless")
- No unnecessary comments, code should be self-documenting
- No generic variable names (temp, data, result) without context
- Respond in English or Vietnamese; use Vietnamese if user types Vietnamese
- Never include Chinese characters in responses

## Git Conventions (Conventional Commits)

Format: `<type>[optional scope]: <description>`

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `ci`, `build`, `chore`, `revert`

Rules:
- Imperative mood: "add feature" not "added feature"
- Start lowercase, no period at end
- Subject <=50 chars, body wrapped at 72 chars
- Breaking changes: `feat!: description`

## Security

- Never commit secrets, API keys, or tokens
- Validate all user inputs at system boundaries

## Verification

Before completing any task:
- Code follows project conventions
- No AI slop patterns
- Git commits follow conventional format
- No secrets exposed
