---
name: build
description: |
  Plan-then-execute implementation against SPEC.md. Native single-thread
  loop, no sub-agents. Enforces a red→green→refactor TDD loop whenever the
  target project has a detectable test setup — write the named test first,
  confirm it fails, then implement to green; falls back to plain
  verification when no test framework is present. On test or build
  failure, auto-invokes the backprop skill before retrying — a failed
  verification always considers whether a new §V invariant would prevent
  recurrence. Triggers when the user asks
  to build, implement, execute the spec, or tackle a specific §T task
  (`build §T.3`, `build --next`, `implement next task`, `run the build`).
  Expects SPEC.md to exist; if not, defers to the spec skill.
---

# build — implement spec

Single-thread native plan→execute. You are main Claude. No swarm.

## LOAD

1. Read `SPEC.md` from project root only (cwd, else nearest parent `.git`) —
   never search wider. Missing there → tell user to invoke the spec skill
   first. Stop.
2. Read `FORMAT.md` once if not loaded — path `../../FORMAT.md` relative to
   this file (plugin root, not the project).
3. Read §R if present — external facts the build must honor, never re-derive or contradict.
4. Parse invocation args:
   - `§T.n` → that task only
   - `--next` → lowest-numbered row with status `.` or `~`
   - `--all` or empty → every `.` row in §T order
5. **Detect test setup** — look for a test runner/framework in the target
   project: `package.json` `test` script, `pytest.ini`/`pyproject.toml`
   `[tool.pytest]`, `go.mod` + `*_test.go` files, `Cargo.toml` + `#[test]`,
   `Gemfile` + rspec/minitest, a `Makefile` `test` target, or an existing
   test directory. Found → **TDD-eligible: yes**, the EXECUTE loop below
   runs red→green→refactor. None found → **TDD-eligible: no**, note it once
   ("no test framework detected — TDD loop skipped, verification contract
   still required") and EXECUTE falls back to edit-then-verify.

High blast radius (shared module, auth, data, money, public §I)? Run `/review` first. Trivial & reversible? Skip planning ceremony, just do step EXECUTE.

## PLAN

Native plan mode — you delegate to it, you do not reinvent task breakdown. For chosen task(s):

1. Cite every §V invariant that applies. Plan must respect all.
2. Cite every §I interface touched. Plan must preserve shape.
3. List files to create / edit.
4. **Verification contract** — name the EXACT test(s) / acceptance criteria that
   prove each §V touched. Which test, not "add tests". "Do TDD" alone backfires;
   the spec says *what to check*. Each §V touched → a named test that fails first.
   TDD-eligible (see LOAD) → that named test must not already exist/pass; it is
   the test EXECUTE writes first, not one that happens to already cover the behavior.
5. Name verification command (test, build, lint) — this is the external oracle. Green = done; never "looks done".

Show plan. Wait for user OK unless auto mode.

## EXECUTE

Per task in order:

1. Flip §T.n status cell `.` → `~`. Just write to SPEC.md.
2. **TDD-eligible** (per LOAD detection) → red→green→refactor:
   a. **RED** — write the named test from the verification contract first.
      Run it. Must fail, and fail for the expected reason (assertion, not a
      syntax/import error). Fails wrong → fix the test, not the code.
   b. **GREEN** — write the minimum code to pass. Run the test. Must pass.
   c. **REFACTOR** — clean up if warranted, keeping the test green; re-run
      after every refactor edit.
   **Not TDD-eligible** → edit code per plan directly, no red/green split.
3. Run verification command.
4. **Pass** → flip `~` → `x`. Next task.
5. **Fail** → invoke backprop skill. Do NOT retry blindly.

## FAIL → BACKPROP

On test/build failure:

1. Read failure output.
2. Ask: is failure (a) my code bug, (b) spec wrong, or (c) unspecified edge case?
3. If (a) → fix code, re-run. No spec change.
4. If (b) or (c) → invoke spec skill with `bug: <cause>` first, let it update §V and §B, then resume build against updated spec.

Rule: never silently fix root-cause without considering backprop. §B is the memory that stops recurrence.

## WRITE POLICY

- Only flip §T status. No other SPEC.md edits from build.
- Other spec edits → invoke spec skill.
- Commit after each §T completes. Message: `T<n>: <goal line>` + §V cites.

## VERIFICATION

Task `x` only if:
- Verification command (the oracle) exits 0.
- Every §V touched has its named test from the verification contract, and it passes.
- TDD-eligible task → the named test was written and observed failing (RED)
  before the implementation landed. Red-first happened; it wasn't retrofitted.
- No §V invariant regressed (run full test suite at end).

## NON-GOALS

- No sub-agents. No parallel workers. Main thread only.
- No progress dashboards. `cat SPEC.md | grep §T` is the dashboard.
- No speculative work beyond chosen task scope.
- Never skip RED to save time when a test framework exists. An implementation
  without a red test first is hoped-correct, not verified.
