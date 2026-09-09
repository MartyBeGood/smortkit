---
name: spec
description: |
  Create, amend, or backprop bugs into an initiative's local SPEC.md under
  `.smort/initiatives/`, or (via `--constitution`) into the project-level
  `.smort/CONSTITUTION.md`. Sole mutator of both. Triggers when the user
  asks to write a spec, start a new initiative, distill a spec from existing
  code, add invariants, amend sections (§G, §C, §I, §V, §T, §B), or record a
  bug via backprop. Common phrasings: "write the spec for...", "new
  initiative", "bug: ...", "amend §V.3", "distill spec from code", "spec
  this idea". Reads and follows FORMAT.md for the caveman encoding rules,
  the `.smort/` layout, the pipe-table shape of §N/§T/§B, and the SLICING
  rule that makes every §T row one shippable commit.
---

# spec — spec mutator

Read `FORMAT.md` once if not already loaded — path `../../FORMAT.md` relative
to this file (plugin root, not the project). Caveman skill applies to all
writes here.

## DISPATCH

Resolve `.smort/` at project root (cwd, else nearest parent `.git` — never
search wider). Then:

1. No `.smort/` AND a legacy root `SPEC.md` exists → tell user to run
   `/sk:migrate` first. Stop.
2. No `.smort/` AND args describe an idea → **NEW** (bootstraps the whole
   tree, see below).
3. No `.smort/` AND `from-code` in args → **DISTILL** (bootstraps the tree
   from existing code).
4. `.smort/` exists AND args start `bug:` → **BACKPROP** (active initiative, local).
5. `.smort/` exists AND args start `amend` → **AMEND** (active initiative,
   local, unless `--constitution`).
6. `.smort/` exists AND the handoff comes from **review**'s promotion step
   (a drafted candidate citing a local `§V.n`, not freeform user text) →
   **PROMOTE** (constitution §V only — the one path in besides bootstrap).
7. `.smort/` exists AND args describe a fresh idea distinct from the active
   initiative → **NEW** (adds another initiative to the existing tree).
8. `.smort/` exists, no args → ask user which mode.

When a mode needs "the active initiative": read `INITIATIVES.md §A` for the
slug, operate on `initiatives/<slug>/SPEC.md`.

## INPUTS — spec is the sole mutator

The other verbs produce material; spec writes it. Ingest their handoff blocks
into the right section, show a diff, write on OK:

- **grill** → sharpened §G + §C (active initiative, or constitution with `--constitution`)
- **research** → §R rows (add the §R section if absent) — active initiative only
- **review** → drafted §V lines + risk verdict (active initiative), or a
  promotion candidate → constitution §V
- **deepen** → §I/§V/§T amendments — active initiative only

Never rewrite a section the handoff did not name. Sectioned ownership (see FORMAT.md).

## NEW — idea → initiative

Input: user idea. If it arrived fuzzy, prefer running **grill** first.

Steps:
1. Slugify the idea → `<today's-date>-<slug>`.
2. `.smort/CONSTITUTION.md` absent (first-ever initiative on this project) →
   bootstrap it: §G from the same goal line, §C and §V empty. This is the
   only time NEW touches the constitution.
