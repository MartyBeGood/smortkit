---
description: Plan-then-execute against the active initiative's SPEC.md, honoring CONSTITUTION.md. Native Claude Code loop, no sub-agents.
argument-hint: [§T.n | --all | --next]
---

Invoke the **build** skill (`skills/build/SKILL.md`). Treat `$ARGUMENTS` as the
target (`§T.n`, `--next`, or `--all`). Plan in native plan mode, cite both
`CONSTITUTION.md` §V and local §V, name the exact test that proves each
touched (verification contract), read local §R for external facts. If the
project has a detectable test setup, execute red→green→refactor — write that
test first, confirm it fails, then implement to green; otherwise fall back to
plain edit-then-verify. Auto-invoke backprop on failure. High blast radius?
Suggest `/sk:review` first. Build only flips local §T status; other spec
edits route through spec.
