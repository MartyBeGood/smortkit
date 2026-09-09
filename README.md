<h1 align="center">smortkit</h1>

<p align="center">
  <strong>compressed spec-driven development for claude code</strong><br/>
  <sub>one constitution · many initiatives · zero sub-agents</sub>
</p>

---

## what this is

Plan-then-execute forgets. SDD remembers — but most SDD frameworks bury
that value under agent swarms, dashboards, and ceremony that costs more
tokens than it saves.

Smortkit is the simplest full loop: **grill → spec → research → review →
build**, over `.smort/` — a small project-level constitution plus one
`SPEC.md` per initiative — no sub-agents. Three commands you run every time;
six more you reach for only when the change earns it.

The spine is four properties that earn their tokens:

- **durable spec** — `.smort/` at project root survives context resets. It
  is the agent's long-term memory: lose the window, reload the spec, keep
  going. Multiple initiatives can be in flight; one is active at a time.
- **caveman encoding** — ~75% fewer tokens than prose. Symbols, fragments,
  pipe tables. The full eleven-skill description set still costs a fraction
  of spec-kit's 18.6k context. That is the whole point.
- **backprop reflex** — every test failure becomes a `§B` entry; classes
  of bug become `§V` invariants the spec never forgets.
- **shippable slices** — one `§T` row = one logical unit = one commit that
  lands on trunk green and deployable. Trunk-based, vertical slices; no
  layer rows, no "add tests" row, no half-shipped trunk.

And one rule that keeps it from bloating into the frameworks it replaces:
**right-size**. A one-line fix is just `/build`. The full chain is for
genuinely uncertain or high-blast-radius work — never for a typo.

## commands

**the loop** — run these every time:

| cmd | job |
|---|---|
| `/sk:spec` | create / amend / backprop the active initiative's `SPEC.md` (or `CONSTITUTION.md` with `--constitution`). Sole mutator of both. |
| `/sk:build` | native plan → execute against the active initiative's spec, honoring `CONSTITUTION.md`. Names which test proves each `§V`. One `§T` row = one shippable commit. Auto-backprops on failure. |
| `/sk:check` | read-only drift report. Lists §V / §I / §T violations for the active initiative + constitution; `--all` sweeps every open initiative. |

**reach for these** — only when the change earns the ceremony:

| cmd | job |
|---|---|
| `/sk:grill` | interrogate a fuzzy idea into a sharp `§G`/`§C`, one question at a time, before you spec. |
| `/sk:research` | gather external knowledge into local `§R` so build grounds in facts, not hallucinations. Every finding cites a source. |
| `/sk:review` | adversarial senior review of the spec *before* build. Refutes, hardens local `§V`, proposes the rare project-wide invariant as a promotion to `CONSTITUTION.md` §V, ends in a go/no-go gate. |
| `/sk:deepen` | spare-budget design pass — make one shallow module deep. Behavior held, tests green before & after. |
| `/sk:switch` | change the active initiative. Rewrites one line (`INITIATIVES.md §A`). |
| `/sk:migrate` | one-shot upgrade from the classic single-file `SPEC.md` to the `.smort/` layout. Idempotent, shows the diff first. |

## install

One line, via the `skills` CLI:

```bash
npx skills add MartyBeGood/smortkit
```

Installs eleven skills into `~/.claude/skills/`: `spec`, `build`, `check`
(the loop), `grill`, `research`, `review`, `deepen`, `switch`, `migrate`
(reach-for), plus `caveman` and `backprop` (the utilities). Claude activates
each when its trigger context matches — e.g. "write a spec for…" invokes
`spec`, a fuzzy idea invokes `grill`, a risky change before build invokes
`review`. Claude Code picks them up on next launch.

Or via the Claude Code marketplace (also adds the `/sk:spec`, `/sk:build`,
`/sk:check`, `/sk:grill`, `/sk:research`, `/sk:review`, `/sk:deepen`,
`/sk:switch`, `/sk:migrate` slash commands):

```bash
/plugin marketplace add MartyBeGood/smortkit
/plugin install sk@smortkit
```

Or clone directly:

```bash
git clone https://github.com/MartyBeGood/smortkit.git ~/.claude/plugins/smortkit
```

## format

See [`FORMAT.md`](./FORMAT.md). `.smort/` holds a project-level
`CONSTITUTION.md` (§G goal, §C constraints, §V promoted invariants) and an
`INITIATIVES.md` registry (§A active slug, §N pipe table) pointing at
`initiatives/<date>-<slug>/SPEC.md` per initiative — full local schema: §G
goal, §C constraints, §I interfaces, §R research (optional, pipe table), §V
invariants, §T tasks (pipe table, one row = one shippable commit), §B bugs
(pipe table). Each verb owns specific sections — no verb rewrites a section it does not own, and only
`/sk:review` promotes a local §V into the constitution.

## files

```
FORMAT.md             .smort/ schema + caveman encoding + §T slicing + sectioned ownership
commands/             nine thin slash-command entry points → the skills (loop + reach-for)
skills/spec           spec mutator — sole writer of SPEC.md and CONSTITUTION.md
skills/build          plan-execute, verification contract
skills/check          drift report
skills/grill          sharpen a fuzzy idea → §G/§C before spec
skills/research       external knowledge → §R, every finding sourced
skills/review         adversarial senior review of the spec → hardens §V, proposes promotions
skills/deepen         spare-budget design pass — make one module deep
skills/switch         change the active initiative
skills/migrate        one-shot upgrade from single-file SPEC.md to .smort/
skills/caveman        encoding utility
skills/backprop       bug → spec protocol (seven steps)
```

## non-goals

- no sub-agents. Main Claude does the work.
- no dashboards. `cat .smort/initiatives/<slug>/SPEC.md` is the dashboard.
- one active initiative at a time — others park, they don't vanish.
- no JSON / YAML spec bodies. Markdown + pipe tables.
- no hooks, no orchestration binaries, no TypeScript helpers.

---

## philosophy

> The spec is the only artifact that earns its tokens. Everything else
> that costs tokens must either save more tokens later, or the user's
> attention, or it gets cut.

See [`CHANGELOG.md`](./CHANGELOG.md) for project history.

## license

MIT.
