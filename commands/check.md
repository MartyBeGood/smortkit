---
description: Drift detector. Diff the active initiative's SPEC.md (+ CONSTITUTION.md) against code. Read-only, zero writes.
argument-hint: [§V | §I | §T | --all]
---

Invoke the **check** skill (`skills/check/SKILL.md`). Treat `$ARGUMENTS` as the
scope (`§V` default, `§I`, `§T` — active initiative + constitution, or `--all`
for the full sweep: every section, across every open initiative). Read-only —
classify each item HOLD/VIOLATE/UNVERIFIABLE (or MATCH/DRIFT/MISSING/EXTRA for
§I), cite file:line evidence, end with remedy hints. Writes nothing. Run it
after each build.
