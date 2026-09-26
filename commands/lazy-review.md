---
description: Review the current changes for quality and size — gate the merge
---

A review, not a hunt. Review the actual diff (git diff plus staged and untracked files). Judge the change against the project's own bar, which is AGENTS.md.

Cover in order:
- correctness: silent catches, error handling, data-loss risk
- security: trust boundaries, injected input, secrets, least privilege
- over-engineering: speculative code, premature abstraction, unrequested surface
- self-check: non-trivial new logic with no runnable check (AGENTS.md Self-check)
- slop: banned AI-slop words (delve, leverage, robust, seamless)

One line per issue: <file>:<line>, <severity>: <issue>. <fix>.
Severity: block (fix first: data loss, security, silent failure), trim (over-engineered — delete/stdlib/native/yagni/shrink), nit (style, naming).

End with a verdict: 'BLOCK — <reason>' | 'Ship after <N> trims' | 'Ship.'