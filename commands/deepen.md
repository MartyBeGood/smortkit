---
description: Spare-budget design pass. Make one shallow module deep — smaller interface, behavior held, tests green before & after.
argument-hint: [module/path | "improve the design"]
---

Invoke the **deepen** skill (`skills/deepen/SKILL.md`). Pick the single shallowest
module the active initiative's spec touches, diagnose the design defect at
file:line, research a deeper shape (hand the external case to research → local
§R), and propose local §I/§V/§T edits to spec. Behavior is sacred — full suite
green before AND after. Run only when the build is green and there is token
budget to drain.
