# 0002 — MANUAL-ONLY descriptions in 1.x

## Status

Superseded by [0004](0004-auto-invoke-in-2.0.md) (decided 2026-06-03 with the first skill; recorded 2026-07-10)

## Context

Every Repertoire skill is a conductor: a single invocation dispatches a fleet of subagents — implementers, adversarial review panels, cross-model Codex reviewers, QC gates — and a run is expensive in both tokens and wall-clock time. A skill description is the routing surface Claude reads when deciding whether to auto-invoke, and generic requests ("execute this plan", "fix this bug", "address the review comments") match these skills' territory exactly. Auto-firing a subagent fleet on a request the user meant casually was judged too risky for the 1.x line.

## Decision

All seven skills carry MANUAL-ONLY descriptions: the skill fires only when the user names it (or runs its slash command), and each description embeds a long negative-trigger list spelling out the generic phrasings it must not fire on, plus homonym guards ("score" as a rating, "coda" as the app, "tuner" as audio tuning). Trigger evals were run against this policy and showed roughly 0 recall with 100% precision — by design: recall is irrelevant when invocation is meant to be explicit, and precision is everything.

## Consequences

The policy worked as intended for 1.x: no accidental fleet dispatches, and the eval numbers confirmed the routing contract held. Two consequences were only learned later, and they motivated the reversal in 0004. First, a negative list embeds the very attractor phrases it guards against — a description that says "do NOT trigger on 'execute this plan'" still puts "execute this plan" into the matcher's context, pulling attention toward the skill it is trying to fence off. Second, the plugin listing enforces a 1,536-character cap per description, and the two longest descriptions were silently truncated without anyone noticing — meaning part of the negative guidance the policy depended on was never reaching the router at all.
