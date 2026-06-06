# Plan Template

The structure Score writes plans in. Decision-complete, not code-complete:
every section makes a decision an implementer would otherwise have to guess —
paths, interfaces, data shapes, named tests, verify commands — and stops short
of function bodies. Scale each part to the work and drop what genuinely doesn't
apply; a small plan is *short*, not padded. The goal is a document a fresh
implementer with zero conversation context can build from without asking what
you meant.

```markdown
# <Title> — Implementation Plan

> **For the conductor:** this plan is structured into movements — task groups
> sized for one fresh implementer each, with clean seams between them. Execute
> with `/repertoire:maestro` (or any plan executor). Tasks use checkbox syntax
> for tracking.

**Spec:** `docs/repertoire/specs/<file>.md` (or: derived from conversation — see Goal)
**Goal:** one sentence — what this builds.
**Architecture:** 2-3 sentences on the approach, lifted from the spec's decision.
**Build:** `<exact command>` · **Test:** `<exact command>`

## File structure

Every file the plan touches, one line each on its single responsibility:

- Create `exact/path/one.py` — role.
- Modify `exact/path/two.py` — what changes and why.

## Movement 1: <name>

What this movement delivers and why these tasks travel together.
**Depends on:** nothing | Movement N.

- [ ] **Task 1.1 — <component>**
  - **Files:** Create `exact/path.py` · Test `tests/exact/test_path.py`
  - **Behavior:** what it does, in a few sentences. Every public interface,
    signature, and data shape spelled out exactly as later tasks will
    reference it — e.g. `parse(raw: str) -> Receipt`, where `Receipt` has
    `total: Decimal`, `vendor: str | None`.
  - **Error handling:** each failure mode that matters, and what handles it.
  - **Tests:** each test by name with the behavior it pins down and its key
    assertion — e.g. `test_expired_token_rejected`: POST with expired JWT →
    401, body `{"error": "token_expired"}`.
  - **Verify:** `exact command` → expected outcome.

- [ ] **Task 1.2 — <component>**
  - …same shape. Repeat any interface from 1.1 that this task consumes —
    implementers read tasks in isolation.

## Movement 2: <name>

…

## Notes for the conductor

Only if needed: risks worth watching, sequencing warnings, assumptions carried
over from the spec's open questions. Not a dumping ground — each line should
change how someone executes.
```

Conventions worth keeping to:

- **Number tasks `<movement>.<task>`** so reviews and fixes can point at them
  unambiguously.
- **Repeat interfaces at the point of use.** A task that consumes `Receipt`
  restates its shape, even though Movement 1 defined it — cross-references die
  when tasks are pasted into separate implementer contexts.
- **The header's Build/Test commands are load-bearing.** Maestro's Phase 0
  reads them from the plan before falling back to the README or manifest; an
  exact command here saves every downstream agent from guessing.
- **An empty "Notes for the conductor" is deleted,** not left as a heading.
