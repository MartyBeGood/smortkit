---
description: One-shot migration from root SPEC.md to the .smort/ layout. Idempotent, shows diff first.
argument-hint: []
---

Invoke the **migrate** skill (`skills/migrate/SKILL.md`). Refuses to run if
`.smort/` already exists or no legacy root `SPEC.md` is found. Splits the old
spec into `CONSTITUTION.md` (§G/§C/§V, all invariants promoted) and
`initiatives/<today>-migrated/SPEC.md` (§G/§C duplicated, §I/§R/§T/§B moved,
§V empty), registers it in `INITIATIVES.md`, shows the full diff before
writing, then stubs the old `SPEC.md` rather than deleting it.
