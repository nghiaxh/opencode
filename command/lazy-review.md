---
description: Review changes for over-engineering, what can be deleted
---

Review the current code changes for over-engineering only, not correctness. One line per finding: L<line>: <tag> <what to cut>. <replacement>. Tags: delete (dead code/speculative feature), stdlib (reinvented standard library), native (dependency doing what the platform does), yagni (abstraction with one implementation), shrink (same logic, fewer lines). End with the net lines removable. If nothing to cut: 'Lean already. Ship.'

Examples:
- L12-38: stdlib: 27-line validator class. "@" in email, 1 line, real validation is the confirmation mail.
- L4: native: moment.js imported for one format call. Intl.DateTimeFormat, 0 deps.
- repo.py:L88: yagni: AbstractRepository with one implementation. Inline it until a second one exists.
- L52-71: delete: retry wrapper around an idempotent local call. Nothing replaces it.
- L30-44: shrink: manual loop builds dict. dict(zip(keys, values)), 1 line.

Scope: over-engineering and complexity only. Correctness, security, and performance are out of scope.
