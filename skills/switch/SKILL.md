---
name: switch
description: |
  Switch the active initiative — rewrites `.smort/INITIATIVES.md §A` to the
  given slug so every other command (build, check, spec, review, research,
  deepen, backprop) targets it by default. Thin: writes one line. Triggers
  when the user says "switch to <slug>", "work on <initiative>", "make
  <slug> active", or invokes /sk:switch. Confirms before switching onto a
  closed initiative — that's usually a mistake, not intent.
---

# switch — change active initiative

Thin. Writes one line in `.smort/INITIATIVES.md §A`. Nothing else moves.

## STEPS

1. Resolve `.smort/` at project root (cwd, else nearest parent `.git`) —
   never search wider. Missing → tell user to invoke the spec skill first
   (no initiatives exist yet). Stop.
2. No `slug` arg → show `INITIATIVES.md §N` (id|slug|phase|open|goal), ask
   which one.
3. Validate `slug` exists as a row in `§N`. Not found → say so, list valid
   slugs. Stop.
4. Row's `open` cell is `.` (closed) → confirm before switching. Switching
   onto closed work is usually a typo, not intent.
5. Rewrite `§A` to `slug`. Only edit in the whole run.
6. Echo the initiative's local §G so the human gets a one-line "here's what
   you're back on."

## BOUNDARIES

- Never touches `CONSTITUTION.md`.
- Never touches the initiative's own `SPEC.md`.
- Never creates an initiative — that's `/sk:spec new`.
- Never switches without a valid slug already present in `§N`.
