# 0001 — Record architecture decisions

## Status

Accepted (2026-07-10)

## Context

Repertoire has accumulated decisions whose reasoning outlives the commits that made them: why every 1.x skill was manual-only, why model seats split into two tiers, why 2.0 flips to auto-invoke. The git log preserves what changed; the why lives in commit bodies, memory notes, and heads — none of which a new contributor (or a future session) can be expected to reconstruct. When a decision is later reversed, the reversal only makes sense next to the original rationale.

## Decision

We record architecturally significant decisions — those that shape the plugin's structure, routing behavior, model policy, or cost posture — as numbered Markdown files in `docs/adr/`, in the format Michael Nygard described: Title, Status, Context, Decision, Consequences. Numbers are sequential and never reused. A superseded ADR is not deleted or edited into agreement; its Status is changed to point at the ADR that replaced it, preserving the arc.

## Consequences

Decisions become reviewable in the same pull request that implements them, and reversals must confront the original reasoning explicitly. The cost is the discipline of writing an ADR when a significant decision is made — cheap, but nonzero, and the set only stays trustworthy if it is kept current.
