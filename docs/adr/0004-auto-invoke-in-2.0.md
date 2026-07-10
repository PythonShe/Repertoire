# 0004 — Auto-invoke in 2.0

## Status

Accepted (2026-07-10). Supersedes [0002](0002-manual-only-descriptions-in-1x.md).

## Context

MANUAL-ONLY (0002) held the line for 1.x, but its mechanism proved self-defeating: the negative-trigger lists embed the very attractor phrases they guard against, and the 1,536-character listing cap silently truncated the two longest descriptions, so the guidance was partly unread anyway. Meanwhile the plugin's growth goal changed the calculus — a skill that only fires when named by users who already know it exists cannot reach new users; growth needs skills that fire when a matching request arrives. The real risk behind MANUAL-ONLY was never invocation itself but unconsented spend: three pipelines (maestro, coda, tuner) dispatch subagents before any user gate, so an accidental trigger used to cost a fleet.

## Decision

v2.0.0 flips every skill to auto-invoke, and moves the safety from the router to the skill:

- **Positive routing.** Each skill carries a positive `description` plus `when_to_use`, together under roughly 1,200 characters — comfortably inside the 1,536-character cap so truncation cannot silently eat the contract again. Adjacency clauses replace negation: instead of "do NOT fire on X", the description names the neighboring skill that owns X, routing the request somewhere rather than merely away.
- **In-skill cost gates.** Maestro, coda, and tuner — the three skills whose pipelines dispatch before any user gate — open with one `AskUserQuestion` confirming scope and cost before the fleet launches. Misfires now cost one question, not a run.
- **Committed regression harness.** Every skill commits trigger evals at `skills/<name>/evals/evals.json` with auto-invoke polarity (positives that must fire), cross-skill confusion cases (requests that must route to the neighbor), and homonym cases. Description edits are validated against these, not against intuition.

The authoring rules live in `docs/authoring/skill-descriptions.md`.

## Alternatives considered

`disable-model-invocation: true` on the expensive skills plus a lightweight router skill that dispatches to them. Rejected twice over: `disable-model-invocation` removes a skill's description from context entirely, so the model cannot even reason about when the skill applies; and the router itself would be routed by the same description matcher it is meant to fix, reintroducing the original problem one level up with an extra hop.

## Consequences

Skills now reach users who don't know their names, which is the point. The precision guarantee of 0002 is traded for a measured precision/recall balance, enforced by the committed evals rather than by policy prose; a routing regression is now a failing eval, not an anecdote. The cost gates make the worst-case misfire cheap but add one interaction to every legitimate maestro/coda/tuner run. And the description contract is tighter, not looser: staying under the combined character budget is a hard authoring rule, because the failure mode for overrunning it is silent.
