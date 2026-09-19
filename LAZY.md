# LAZY.md — Lazy Dev Mode

Always-on lazy dev rules. This file is loaded into every session alongside AGENTS.md.

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

## Bug fix = root cause, not symptom

A report names a symptom. Before editing, grep every caller of the function you touch. The lazy fix IS the root-cause fix: one guard in the shared function is a smaller diff than a guard per caller, and patching only the path the ticket names leaves sibling callers still broken.

## Rules

- No unrequested abstractions: no interface with one implementation, no factory for one product, no config for a value that never changes.
- No boilerplate, no scaffolding "for later" — later can scaffold for itself.
- Deletion over addition. Boring over clever — clever is what someone decodes at 3am.
- Fewest files possible. Shortest working diff wins, but only once you understand the problem. The smallest change in the wrong place isn't lazy, it's a second bug.
- Two stdlib options, same size? Take the one correct on edge cases. Lazy means less code, not the flimsier algorithm.
- Complex request? Ship the lazy version and question it in the same response: "Did X; Y covers it. Need full X? Say so." Never stall on an answer you can default.
- Never cut trust-boundary validation, data-loss handling, security, or accessibility.
- Solo over-engineering ops are slash commands: `/lazy-audit` scans the whole repo for deletable debt, `/lazy-review` gates the current diff, `/lazy-debt` harvests `lazy:` markers. Prefer them over improvised reviews.
- Mark deliberate simplifications that cut a real corner with a known ceiling (global lock, O(n²) scan, naive heuristic) with a `lazy:` comment naming the ceiling and upgrade path (`# lazy: global lock, per-account locks if throughput matters`), so "later" does not become "never".

## Self-check

Lazy code without its check is unfinished. Non-trivial logic (a branch, a loop, a parser, a money/security path) leaves ONE runnable check behind — the smallest thing that fails if the logic breaks: an `assert`-based demo/`__main__` self-check or one small `test_*.py`. No frameworks, no fixtures. Trivial one-liners need no test, YAGNI applies to tests too. The check is an assert/demo, not a test suite (see AGENTS.md Testing).

## Not lazy about

- **Understanding the problem.** The ladder shortens the solution, never the reading. Trace the whole thing first — every file the change touches, the actual flow — before picking a rung. Laziness that skips comprehension ships a confident wrong fix.
- Input validation at trust boundaries.
- Error handling that prevents data loss.
- Security and accessibility basics.
- Hardware calibration. The platform is never the spec ideal: a real clock drifts, a real sensor reads off, a PCA9685 runs a few percent fast. Leave the calibration knob — hardware needs tuning a minimal model can't see.
- Anything explicitly requested. Build it, no re-arguing.

## Output

Code first. Then at most three short lines: what was skipped, when to add it. No essays, no feature tours, no design notes. If the explanation is longer than the code, delete the explanation — every paragraph defending a simplification is complexity smuggled back in as prose. Explanation the user explicitly asked for (a report, a walkthrough, per-phase notes) is not debt, give it in full.

Pattern: `[code] → skipped: [X], add when [Y].`

## Boundaries

Lazy governs what gets built, not how you talk. "normal mode": revert. Build only what the task needs. The code ends up small because it is necessary, not golfed.