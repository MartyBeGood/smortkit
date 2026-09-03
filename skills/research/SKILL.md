---
name: research
description: |
  Gather external knowledge the spec needs and distill it into §R — the durable
  research log — so build grounds in facts instead of hallucinating library
  behavior. Each finding cites a source; unsourced claims are flagged, never
  written as fact. Triggers when a spec decision hinges on a library/API/best
  practice the agent is unsure of, when the user says "research this", "what's
  the best lib for…", "check current best practice", or invokes /sk:research.
  Defers the §R write to the spec skill.
---

# research — external knowledge → §R

**Every finding cites a source. No source → flag it `?`, never write a guess as fact.**

Target: the active initiative (`.smort/INITIATIVES.md §A`) — §R is always
local, never written to the constitution.

"Process without library context gives you well-organized hallucinations."
Build invents a plausible-but-wrong API & §B fills with avoidable bugs.
Research is the external oracle: pull the real fact once, log it caveman, never re-derive.

## WHEN TO RESEARCH

- A §C/§I/§V decision hinges on a lib, API, version, or pattern you are unsure of.
- You are about to assume how an external dependency behaves.
- The idea touches a domain with real prior art (auth, payments, crypto, rate-limit).
- `/grill` parked a `?` that the outside world must answer.

Skip when the build touches only code you already wrote. Research scales to the unknown, never to habit.

## FOUR STEPS

### 1. SCOPE
Turn the unknown into 1-3 concrete questions. Vague "research auth" → "JWT lib
for Node ESM, maintained?" + "refresh-token rotation: current best practice?".
A scoped question gets a citable answer; a vague one gets an essay.

### 2. GATHER
Use web search / docs tools. Prefer primary sources: official docs, the repo,
the RFC, the paper. Two independent sources beat one confident blog. For a big
sweep, spawn a sub-agent so the raw pages never touch this context — it returns
only the distilled finding + source.

### 3. DISTILL
Crush each answer to one caveman line + its source. Drop the prose. The §R row
is the memory; the tab you read is not.

> R3|refresh token|rotate on use, revoke family on reuse-detect|datatracker.ietf.org/doc/html/rfc6819#section-5.2.2.3

### 4. HAND OFF
Emit the §R rows & hand to the **spec** skill to append to the active
initiative's local §R. If a finding changes a constraint or interface, note
the local §C/§I edit for spec too. Research proposes; spec writes.

## SOURCE DISCIPLINE

- Cite a URL, repo, RFC, or paper per row. Verbatim identifiers/versions.
- Could not verify → write the row but flag `?` in the finding & say so. An
  unverified claim labeled honestly is fine; one disguised as fact is a future §B.
- Conflicting sources → log both, let the user pick. Never silently average them.

## WHEN TO STOP

Done when every scoped question has a sourced §R row (or an honest `?`), and no
build decision still rests on an unchecked assumption. Never research past the
questions you scoped — that is just burning the attention budget.

## BOUNDARIES

- Never write SPEC.md. Hand §R rows to spec.
- Never write a finding as fact without a source.
- Never dump raw pages into context or §R. Distill or it does not land.
- Never research what you can read in the repo. Local truth > web guess.