3. `mkdir .smort/initiatives/<date>-<slug>/`.
4. Extract goal (1 line, caveman) → local §G.
5. List constraints user stated or implied → local §C (additive to
   constitution §C per STACKING — don't repeat a constitution constraint here).
6. List external surfaces user named → local §I.
7. §R only if **research** ran — else omit the section (right-size).
8. Propose initial invariants → local §V (numbered V1…). These start local;
   only `/sk:review` can promote one to the constitution.
9. Slice goal into shippable units → local §T pipe table, all status `.`,
   ids T1… One row = one commit that lands on trunk green & deployable. Run
   the SLICE TEST below on every row before writing.
10. Local §B section with header row only (`id|date|cause|fix`).
11. `.smort/INITIATIVES.md` absent → create it, `§A` = new slug, `§N` header
    row + this row. Else append the row to `§N` and set `§A` to the new slug
    — a freshly created initiative becomes the active one, same as creating
    and checking out a branch.

Write. Show user the full new file(s). Ask: "spec OK? `/sk:review` if
high-blast-radius, else `/sk:build`."

## DISTILL — code → initiative

Same bootstrap as NEW (constitution first-time, `INITIATIVES.md` row, active
initiative). Walk repo. Produce §G (infer from README/package.json/main
entry), §C (infer from stack), §I (enumerate public APIs/CLIs/configs), §V
(derive from tests and assertions — starts local, promotion is `/sk:review`'s
job), §T (one shippable row per known gap — a TODO or missing test that ships
alone; fold layer-only work into the behavior row that needs it), §B (empty).

Caveman everywhere. Flag uncertain items with `?` in text so user can confirm.

## BACKPROP — bug → local §B + §V

Input: `bug: <description>`. Target: active initiative's SPEC.md, always
local — a bug never promotes itself (see FORMAT.md PROMOTION; only
`/sk:review` promotes, and only the invariant, never the bug row).

Steps:
1. Parse bug description.
2. Find root cause (read relevant code).
3. Decide: would a new invariant catch recurrence? If yes → draft `V<next>`
   in the active initiative's local §V.
4. Append local §B row: `B<next>|<date>|<cause>|V<N>`.
5. Append new invariant to local §V.
6. If fix also changes behavior → add/update local §T rows. Fix + its
   regression test = one row, one commit; never a separate "add test for B<n>"
   row.
7. Show diff. Apply only on user OK.

Rule: every bug gets a §B entry. Invariant optional but preferred.

## AMEND — targeted edit

Input: `amend §V.3` etc. Default target: active initiative's SPEC.md.
`--constitution` → target `.smort/CONSTITUTION.md` instead, §G or §C only —
constitution §V is promotion-only (see FORMAT.md PROMOTION); refuse a direct
`amend --constitution §V` and point the user at `/sk:review`. PROMOTE, below,
is the only path a §V takes into the constitution — it is not a variant of
AMEND and never triggers off user-typed `amend --constitution §V`.

Read that section. Show current. Ask user what changes. Write. Show diff.

Never silently rewrite sections user did not name.

## PROMOTE — local §V → constitution §V

Input: review's handoff only — a drafted candidate line plus the local
`§V.n` it came from (see FORMAT.md PROMOTION). Never triggered by a user
typing `amend --constitution §V` directly; that stays refused under AMEND.

Steps:
1. Show diff: candidate line appended to `CONSTITUTION.md §V`, numbered next
   in that file's own sequence, citing the source `<slug>§V.n`.
2. Write only on user OK.
3. Local §V entry stays put — promotion copies, it doesn't move or delete
   the local invariant.

## SLICING §T — one row, one shippable commit

Every §T row this skill writes (NEW, DISTILL, BACKPROP, AMEND, or a deepen
handoff) is a trunk-based commit: merged alone, trunk stays green and
deployable. Full rule + examples in FORMAT.md SLICING.

**SLICE TEST** — write the row only if all four hold:

1. **ships** — trunk green & deployable after this row alone. No half-built
   user-visible state.
2. **observable** — names behavior someone can see, or a behavior-preserving
   refactor. Not a stack layer.
3. **whole** — test + impl + doc/migration for that behavior in the same row.
4. **revertable** — one `git revert` undoes it without breaking earlier rows.

Fails the test → don't write it. Instead:

- layer row (`add model`, `wire routes`, `scaffold repo`, `add tests`) → fold
  into the first behavior row that needs it.
- row too big to ship whole, partial unsafe → split by behavior and gate
  behind a flag defaulting off; last row flips the flag.
- row too small to ship alone (`rename var`) → merge into its neighbor.

Order rows by dependency only. Thinnest end-to-end path first, then broaden.

## OUTPUT RULES

- Caveman format per `FORMAT.md`.
- Preserve identifiers, paths, code verbatim.
- Numbering monotonic per file — never reuse §V.N or §B.N or §N.N within it.
- §T row `cites` column must list §V/§I deps, and the task cell names the
  behavior shipped: `T5|.|reject req w/o auth header → 401|V2,I.api`.
- Local edits stay local. Only touch `CONSTITUTION.md` via: the one-time
  bootstrap inside NEW/DISTILL, `--constitution` on AMEND (§G/§C only), or
  PROMOTE (§V only, review-sourced).

## NON-GOALS

- No sub-agents. Main thread writes.
- No dashboards, no logs, no state files beyond `.smort/` itself.
- No auto-build after spec. User invokes build explicitly.
