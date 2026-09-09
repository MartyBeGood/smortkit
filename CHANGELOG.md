# CHANGELOG

## v5.1.0 — shippable §T slices

Behavior change, format-compatible. Existing `.smort/` trees keep parsing;
what changes is how §T rows get written and built.

### why

§T rows were coming out layer-shaped — `scaffold repo`, `add User model`,
`wire routes`, `add tests`. Nothing in that sequence ships until the last
row lands, so `/sk:build`'s commit-per-task produced commits that could not
go to trunk on their own. Trunk-based development wants the opposite: each
commit a logical, deployable unit.

### added

- **FORMAT.md SLICING** — one §T row = one logical, shippable unit = one
  commit that lands on trunk green and deployable. Codifies the **SLICE
  TEST** (ships / observable / whole / revertable), vertical-over-horizontal
  slicing, flag-gating for behavior too big to ship in one row, and
  dependency-only ordering. Bad/good examples included.
- **`/sk:build` slice check** — LOAD runs the SLICE TEST on every chosen
  row. A mis-sliced row is named, re-sliced, and routed through spec
  (`amend §T`) before any code is written.
- **`/sk:build` commit plan** — PLAN names what makes each row shippable
  (behavior going live, or the flag that hides the unfinished part). A plan
  that needs two commits to reach a green trunk means the row is mis-sliced.
- **`/sk:review` slice axis** — PHASE 2 refutes §T slicing: a row that
  ships nothing alone is a finding, with re-sliced rows proposed.

### changed

