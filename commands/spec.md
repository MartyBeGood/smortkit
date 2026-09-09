---
description: Create, amend, or backprop bug into the active initiative's SPEC.md (or CONSTITUTION.md with --constitution). Sole mutator of both.
argument-hint: [bug: <description> | amend <§X.n> [--constitution] | from-code | <idea>]
---

Invoke the **spec** skill (`skills/spec/SKILL.md`). Treat `$ARGUMENTS` as the mode:
an idea → NEW (bootstraps `.smort/` on a fresh project), `from-code` → DISTILL,
`bug: <cause>` → BACKPROP (always local), `amend <§X.n>` → targeted edit on the
active initiative, `amend --constitution <§X.n>` → targeted edit on
`CONSTITUTION.md` (§G/§C only — §V there is promotion-only, via `/sk:review`).
spec is the sole mutator — it also writes the handoff blocks that grill
(§G/§C), research (§R), review (§V, plus promotions), and deepen (§I/§V/§T)
produce. Caveman encoding per FORMAT.md; sectioned ownership; show a diff,
write on OK. §T rows are sliced per FORMAT.md SLICING — one row = one
shippable commit, behavior-shaped, test included; never a stack layer.
