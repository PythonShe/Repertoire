# Changelog

One paragraph per release, newest first. Each entry explains why the release happened, not what every commit touched — the git log holds the diff-level detail.

## v2.0.0 — 2026-07-10 (unreleased)

The flip. Every skill drops MANUAL-ONLY and becomes auto-invocable: the long negative-trigger descriptions are rewritten as a positive `description` plus `when_to_use`, sized against the 1,536-character listing cap — and the audit that preceded the rewrite discovered the cap had been silently truncating the two longest 1.x descriptions all along, so part of the negative guidance the old policy relied on was never reaching the router in the first place. Adjacency clauses that name the neighboring skill replace negation, and the doctrine behind the rewrite is committed at `docs/authoring/skill-descriptions.md`. Because a skill that can now fire on its own must not spend money on its own, the three pipelines that dispatch subagents before any user gate — maestro, coda, tuner — gain an in-skill cost gate: one `AskUserQuestion` before the fleet launches. Routing quality becomes a regression surface rather than a one-off tuning exercise: every skill commits trigger evals at `skills/<name>/evals/evals.json`, with auto-invoke polarity, cross-skill confusion pairs, and homonym cases. The release also pays down run cost ahead of the wider audience auto-invoke will bring: implementer and fixer test runs are scoped to the touched areas (the full suite runs once, at the QC gate), QC gains a retry-round mode that close-reads only the new fixes, Maestro skips its Phase-1 fixer entirely on a clean reviewer PASS, and four seats are retuned down a tier — the triage scout and feedback clerk to medium, Tuner's verifier and Eureka's ideators from xhigh to high. Finally, the seven dot pipeline graphs are replaced by phase-numbered at-a-glance checklists (same topology, roughly a fifth of the tokens, no more graph/prose drift), and the per-skill codex-reviewer prompts are consolidated into one `shared/` contract instead of seven drifting copies.

## v1.7.0 — 2026-07-01

Fable 5 came back, and instead of hardcoding it again the plugin learned from the suspension: a two-tier model policy. Build seats (implementers, fixers, QC and final gates, Tuner's scout, Eureka's ideators, Coda's feedback clerk) dispatch unpinned and inherit the session model, so `/model best` routes Fable-where-available, else Opus, with no model name baked in to break on the next suspension. Review seats (reviewers, evidence verifiers, Tuner's investigator and fix reviewer, the vetting/spec/plan panels, Encore's hunters) stay pinned to Opus for a stable adversarial baseline. Never Sonnet; effort stays xhigh.

## v1.6.2 — 2026-06-16

An externally forced release: Anthropic suspended Claude Fable 5 access on 2026-06-12 under a US export-control directive, four days after v1.6.1 had put Fable on every seat. Every dispatch moved to Opus at xhigh — the strongest model still available, at maximum reasoning depth — with a temporary notice to switch back when Fable returned. The "only xhigh seat" framing for Tuner's investigator was dropped, since every seat was now xhigh.

## v1.6.1 — 2026-06-09

A pure model retune, two days after the pipeline was declared complete: every Claude subagent seat moved to Fable 5 at high effort, with xhigh reserved for Tuner's investigator to keep parity in the cross-model root-cause duel against Codex. The same window committed the Eureka cost-and-reuse shortlist that would seed the 2.0 line — consolidating the Codex contract into shared parts was already the champion idea.

## v1.6.0 — 2026-06-07

Encore closed the 1.x line — the curtain call. A post-performance enhancement pass over a finished codebase: a scout profiles the target, an adaptive lens roster is agreed with the user, parallel Opus hunters find opportunities, a verifier kills anything the codebase refutes, and only what the user picks at the set-list gate gets built. With it the music line stood complete: Eureka → Libretto → Score → Maestro → Coda → Encore, plus Tuner off to the side for debugging.

## v1.5.0 — 2026-06-07

Eureka gave the pipeline its opening movement: everything downstream assumed the user already held an idea worth speccing, and discovery was the missing phase. It is deliberately conversation-heavy — seed sketches, a running idea board, on-demand ideator bursts — ending in a vetting funnel where every kill verdict needs user confirmation, and its terminal artifact is a ranked shortlist handed to Libretto, never a spec. The docs also went multilingual in this window (zh-CN, zh-TW, ja READMEs with a language switcher).

## v1.4.0 — 2026-06-06

Tuner added the first skill outside the production line: a cross-model debugging conductor that races a Codex investigator against a triage-primed Opus investigator, cross-examines their rival root-cause hypotheses as the confidence gate, and lands the fix behind a failing-test-first fixer and a red→green verifier. The release also refactored Coda's ending — a staged final verdict, with the broad re-review folded into QC — shortened the plugin description as the catalog entry grew unwieldy, and documented the honest operating cost: a Codex plugin and Max/API billing are recommended for these token-heavy skills.

## v1.3.0 — 2026-06-06

Coda extended the pipeline past the merge gate into the PR conversation. Its stance is that review feedback is a claim, not an order: every formal review, inline thread, and failing check is verified against codebase reality by a read-only Opus verifier before anything is fixed — no blind implementation, no performative agreement — and pushing and posting the drafted replies sit behind one user approval. The release also closed Score's own review-panel findings, eating the plugin's dog food.

## v1.2.0 — 2026-06-06

Score filled the gap between Libretto's spec and Maestro's build: an approved spec becomes a decision-complete implementation plan — movements of Maestro-ready task groups with exact paths, interfaces, and test expectations, but no function bodies — hardened by the same adversarial panel pattern (2-3 Opus lenses plus a cross-model Codex reviewer) behind a user-approval gate. Adapted from superpowers writing-plans; Libretto's handoff was rewired to point at it.

## v1.1.0 — 2026-06-03

Libretto made Repertoire a pipeline rather than a single skill: a guided design dialogue that turns a rough idea into a build-ready spec, hardened by an adversarial review panel before a final approval gate. Adapted and compacted from superpowers brainstorming, it set the shape everything after it followed — the controller does the thinking, subagents do the reading and the skepticism, and the terminal artifact is a document, never code.

## v1.0.2 — 2026-06-03

Marketplace-plumbing corrections from watching the `/plugin` UI: the catalog entry wasn't surfacing the plugin version, so it was added there and the entry enriched (category, tags, homepage), descriptions were compacted, and the lesson was written down as the two-place version rule — `plugin.json` drives resolution, the `marketplace.json` entry drives what the UI shows, and the two must be bumped together or they drift.

## v1.0.1 — 2026-06-03

Made the repo publishable as an open project: an Apache-2.0 LICENSE, a maintainer email in the manifest, and a CLAUDE.md guide so future sessions understand the one-plugin topology before touching it.

## v1.0.0 — 2026-06-03

The founding release: the Maestro skill — a conductor that executes an implementation plan through fresh implementers, an adversarial review panel, and an evidence-based QC merge gate, never reading code itself — packaged as the Repertoire plugin shipping through its own one-entry marketplace. The repo root is simultaneously the marketplace root, the plugin root, and the source tree, which is the topology decision everything since has leaned on: adding a skill never touches the marketplace.
