---
description: Switch the active initiative. Rewrites .smort/INITIATIVES.md §A only.
argument-hint: [slug]
---

Invoke the **switch** skill (`skills/switch/SKILL.md`). Treat `$ARGUMENTS` as
the target slug — no argument shows `INITIATIVES.md §N` and asks which one.
Validate the slug exists, confirm before switching onto a closed initiative,
rewrite `§A`, echo the initiative's local §G. Touches nothing else.
