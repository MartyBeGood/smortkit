---
name: migrate
description: |
  One-shot migration from the classic single-file `SPEC.md` layout to
  `.smort/CONSTITUTION.md` + `.smort/INITIATIVES.md` +
  `.smort/initiatives/<date>-migrated/SPEC.md`. Idempotent — refuses to run
  if `.smort/` already exists. Shows the full diff before writing anything.
  Replaces the old root `SPEC.md` with a one-line stub pointing at the new
  location rather than deleting it. Triggers when the user says "migrate to
  .smort", "upgrade my spec layout", or invokes /sk:migrate, or when
  another smortkit skill detects a legacy root `SPEC.md` and no `.smort/`.
---

# migrate — SPEC.md → .smort/

One-shot. Idempotent. Never runs twice on the same project.

## PRECONDITIONS

1. Resolve project root (cwd, else nearest parent `.git`).
2. `.smort/` already exists → stop: "already migrated, nothing to do."
3. No root `SPEC.md` → stop: "no legacy spec found, nothing to migrate."

Both checks must pass before touching anything.

## STEPS

### 1. READ OLD SPEC
Read root `SPEC.md`. Parse §G §C §I §R (if present) §V §T §B.

### 2. BUILD CONSTITUTION.md
```
§G ← old §G, verbatim
§C ← old §C, verbatim
§V ← old §V, verbatim — every existing invariant was project-proven under
     the old single-spec model. Promote all of them; don't try to guess
     which ones are "really" cross-cutting. The human demotes later via
     amend if one turns out to be initiative-specific.
```

### 3. BUILD initiatives/\<today>-migrated/SPEC.md
```
§G ← old §G           (duplicated intentionally — the old spec never
§C ← old §C            distinguished project-goal from current-work-goal,
                        so copy into both and let the human trim rather
                        than guess and drop context)
§I ← old §I, verbatim
§R ← old §R, verbatim (only if old spec had one)
§V ← empty             (all invariants now live in the constitution;
                        local review can propose new ones later)
§T ← old §T, verbatim
§B ← old §B, verbatim
```

### 4. BUILD INITIATIVES.md
```
§A ← migrated
§N ← one row, id N1, slug "migrated":
     phase = build if any §T row is `.` or `~`, else done
     open  = x if phase=build, else .
```

### 5. SHOW DIFF
Show the full new tree and every file's content — `CONSTITUTION.md`,
`INITIATIVES.md`, `initiatives/<today>-migrated/SPEC.md` — before writing
anything. Same show-diff-before-write convention `/sk:review` and
`/sk:deepen` already use. Wait for user OK.

### 6. WRITE
On confirm: create `.smort/`, `.smort/initiatives/<today>-migrated/`, write
the three files.

### 7. STUB THE OLD FILE
Replace root `SPEC.md` with a one-line stub:
```
moved to .smort/CONSTITUTION.md and .smort/initiatives/ — see FORMAT.md
```
Don't delete it outright — git history is the real backup, but the stub is
for anyone with `cat SPEC.md` muscle memory. Delete it in a follow-up commit
once nothing's still reading the old path.

## VERIFICATION

Before declaring done, confirm:
- Every old §V row landed in `CONSTITUTION.md §V`, none dropped.
- Every old §B and §T row landed in the migrated initiative's local §B/§T, none dropped.
- Phase inference matches: any `.`/`~` in old §T → `build`/`x`; all `x` → `done`/`.`.
- `INITIATIVES.md §A` points at `migrated`.

## NON-GOALS

- Not a general-purpose importer — only understands the classic smortkit
  single-file `SPEC.md` shape (§G §C §I §R §V §T §B).
- Never splits the migrated initiative into multiple initiatives — that's a
  manual `/sk:spec new` + `/sk:switch` step later, if the human wants it.
- Never guesses which §V to demote back to local — promotes everything,
  human trims via `/sk:spec amend --constitution`.
- Runs once. A second run on an already-migrated project is a no-op, not a merge.
