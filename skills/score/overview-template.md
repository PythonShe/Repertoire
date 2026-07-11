# Overview Template (plan sets only)

The structure Score writes `00-overview.md` in — authored by you, never by a
plan writer. The overview is not a digest of the plans; it carries the
**global information no single plan can**: what the set delivers, the order
the plans execute in, and the contracts that cross plan boundaries. Each plan
is conducted by a separate Maestro run that never reads its siblings — the
overview is the only shared ground. Scale each part to the work and delete
sections that genuinely don't apply.

```markdown
# <Title> — Plan Set Overview

> **For the conductor:** this goal ships as [N] coordinated plans, listed
> below in execution order. Conduct one plan per `/repertoire:maestro` run,
> in dependency order, and read this overview before any plan. This file
> defines the cross-plan contracts — it contains no tasks itself.

**Spec:** `docs/repertoire/specs/<file>.md` (or: derived from conversation — see Goal)
**Goal:** one sentence — what the whole set builds.
**Architecture:** 2-4 sentences on the global approach and why the work
splits along these seams.
**Build:** `<exact command>` · **Test:** `<exact command>`

## The plans

| # | Plan | Delivers | Depends on |
|---|------|----------|------------|
| 01 | `plan-01-<slug>.md` | one line | — |
| 02 | `plan-02-<slug>.md` | one line | 01 |

Execution notes, only where the table alone would mislead: which plans could
run on parallel branches, what "done" looks like after each lands.

## Shared contracts

Every interface, data shape, schema, or protocol that crosses a plan
boundary, spelled out exactly, with its defining and consuming plans named —
e.g.:

- `StationClient.stream(id: StationId) -> AsyncIterator[Frame]` — defined in
  plan 01, consumed by 02 and 03. `Frame` has `data: bytes`, `pts_ms: int`.

The defining plan implements the contract; consuming plans restate it at the
point of use, exactly as written here.

## Shared conventions & constraints

Project-wide rules every writer and implementer must respect that the spec or
the codebase doesn't already make obvious: error-handling idiom, naming,
config surface, dependency policy.

## Out of scope

What the set deliberately does not cover — so no plan quietly absorbs it.
```

Conventions worth keeping to:

- **The overview is a contract document, not a summary.** If a line wouldn't
  change how someone writes or executes a plan, cut it.
- **Number plans in execution order**; the overview is `00` so it sorts first.
- **A shared contract lives here or nowhere.** A shape defined only inside
  plan 01 that plan 03 consumes is a seam failure — hoist it into this file.
- **The header's Build/Test commands are load-bearing** here exactly as in a
  single plan — and each plan file repeats them in its own header, because
  Maestro reads only the plan it is conducting.
