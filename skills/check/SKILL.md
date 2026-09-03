---
name: check
description: |
  Read-only drift detector. Diffs the active initiative's local SPEC.md (plus
  the project-level CONSTITUTION.md) against current code and reports
  violations grouped by severity. `--all` is the full sweep — every section
  (§V/§I/§T) across every open initiative, not just the active one. Writes
  nothing — suggests remedies via the spec or build skills but never invokes
  them. Triggers when the user asks to check drift, audit the spec, verify
  invariants, or ask whether code still matches the spec. Phrasings: "check
  drift", "audit the spec", "does the code still match §V", "check
  invariants", "spec vs code", "check every initiative".
---

# check — drift report

Pure diagnostic. Reports violations. Writes nothing. User decides remedy.

Spec drifting silently from code is the #1 SDD failure mode. check is the
detector. Run it after each `/build` and before each ship — drift caught here is
a diff; drift caught in prod is a §B.

## LOAD

1. Resolve `.smort/` at project root (cwd, else nearest parent `.git`) —
   never search wider. Missing → "no spec, nothing to check." Stop.
2. Read `CONSTITUTION.md` — §V here (promoted invariants) apply to every
   initiative checked below (see FORMAT.md STACKING).
3. Parse invocation args:
   - `§V` → check invariants only (default), active initiative
   - `§I` → check interfaces, active initiative
   - `§T` → audit task status vs code, active initiative
   - `--all` → full sweep: all three sections, across every `open=x` row in
     `INITIATIVES.md §N` (not just the active one) — report grouped per initiative
4. No `--all` → read `INITIATIVES.md §A`, target `initiatives/<slug>/SPEC.md` only.

## CHECK §V — invariants

For each V<n> (constitution §V, then the target initiative's local §V):

1. Translate invariant into verifiable claim about code.
2. Grep / read relevant files.
3. Classify: **HOLD** / **VIOLATE** / **UNVERIFIABLE**.
4. Record address + file:line evidence. Constitution invariants cite as
   `CONSTITUTION§V.n`; local ones as bare `V.n` inside a per-initiative
   report, or `<slug>§V.n` when reporting across initiatives (`--all`).

## CHECK §I — interfaces

For each I item:

1. Locate implementation.
2. Classify:
   - **MATCH** — shape in code = shape in spec.
   - **DRIFT** — impl exists, shape differs.
   - **MISSING** — impl absent.
   - **EXTRA** — code exposes surface not in §I.

## CHECK §T — tasks

For each T<n>:

1. If `x`: verify claimed work present.
2. If `~`: note as in-progress.
3. If `.`: note as pending.
4. Flag `x` rows with no evidence as **STALE**.

## REPORT

Caveman. Grouped by severity — and by initiative when `--all`.

```
## §V drift
CONSTITUTION§V.2 VIOLATE: auth/mw.go:47 uses `<` not `<=`. see §B.1.
V5 UNVERIFIABLE: no test covers every req path.

## §I drift
I.api DRIFT: POST /x returns `{result}` not `{id}`. route.go:112.
I.cmd MISSING: `foo bar` absent from cli/*.go.

## §T drift
T3 STALE: status `x`, no middleware file exists.

## summary
2 violate. 1 missing. 1 stale. 1 unverifiable.
next: spec skill with `bug:` or fix code at cited lines.
```

## REMEDY HINTS (not actions)

End report with one-line hint per class:
- VIOLATE / DRIFT → invoke spec skill `bug: <V.n>` or fix code. Constitution
  violation → still a local `bug:` on the initiative that surfaced it; the
  spec skill routes any resulting §V edit through review/promotion, never a
  direct constitution write.
- MISSING → invoke build skill on `§T.n` if task exists; else spec skill `amend §T`.
- STALE → spec skill `amend §T` to uncheck.
- EXTRA → spec skill `amend §I` to document, or delete code.

Never invoke fixes. Report only.

## NON-GOALS

- Zero writes. No SPEC.md edits, no CONSTITUTION.md edits, no INITIATIVES.md edits. No code edits.
- No sub-agents. Main thread reads.
- No scores, no grades. Binary per item: holds or drifts.
