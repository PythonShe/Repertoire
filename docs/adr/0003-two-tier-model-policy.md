# 0003 — Two-tier model policy

## Status

Accepted (decided across v1.6.1 → v1.7.0, 2026-06-09 to 2026-07-01)

## Context

Repertoire is quality-first: subagent seats run the strongest models available, and cost is managed by pipeline design (gates, scoped test runs), not by downgrading models. v1.6.1 pinned every seat to Fable 5 by name. Three days later Anthropic suspended Fable 5 access under a US export-control directive, and v1.6.2 had to rewrite every dispatch to Opus at xhigh — proving that hardcoding a model name across seven skills breaks wholesale on the next availability event. When Fable returned, the question was not just "which model" but "how do seats reference models at all". A second pressure shaped the answer: adversarial review only works against a stable baseline — a review panel whose model drifts with the session gives verdicts that cannot be compared across runs.

## Decision

Seats split into two tiers by role, not by skill. Build seats — implementers, fixers, QC and final gates, scouts, clerks, and Tuner's mechanical red→green verifier — dispatch unpinned and inherit the session model, so `/model best` routes Fable 5 where available, else Opus, with no model name baked into any prompt. Review seats — investigators, review panels, and the evidence verifiers (Coda's and Encore's) — stay pinned to Opus for a stable adversarial baseline. Sonnet and Haiku are never used on any seat.

## Consequences

The next model suspension or release is absorbed by the session-model indirection instead of a seven-skill rewrite; the v1.6.1 → v1.6.2 fire drill cannot recur for build seats. Review verdicts stay comparable across sessions and model events because the reviewing model does not move. The costs are accepted deliberately: review seats forgo Fable even when it is available and stronger, and build quality varies with the session's model choice — a user running a weaker session model gets weaker builds, which quality-first tolerates only because the floor is Opus. `best` is session-level vocabulary and is rejected at the Agent-tool dispatch enum, so prompts must not pass it as a `model` value; unpinned means omitting the parameter, not naming `best`.
