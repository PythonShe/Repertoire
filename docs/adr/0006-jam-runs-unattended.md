# 0006 — Jam runs unattended: announce-don't-ask, and no cross-model seat

## Status

Accepted (2026-07-31).

## Context

Every skill in the line through v2.2.0 assumes a user in the loop somewhere load-bearing. Eureka and Libretto are dialogues; Score and Libretto end at approval gates; Maestro needs a written plan handed to it; Presto needs the user to name the one change and pick the approach; Encore needs a named target, a user-picked lens roster, and a set-list gate. The request none of them owns is the standing one: "make this project better — you pick what to do." That mandate has no named target, no plan, no brief, and a user who explicitly does not want to be asked. Routing it anywhere in the existing line either misfires (Encore without a named target) or quietly converts "don't ask me" into a chain of questions.

The second constraint is operational. The line's cross-model Codex seat is dispatched through a CLI runtime that has been observed to hang. In every existing skill that is an annoyance — a user is watching and can interrupt. In a session that is unattended *by design*, a hung seat is fatal: the run stalls with nobody at the wheel, and the whole premise (hand over the keys, come back to a sealed branch) is broken by the one seat that was supposed to add safety.

## Decision

Jam is the autonomous lane, and two structural choices make autonomy safe instead of reckless.

- **The user's judgment is replaced by structure, not omitted.** Discovery is three parallel read-only Opus scouts with forcibly diverse lenses (features, quick fixes, UI/UX polish), each instructed to sit in the *end user's* seat and to anchor every candidate at `file:line`. An Opus selector — not the scouts' enthusiasm, and not the conductor — composes the 3-5 job docket, with a hard non-breaking rule, an L-size ban, and the rejects kept on the record. The conductor may strike a docket job but never add one.
- **Announce, don't ask.** The docket is reported to the user as building begins; it is never an approval gate. The run stops for exactly three things: the auto-invoke cost gate (a user who named Jam has consented), Phase-0 blockers only the user can resolve (a dirty tree, an undiscoverable test command), and QC's two-strike escalation. Every other decision — per-job review, dropping a blocked job, amputating an unshippable one — is announced in passing and proceeds.
- **The conductor never plays.** Jam inherits Maestro's doctrine, not Presto's carve-out. An unattended run is precisely where a controller quietly editing code would go unreviewed the longest; the ADR 0005 reasoning for playing (handoff latency on a change the user already scoped) does not apply when no user scoped anything.
- **Review is discretionary per job, full-strength at the finale.** The selector flags each job `review: required` (behavior, logic, public surface, security, data) or `optional` (docs, copy, cosmetics); the conductor may batch adjacent jobs into one review but may not skip a required one. The finale never thins: two whole-branch reviewers plus an evidence-based QC that builds and tests.
- **No Codex seat; cross-tier instead of cross-model.** Jam seats no Codex agent — the second sanctioned variance after Presto's, for a different reason: hang risk with nobody watching. The reviewer-diversity it buys elsewhere is bought here by pairing a finale reviewer pinned to Opus with a second finale reviewer unpinned on the session model (Fable where available). Same review prompt, different lenses, different model tiers.
- **Prefer shrinking to stalling.** A job that blocks twice is dropped; a job QC cannot pass is amputated by clean revert; the branch ships what is sound. An unattended run that stalls on one stubborn job delivers nothing, and a four-job branch that ships is a success with a footnote.

## Alternatives considered

**Encore with an auto-accepted set list.** Rejected. Encore's user gates are its design, not overhead — the lens roster and set-list pick are how the user's intent enters the run. Hollowing them out produces an Encore that builds what nobody chose, and it still requires a named target Jam's mandate does not supply.

**Auto-chaining Eureka → Libretto → Score → Maestro.** Rejected. The line's skills explicitly never auto-chain, and their artifacts are dialogue-shaped: Eureka's funnel and Libretto's section-by-section design assume a user answering. Chaining them headless would silently answer the user's questions on the user's behalf — worse than a skill that is honest about replacing judgment with structure.

**Keeping the Codex seat behind a timeout.** Rejected. The conductor has no reliable way to bound a hung CLI call from inside the loop, and a watchdog subagent babysitting a reviewer doubles the cost of the seat it guards. Graceful degradation ("if Codex returns nothing, proceed") does not help when the failure mode is *not returning*.

## Consequences

`shared/invariants.md` now records two no-Codex variances, and ADR 0005's "the only skill without a Codex agent" is superseded on that one fact (ADRs stay immutable; this one records the supersession). Presto's SKILL.md, the README's Codex bullet, and CLAUDE.md all state the pair. Adjacency is reciprocal from day one: eureka, encore, and presto gain a jam clause and a boundary eval each, and jam's own evals hold the reverse cases.

The honest risk is docket quality: with no user gate, a bad docket ships bad work politely. The defenses are layered — the user's-seat stance and mandatory `file:line` anchors in the scouts, the selector's verification right and non-breaking/L-ban rules, per-job review defaulting to required when unsure, the never-thinned finale pair, QC's amputation power, and the two-strike escalation. If Jam is ever compressed, the clauses to defend are: the finale pair never thins, announce-don't-ask has exactly three stops, and drop-don't-stall (with amputation) is what keeps autonomy from becoming stubbornness.
