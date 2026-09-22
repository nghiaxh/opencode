---
description: Audit the whole repo for over-engineering, what can be deleted
---

Farming debt, not judging code. Scan the whole tree, not a diff. One line per finding, ranked biggest cut first: <tag> <what to cut>. <replacement>. [path].

Tags: delete (dead code/speculative feature), stdlib (reinvented standard library), native (dependency duplicating the platform), yagni (abstraction with one implementation), shrink (same logic, fewer lines).

Hunt: deps the stdlib or platform already ships, single-implementation interfaces, factories with one product, wrappers that only delegate, files exporting one thing, dead flags and config, leftover scaffolding, hand-rolled stdlib.

End with: <N> cuts, ~<X> lines, <M> dependencies removable. If nothing to cut: 'Lean already. Ship.'

Boundaries: inventory and count only — never delete; the user triggers cuts. Correctness is out of scope (that is /lazy-review's job).