# 0005 — The controller plays, in Presto only

## Status

Accepted (2026-07-25).

## Context

Every skill in the line through v2.1.0 shares one invariant: the conductor never plays. Maestro, Tuner, Coda, and Encore all state it as a core principle, and it earns its keep — a controller that reads source files and edits code burns the context it needs to coordinate a long run, and a controller that reviews its own work is not reviewing anything.

That invariant was written for runs with ten or more seats, where the controller's context is the scarce resource. It generalizes badly downward. A user asking for a `--json` flag on one command gets, under the existing line, either the full Score → Maestro pipeline (a written plan, task groups, a four-reviewer panel — for a change of thirty lines) or nothing at all: Maestro's own guidance says a small one-off task needs no pipeline, and then names no door. The gap is real and it is where a large share of ordinary requests actually live. Meanwhile the cost of holding the invariant at that scale inverts: dispatching a subagent to make a three-line edit means paying for it to reconstruct context the controller already holds, and paying again in the round trip.

## Decision

Presto is the fast lane, and it breaks the invariant deliberately and narrowly.

- **A size gate, not a judgment call.** The controller implements directly only when all four terms hold — recon left no open questions, the change is confined to roughly ≤3 files and ≤150 lines, it lives in one module, and it introduces no new subsystem, dependency, schema, or public interface. Fail one term and an implementer subagent takes it. Discovering mid-build that the work outgrew the gate is a documented stop-and-hand-off, not a licence to continue.
- **The review is never self-served.** The one seat Presto refuses to compress is the reviewer: always a fresh Opus subagent, always dispatched, and most emphatically when the controller was the author. The carve-out is about who writes code, never about who checks it.
- **A bounded ensemble, and a door out.** Three agents at the floor, six at the ceiling. A run that wants a seventh seat, a second review round, or a third scout has outgrown the fast lane; the QC gate's strike budget is two rather than three for the same reason, and its escalation names `/repertoire:maestro` explicitly. Escalating is a successful Presto run.
- **No cross-model seat.** Presto is the only skill without a Codex agent. That pass earns its wall-clock over a whole branch built by a fleet; on one scoped change it becomes the slowest step in a pipeline whose premise is speed.

## Alternatives considered

**Widening Maestro to accept plan-less work.** Rejected: Maestro's phases are built around task *groups* derived from a written plan, and its safety comes from a four-reviewer panel over an integrated branch. Making the plan optional would fork its every phase on "is there a plan", and the result would be two skills sharing one SKILL.md badly.

**Holding the invariant and just shrinking the ensemble.** A Presto that still dispatched every edit would seat one implementer for a three-line change and pay full context-reconstruction cost for it. That is the specific waste this skill exists to remove; removing everything *except* it would have produced a slightly cheaper Maestro rather than a genuinely different room.

**Dropping the reviewer for small changes, keeping the controller as author.** Rejected outright. Ensemble size is the dial Presto turns; scrutiny is not. A pipeline with one review is one skipped dispatch away from having none.

## Consequences

The line gains a floor it did not have, and the routing surface gains a fourth confusable neighbor — Presto's evals carry the maestro / tuner / encore boundaries plus a below-the-floor case ("rename this variable" triggers nothing, because trivial work deserves no pipeline at all). The `shared/invariants.md` model policy now records two sanctioned variances rather than one: Presto pins its recon scouts to Opus, since their approach recommendation is the only investigation the run gets, and it seats no Codex agent.

The honest risk is the size gate's terms drifting in practice — a controller that reads "roughly ≤3 files" generously will play its way into work that deserved a subagent, and the stop-and-hand-off rule is what has to catch it. That rule, and the always-dispatched reviewer behind it, are the two clauses to defend if this skill is ever compressed.
