# SMORTKIT FORMAT

`.smort/` at project root. Three files, three schemas, one loop.

**ROOT**: project root = cwd, else nearest parent dir with `.git`. `.smort/`
lives there only — check that one place, never glob/find/search wider, that's
how these tools end up scanning Desktop/Documents/unrelated dirs and tripping
OS file-access prompts. Not found there → treat as missing, don't keep looking.

This file (`FORMAT.md`) ships inside the smortkit plugin itself, not the
target project — its location depends on where the plugin is installed
(user-wide or project-wide). Every `SKILL.md` that needs it resolves the path
relative to its own file (`../../FORMAT.md` from `skills/<name>/SKILL.md`),
never by searching for it.

## LAYOUT

```
.smort/
  CONSTITUTION.md              §G §C §V — project-level, low-churn
  INITIATIVES.md               §A §N   — pointer + registry, high-churn
  initiatives/
    <date>-<slug>/
      SPEC.md                  full local schema: §G §C §I §R §V §T §B
```

## CONSTITUTION.md

```
# CONSTITUTION

## §G GOAL
one line. what the project is, project-wide.

## §C CONSTRAINTS
- bullet. non-negotiable. every initiative inherits these.

## §V INVARIANTS
numbered. promoted only — never written directly except by /sk:grill
(seeds the first pass on a brand-new project) and /sk:review (promotes a
local §V that held up under adversarial review).
V1: every req → auth check before handler
```

## INITIATIVES.md

```
# INITIATIVES

## §A ACTIVE
oauth-flow

## §N INITIATIVES
id|slug|phase|open|goal
N1|oauth-flow|build|x|add OAuth login flow
N2|billing-export|done|.|CSV export for billing
```

`§A` holds one slug — the initiative every command targets by default.
`phase` in §N: grill / spec / research / review / build / done, wherever the
initiative sits in the loop. `open`: `x` in flight, `.` closed. Ids monotonic,
never reused, same rule as §T/§B.

## initiatives/\<date>-\<slug>/SPEC.md

Unchanged from classic single-file smortkit — full local schema, same rules
as always:

```
# SPEC

## §G GOAL
one line. what this initiative must do.

## §C CONSTRAINTS
- bullet. local to this initiative, additive to constitution §C (see STACKING).

## §I INTERFACES
external surface. what world sees.
- cmd: `foo bar` → stdout JSON
- api: POST /x → 200 {id}
- file: `config.yaml` schema …
- env: `FOO_KEY` required

## §R RESEARCH
optional. external-knowledge log. pipe table. present only if `/sk:research` ran.
durable, so build never re-derives & never hallucinates lib facts.
id|topic|finding|src
R1|jwt lib|`jose` > `jsonwebtoken` — maintained, ESM, 0 deps|github.com/panva/jose
R2|rate limit|token bucket ok @ our scale|<url>

## §V INVARIANTS
numbered. testable. local to this initiative until promoted (see PROMOTION).
V1: every req → auth check before handler
V2: token expiry no later than now → reject
V3: DB write must run in transaction

## §T TASKS
pipe table. ids monotonic (never reused). status: `x` done / `~` wip / `.` todo.
id|status|task|cites
T1|.|scaffold repo|-
T2|.|impl §I.api POST /x|V2
T3|x|add §V.1 middleware|V1,I.api

## §B BUGS
pipe table. backprop log. each row = bug + invariant that catches recurrence.
id|date|cause|fix
B1|2026-04-20|token `<` not `<=`|V2
B2|2026-04-21|race on write|V3
```

**Table cell rules** (every pipe table — §N, §R, §T, §B): literal `|` →
escape as `\|`. Backticks OK. Cells trimmed. Empty = `-`.

## ADDRESSING

`§<S>.<n>` = section.item. Unqualified, it resolves inside the file being
read — a bug row in an initiative's SPEC.md citing an invariant in that same
file stays bare: `V2`, `V1,I.api`. Reaching across files — constitution
citing an initiative, or one initiative citing another — needs the slug:
`oauth-flow§T.3`. Commands, commits, PRs all reference by §. Zero ambiguity.

## STACKING

An initiative's local §C is *additive* to constitution §C, never an override.
`/sk:build` and `/sk:check` read both and honor the union — a local
constraint can narrow, it can't contradict the constitution.

## PROMOTION

`/sk:review` is the only path from a local §V to constitution §V. It
proposes candidates, shows the diff, writes constitution §V only on
approval. §B never promotes on its own — only the invariant a bug produced
can, and only by surviving a review pass.

## CAVEMAN ENCODING

Default for every section, every file. Rules:

- Drop articles (a, an, the). Drop filler.
- Drop aux verbs (is, are, was) where fragment works.
- Short synonyms (fix > implement).
- Fragments fine.

**Preserve verbatim**: code, paths, identifiers, URLs, numbers, error strings, SQL, regex.

**Bad** (v1 prose):

> The authentication middleware must verify the token expiry on every request before allowing the handler to execute.

**Good** (v2 caveman):

> V1: every req → auth check before handler

**Bad** (prose bug note):

> Fixed a bug where token expiry comparison used strict less-than instead of less-than-or-equal, causing tokens to be rejected exactly at their expiry timestamp.

**Good** (v2 caveman):

> B1: token `<` not `<=`, so tokens rejected @ expiry. §V.2 now must use `<=`.

## WHY CAVEMAN FOR SPECS

Constitution + active initiative loaded every invocation. 75% fewer tokens =
75% fewer dollars & faster reads. Human skims fast too — no symbol legend to
hold in your head.

## COMPACTION

Big project → more initiatives, not more sections crammed into one
file. Constitution stays small by design — goal, constraints, and only the
invariants that survived promotion; low-churn. An initiative's SPEC.md > 500
lines → compact §B (old bugs drop oldest) before splitting.

## WRITES — SECTIONED OWNERSHIP

Each verb owns specific sections and reads a specific scope. No verb rewrites
a section it does not own. That rule alone kills the "tool deleted my spec"
failure mode.

| command | reads | writes |
|---|---|---|
| `/sk:grill` | active initiative | active initiative's §G+§C (local); `--constitution` targets constitution §G+§C instead |
| `/sk:spec new` | — | creates initiative folder, all sections; adds row to `INITIATIVES.md §N` |
| `/sk:spec amend` | active initiative | named section, local, unless `--constitution` |
| `/sk:spec bug` | active initiative | local §B + §V |
| `/sk:research` | active initiative | local §R |
| `/sk:review` | active initiative + constitution | hardens local §V; proposes promotions to constitution §V |
| `/sk:build` | constitution + active initiative | local §T status only |
| `/sk:switch` | `INITIATIVES.md §N` | `INITIATIVES.md §A` only |
| `/sk:check` | active initiative + constitution (default), all open initiatives with `--all` | — |
| `/sk:deepen` | active initiative | local §I §V §T |

`spec` is the general editor — any cross-cutting local edit routes through
it. Every other verb shows a diff and touches only its own sections.
Compaction (dropping oldest §B rows when an initiative's SPEC.md > 500 lines,
per COMPACTION) is the one sanctioned §B rewrite — route it through `spec`,
which shows the diff before dropping rows.

## RIGHT-SIZE

Ceremony scales to blast radius, never to ego. One-line fix in the active
initiative → just `/sk:build`. New feature in a shared module → `/sk:grill`
then `/sk:review` first. The full grill→spec→research→review→build chain is
for genuinely uncertain or high-blast-radius work, never for a typo. Skip any
verb that would cost more attention than the change is worth.

That is whole format.
