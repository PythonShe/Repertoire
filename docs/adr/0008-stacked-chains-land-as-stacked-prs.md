# 0008 — Stacked chains land as stacked PRs, and the merge stays the user's

## Status

Accepted (2026-08-03)

## Context

Score has been able to split one goal into a plan set since v2.1.0, and the
overview template has always said "conduct one plan per Maestro run" — but the
set's *dependent* case never had an execution substrate. When plan 02 consumes
plan 01's code, the second Maestro run had two bad options: branch off plan 01's
**unmerged** branch by hand — a hand-rolled stack whose reviews and QC see the
sibling's diff muddled into their own, with every rebase after a plan-01 fix
falling on someone manually — or wait for the user to merge plan 01 between
runs, serializing the whole set on human availability. Maestro's own doctrine
("pushes but never merges") is what makes the wait real: the conductor cannot
unblock itself, by design.

On 2026-07-30 GitHub moved **stacked pull requests** into public preview for
all repositories: an ordered chain of PRs where each targets the branch below,
each layer shows only its own diff, a merged lower layer triggers a
**server-side** cascading rebase-and-retarget of everything above it, and the
user can land one layer or the whole stack in one click. Tooling is the
`gh stack` CLI extension (with a non-tracking `gh stack link` for branches
managed externally), REST/GraphQL endpoints, and per-layer branch protections.
Constraints as of this writing (per GitHub's changelog and docs at preview
time): same-repo branches only, merge-queue support still rolling out, and the
whole feature is preview-grade.

## Decision

Plan sets declare an **execution shape** in `00-overview.md`: `parallel` (the
default — independent plans, branches off main, unchanged behavior) or
`stacked chain`. Score's overview template defines both and forbids stacking
independent plans — a stack fabricates an ordering every review and merge must
then honor.

On a stacked chain, each Maestro run still conducts exactly one plan, and a
**dependent** plan lands as one layer of a stack: Phase 0 branches off the
**parent plan's branch** (the sibling its overview row depends on) and
captures `BASE` against it, so the panel and QC see exactly one layer's diff —
the same `BASE..HEAD` contract as today, pointed one branch down. Plans in the
set with no dependency branch off main as in `parallel`, so a mixed set needs
no third shape. Phase 3's push gains one step: `gh stack link`, chosen over
full `gh stack` local tracking because it leaves Maestro's branch/commit/push
workflow byte-for-byte intact.

Three guardrails, in order of importance:

1. **`gh stack merge` is forbidden.** It is a merge. "Pushes but never merges"
   survives unreworded — the user lands every layer, now with GitHub doing the
   cascading-rebase chore and offering the one-click whole-stack landing.
2. **A dirty stack is a stop-and-ask.** Maestro runs are separate sessions, so
   a cascading-rebase conflict (plan 01's fixer committed after plan 02
   branched) lands *between* runs with no owner. Phase 0 checks with read-only
   git metadata only; on a missing parent branch or a conflicted/mid-rebase
   stack, the conductor stops and asks — it never runs a stack sync or
   resolves a rebase itself.
3. **Graceful degradation, said out loud.** The feature is public preview.
   Without the `gh stack` extension or the preview, the fallback is a plain
   push with the PR targeting the previous plan's branch — the chain still
   works, minus the auto-retarget — and the report names the missing link.
   Nothing in the pipeline hard-depends on preview behavior.

## Consequences

The dependent plan set becomes conductable without either muddied review diffs
or user-availability stalls, and the reviewer economics improve: each layer's
panel reads a clean, single-plan diff. The costs: a preview-grade external
feature now appears in Maestro's instructions and may change under us before
GA — the plain-push fallback is the hedge, and the guidance should be
revisited when stacked PRs go GA (merge-queue interaction especially). The
between-runs conflict case is escalated, not solved; if it proves frequent, a
future revision could add a dedicated restack step with user consent rather
than a bare stop.