- **`/sk:spec`** — NEW slices the goal into shippable units (was "break
  goal into ordered tasks"); DISTILL emits one shippable row per gap;
  BACKPROP puts fix + regression test in one row. New SLICING §T section
  carries the SLICE TEST and the three re-slice moves.
- **`/sk:build` write policy** — one row, one commit, made only when green.
  Never mid-row, never one commit spanning rows, never a red or half-shipped
  trunk. VERIFICATION adds "row ships" to the `x` conditions.
- **`/sk:deepen`** — proposed §T refactor rows must each ship alone; big
  refactors split by intermediate green states, never into layers.
- **§T task cells** — phrased as observable behavior
  (`POST /x accepts {name} → 201 {id}`), not stack layers (`add auth mw`).
  Examples updated in FORMAT.md and the caveman skill.

## v5.0.0 — .smort/: constitution + initiatives

Breaking. Not backward compatible with v4.x's single-file `SPEC.md` — folder
rename, root file split into three, two new commands. Migration path
provided (`/sk:migrate`), not automatic.

### why

One `SPEC.md` per project stops scaling once a second piece of work starts —
either it all crams into one file's §T, or you overwrite it starting a new
one. `.smort/` splits low-churn project truth (goal, constraints, invariants
that proved themselves) from high-churn current work (one `SPEC.md` per
initiative), and lets several initiatives exist at once without stepping on
each other.

### added

- `.smort/CONSTITUTION.md` — project-level §G/§C/§V. §V here is
  promotion-only: written by `/sk:grill` on a project's first pass, or by
  `/sk:review` promoting a local invariant that held up under adversarial
  review. Nothing else writes it.
- `.smort/INITIATIVES.md` — §A (active initiative slug) + §N (pipe-table
  registry: id|slug|phase|open|goal).
- `.smort/initiatives/<date>-<slug>/SPEC.md` — same local schema as classic
  smortkit (§G §C §I §R §V §T §B), one per initiative.
- **`/sk:switch`** — change the active initiative. Writes one line
  (`INITIATIVES.md §A`), nothing else moves. Confirms before switching onto
  a closed initiative.
- **`/sk:migrate`** — one-shot, idempotent upgrade from a classic root
  `SPEC.md` to the `.smort/` layout. Promotes every existing §V to the
  constitution (all were project-proven under the old single-spec model),
  duplicates §G/§C into the migrated initiative, moves §I/§R/§T/§B verbatim,
  shows the full diff before writing, stubs (doesn't delete) the old file.
- **addressing** — unqualified `§<S>.<n>` resolves within the file being
  read; cross-file references need the slug (`oauth-flow§T.3`).
- **stacking** — an initiative's local §C is additive to constitution §C,
  never an override; `/sk:build` and `/sk:check` read both.
- **promotion** — `/sk:review` is the only path a local §V takes to the
  constitution. A §B row never promotes on its own, only the invariant it
  produced, and only by surviving review. `/sk:backprop` gets a seventh
  step: flag a project-wide-looking §V as a promotion candidate, never
  write the constitution itself.
- `--constitution` flag on `/sk:grill` and `/sk:spec amend` — the rare
  project-level edit, escape-hatched out of the initiative-scoped default.
- `/sk:check --all` now sweeps every open initiative, not just the active
  one, in addition to all three sections.

### changed

- every skill that used to read/write a fixed `SPEC.md` path now resolves
  the active initiative via `INITIATIVES.md §A` first.
- `/sk:build` and `/sk:check` read `CONSTITUTION.md` §C/§V in addition to
  the active initiative's local spec.
- README, `FORMAT.md` fully rewritten for the three-schema layout.

## v4.2.0 — smortkit fork

Fork of `cavekit` under new ownership. Rebrand only where it's ownership
(plugin/marketplace identity, license, contact info) — `SPEC.md` files and
installed skills from the parent project keep working unchanged.

### changed

- project renamed `cavekit` → `smortkit`; plugin/command prefix `ck` → `sk`
  (`/sk:spec`, `/sk:build`, `/sk:check`, `/sk:grill`, `/sk:research`,
  `/sk:review`, `/sk:deepen`); marketplace renamed to `smortkit`.
- `build` enforces a red→green→refactor TDD loop whenever the target
  project has a detectable test setup (test runner config, test dir, or
  framework files) — write the named test from the verification contract
  first, confirm it fails for the right reason, then implement to green.
  Falls back to today's plain verification-command check when no test
  framework is detected.
- `FORMAT.md`'s caveman-encoding section and the `caveman` skill no longer
  carry a symbols lookup table — the symbol set is used inline in examples
  but isn't spelled out as a separate reference table.

### removed

- `SECURITY.md`, `LAUNCH-POST.md`, `UPGRADE.md` — tied to the parent
  project's contact info, personal launch narrative, and v3.1.0 migration
  path, none of which apply to this fork.
- README's "older cavekit (Hunt lifecycle, v3.1.0)" section and the
  ecosystem table pointing at the parent author's other repos.

## v4.1.0 — the full loop

Additive. Backward compatible with v4.0.0 — the three-command core is
unchanged; `§R` is optional; existing `SPEC.md` files still parse. Every
change below traces to a documented pain point or a research finding, not
a hunch.

### added — four reach-for verbs

The core loop stays `spec → build → check`. Four new verbs join it, each
opt-in and right-sized — you reach for them only when the change earns it:

- **`/sk:grill`** — calibrated interrogation of a fuzzy idea into a sharp
  `§G`/`§C`, one question at a time, before a spec exists.
- **`/sk:research`** — external knowledge into the new `§R` log; every
  finding cites a source, unverified ones flagged, never written as fact.
- **`/sk:review`** — adversarial senior review of the spec *before* build:
  refutes rather than rubber-stamps, hardens `§V`, ends in a go/no-go gate.
- **`/sk:deepen`** — spare-budget design pass. Picks the one shallowest
  module, proposes a deeper shape, holds behavior constant (tests green
  before and after).

### added — format

- `§R RESEARCH` — optional pipe-table log of external knowledge (`id|topic|finding|src`).
- **Sectioned ownership** — each verb writes only the sections it owns; no
  verb rewrites a foreign section. `spec` remains the sole general mutator.
- **Right-size** rule — ceremony scales to blast radius, never to ego.

### changed

- `build` now names the *exact* test that proves each `§V` it touches (a
  verification contract) instead of "add tests" — "do TDD" alone backfires.
- `build` reads `§R` so it grounds in researched facts, not re-derivation.
- `check` reframed as the drift detector: run after each build, before each ship.
- skill descriptions kept ultra-tight — nine descriptions cost ~1.1k context, 16× lighter than spec-kit's 18.6k.

### why — pain points → changes (sourced)

| pain point (source) | change |
|---|---|
| Token / context tax — spec-kit loads ~18.6k tokens every session (spec-kit #1401); BMAD burns 80–100k/step (#1188) | caveman descriptions keep smortkit's whole nine-skill set at ~1.1k context — 16× lighter |
| Specs drift silently with no detector (OpenSpec #1212; spec-kit #1686) | `check` reframed as the drift detector, run every build |
| Ceremony overkill — 10–15× overhead, "sledgehammer for a nut" (BMAD #2003; HN 45610996) | **right-size** rule; core stays 3 commands; verbs are opt-in |
| Agents ignore the spec / mark done without doing (spec-kit #230; BMAD #446) | `build` verification contract names which test proves each `§V` |
| "Process without library context = organized hallucinations" (Tessl) | `/research` + durable `§R` external-knowledge log |
| No tool adversarially reviews the *plan* before build (competitor scan) | `/review` — separate skeptic anchored to an external oracle |
| Tools overwrite/delete spec files (Kiro #5239; Conductor) | **sectioned ownership** — no verb rewrites a section it does not own |

### why — research backing

- Spec as durable external memory across context resets — Anthropic,
  *Effective Context Engineering* (2025); the core justification for SDD.
- Plan in a separate phase — ADaPT (NAACL 2024).
- Verification contract names *which* tests, not "do TDD" — TDAD (2026).
- Critique must be external + adversarial, not introspective — LLMs cannot
  self-correct alone (Huang et al., ICLR 2024); separate-critic debate works
  (Du et al., ICML 2024).
- Gate effort by difficulty — Self-Critique Paradox (Snorkel, 2025);
  right-size follows directly.
- Deep modules — Ousterhout, *A Philosophy of Software Design* (`/deepen`).

---

## v4.0.0 — the rewrite

Full rewrite. Not backward compatible with v3.x. Different shape, same name.

### philosophy

Kept only what earned its tokens:

- `SPEC.md` — durable, addressable, caveman-encoded
- three commands — `/sk:spec`, `/sk:build`, `/sk:check`
- two skills — `caveman` encoding, `backprop` protocol

### added

- single `SPEC.md` format with six addressable sections (§G §C §I §V §T §B)
- pipe-table encoding for §T (tasks) and §B (bugs)
- caveman symbol set (→ ∴ ∀ ∃ ! ? ⊥ ≠ ∈ ∉ ≤ ≥ & |) as default for spec writes
- bug → §B → §V backprop reflex wired into `/sk:build` failure path
- `/sk:spec from-code` — distill spec from existing codebase
- `/sk:check` — read-only drift report (replaces five v3 review flavors)
- `npx skills add MartyBeGood/smortkit` one-line install path (commands + skills)

### removed (relative to v3.1.0)

- 13 of 16 commands (sketch/map/make/ship/review/revise/status/init/config/resume/help/design/research/team/make-parallel)
- all 12 named sub-agents
- 19 of 21 skills
- Go binary and source (`cmd/`, `internal/`, `bin/`, `cavekit` executable)
- shell hooks (`hooks/`, `scripts/cavekit-launch-session.sh`, stop-hook state machine)
- TS tooling (`scripts/cavekit-picker.ts`, `scripts/cavekit-router.cjs`)
- Codex peer-review bridge (`.codex-plugin/`)
- `context/kits/`, `context/plans/`, `context/impl/`, `context/refs/` directories
- autonomous loop, per-task budgets, model-tier routing
- design-system `DESIGN.md` workflow
- knowledge-graph `graphify-out/` integration
- parallel wave execution and team mode
- `install.sh` (216 lines → 0)

### changed

- caveman was opt-in for inter-agent chatter in v3; default for spec writes in v4
- version: 3.1.0 → 4.0.0 (major rewrite, semver respected)
- README, plugin metadata, marketplace entry

### migration

No automated migrator — the v3 kit shape does not map cleanly to v4's
single file. Recommended path: run `/sk:spec from-code` on your existing
v3 project to distill a v4 spec from your built code.

### v3 reachability

v3 is frozen at tag `v3.1.0`. Stays installable and documented. Fixes
only for critical bugs; no new features.

---

## v3.1.0 and prior

See git log before the `v4.0.0` commit, or check out `v3.1.0`:

```bash
git checkout v3.1.0
```
