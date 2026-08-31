<h1 align="center">smortkit</h1>

<p align="center">
  <strong>compressed spec-driven development for claude code</strong><br/>
  <sub>one file · one loop · zero sub-agents</sub>
</p>

---

## what this is

Plan-then-execute forgets. SDD remembers — but most SDD frameworks bury
that value under agent swarms, dashboards, and ceremony that costs more
tokens than it saves.

Smortkit is the simplest full loop: **grill → spec → research → review →
build**, over one `SPEC.md` file, no sub-agents. Three commands you run
every time; four more you reach for only when the change earns it.

The spine is three properties that earn their tokens:

- **durable spec** — `SPEC.md` at repo root survives context resets. It is
  the agent's long-term memory: lose the window, reload the spec, keep going.
- **caveman encoding** — ~75% fewer tokens than prose. Symbols, fragments,
  pipe tables. All nine skill descriptions cost ~1.1k context — 16× lighter
  than spec-kit's 18.6k. That is the whole point.
- **backprop reflex** — every test failure becomes a `§B` entry; classes
  of bug become `§V` invariants the spec never forgets.

And one rule that keeps it from bloating into the frameworks it replaces:
**right-size**. A one-line fix is just `/build`. The full chain is for
genuinely uncertain or high-blast-radius work — never for a typo.

## commands

**the loop** — run these every time:

| cmd | job |
|---|---|
| `/sk:spec` | create / amend / backprop `SPEC.md`. Sole mutator. |
| `/sk:build` | native plan → execute against spec. Names which test proves each `§V`. Auto-backprops on failure. |
| `/sk:check` | read-only drift report. Lists §V / §I / §T violations. The drift detector. |

**reach for these** — only when the change earns the ceremony:

| cmd | job |
|---|---|
| `/sk:grill` | interrogate a fuzzy idea into a sharp `§G`/`§C`, one question at a time, before you spec. |
| `/sk:research` | gather external knowledge into `§R` so build grounds in facts, not hallucinations. Every finding cites a source. |
| `/sk:review` | adversarial senior review of the spec *before* build. Refutes, hardens `§V`, ends in a go/no-go gate. |
| `/sk:deepen` | spare-budget design pass — make one shallow module deep. Behavior held, tests green before & after. |

## install

One line, via the `skills` CLI:

```bash
npx skills add MartyBeGood/smortkit
```

Installs nine skills into `~/.claude/skills/`: `spec`, `build`, `check`
(the loop), `grill`, `research`, `review`, `deepen` (reach-for), plus
`caveman` and `backprop` (the utilities). Claude activates each when its
trigger context matches — e.g. "write a spec for…" invokes `spec`, a fuzzy
idea invokes `grill`, a risky change before build invokes `review`. Claude
Code picks them up on next launch.

Or via the Claude Code marketplace (also adds the `/sk:spec`, `/sk:build`,
`/sk:check`, `/sk:grill`, `/sk:research`, `/sk:review`, `/sk:deepen` slash
commands):

```bash
/plugin marketplace add MartyBeGood/smortkit
/plugin install sk@smortkit
```

Or clone directly:

```bash
git clone https://github.com/MartyBeGood/smortkit.git ~/.claude/plugins/smortkit
```

## format

See [`FORMAT.md`](./FORMAT.md). Sections: §G goal, §C constraints, §I
interfaces, §R research (optional, pipe table), §V invariants, §T tasks
(pipe table), §B bugs (pipe table). Each verb owns specific sections —
no verb rewrites a section it does not own.

## files

```
FORMAT.md             spec schema + caveman encoding + sectioned ownership
commands/             seven thin slash-command entry points → the skills (loop + reach-for)
skills/spec           spec mutator — sole writer
skills/build          plan-execute, verification contract
skills/check          drift report
skills/grill          sharpen a fuzzy idea → §G/§C before spec
skills/research       external knowledge → §R, every finding sourced
skills/review         adversarial senior review of the spec → hardens §V
skills/deepen         spare-budget design pass — make one module deep
skills/caveman        encoding utility
skills/backprop       bug → spec protocol (six steps)
```

## non-goals

- no sub-agents. Main Claude does the work.
- no dashboards. `cat SPEC.md` is the dashboard.
- no parallel workers. One thread, one spec, one diff.
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
