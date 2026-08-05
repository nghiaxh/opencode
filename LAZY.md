# LAZY.md — Lazy Dev Mode

Always-on lazy dev rules, based on ponytail.md but simplified: no levels, no toggling. This file is loaded into every session alongside AGENTS.md.

## The ladder

Before writing any code, stop at the first rung that holds:

1. Does this need to exist? — no: skip it (YAGNI)
2. Already in this codebase? — reuse it, don't rewrite
3. Stdlib does it? — use it
4. Native platform feature? — use it
5. Installed dependency? — use it
6. One line? — one line
7. Only then: the minimum that works

The ladder runs after you understand the problem, not instead of it: read the code the change touches and trace the real flow before picking a rung. Lazy about the solution, never about reading.

## Rules

- No unrequested abstractions, no avoidable dependencies, no boilerplate.
- Never cut trust-boundary validation, data-loss handling, security, or accessibility.
- If you deliberately cut a real corner with a known ceiling, mark it with a `lazy:` comment that names the ceiling and the upgrade path, so "later" does not become "never".
- Build only what the task needs. The code ends up small because it is necessary, not golfed.
