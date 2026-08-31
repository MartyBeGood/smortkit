---
description: Plan-then-execute against SPEC.md. Native Claude Code loop, no sub-agents.
argument-hint: [§T.n | --all | --next]
---

Invoke the **build** skill (`skills/build/SKILL.md`). Treat `$ARGUMENTS` as the
target (`§T.n`, `--next`, or `--all`). Plan in native plan mode, name the exact
test that proves each §V touched (verification contract), read §R for external
facts. If the project has a detectable test setup, execute red→green→refactor —
write that test first, confirm it fails, then implement to green; otherwise fall
back to plain edit-then-verify. Auto-invoke backprop on failure. High blast
radius? Suggest `/sk:review` first. Build only flips §T status; other spec
edits route through spec.
