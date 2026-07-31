# 0007 — Legato borrows its taste, and keeps a record

## Status

Accepted (2026-07-31).

## Context

Motion polish is a request the line kept half-answering. "The dropdown feels sluggish" is too small for the spec-to-plan-to-build pipeline, not a bug (Tuner), not a general enhancement pass (Encore), and Presto — the right size — has no opinion about what good motion *is*. The work is peculiar: the diffs are tiny, the judgment per line is enormous, and nothing in a test suite can tell a right-feeling 200ms `ease-out` from a faintly-broken 400ms `ease-in`. A skill for this work needs a source of taste that is more stable than whatever the session model improvises, and a way to keep one session's decisions from being re-argued by the next.

Emil Kowalski's MIT-licensed skills repository ([emilkowalski/skills](https://github.com/emilkowalski/skills)) already encodes that taste operationally — exact curves, duration bands, spring configs, a frequency gate, ten reviewable standards. The question was how to use it, and where a run's decisions should live afterward.

## Decision

Legato is the motion lane, and it makes four deliberate calls.

- **Borrowed taste, bundled verbatim.** The operational files — `STANDARDS.md` (review-animations), `AUDIT.md` (improve-animations), and the animation vocabulary (minus its upstream frontmatter, otherwise verbatim) — are copied into `skills/legato/references/` with provenance headers, under the upstream MIT license reproduced in `references/LICENSE`. The rest of the upstream material — emil-design-eng, apple-design, find-animation-opportunities, pick-ui-library, and the workflow scaffolding of review-animations and improve-animations — is distilled into the prompt files instead, with the same attribution. The copies are never edited except to re-sync upstream; agents cite them by path and copy values exactly — "never approximate a value that appears in the references" is the skill's load-bearing rule, and it only holds if the references are stable, citable files rather than paraphrase.
- **The record lives in the target project.** Phase 7 writes `docs/repertoire/animation.md` into the project the run polished: the motion language, per-surface decisions, and rejected directions. It is standing guidance, not a changelog — a changed decision supersedes its row in place, a rejected direction is recorded once so no future session re-proposes it, and the doc must not grow run over run unless decisions actually changed. Phase 0 of every later run reads it and honors it. A pointer from the project's `CLAUDE.md` or `AGENTS.md` is offered exactly once, at first creation, behind an AskUserQuestion — Legato never edits those files without that consent.
- **The conductor never plays, even at Presto's size.** Presto's carve-out (ADR 0005) rests on handoff cost: dispatching a subagent to reconstruct context the controller already holds is pure latency. Legato's gate dissolves that argument — the approved motion spec *is* the context, complete with exact values, so the implementer's brief costs nothing to assemble and a fresh window holds it better than a controller mid-run. The genuinely trivial case (one obvious property, one element, a right answer the references settle) is handled below the floor: skip the pipeline entirely and say so, which is Presto's floor rule, not its carve-out.
- **Eyes when available, never manufactured.** Feel cannot be fully judged from a diff, so the scout may watch the real motion and the reviewer may verify it — when the app is already reachable in a browser (`VIEW`). No agent ever starts a dev server to get a view, and a run without one degrades to code-only silently, saying plainly which feel judgments were not made. Legato also seats no Codex reviewer, sharing Presto's sanctioned variance for Presto's reason: the cross-model pass earns its wall-clock over a whole branch, not one scoped polish.

## Alternatives considered

**Referencing the locally-installed emil skills instead of copying.** Rejected: the plugin ships to machines that do not have them, and a skill whose craft bar exists only on the author's laptop is broken on arrival everywhere else.

**Copying all seven upstream skills wholesale.** Rejected: ~110KB of reference text in the plugin for material that is mostly posture and prose. The operational files carry the exact values agents must cite; the philosophy compresses into prompts without losing what agents actually need at runtime.

**Distilling everything, copying nothing.** Rejected: distillation loses the exact numbers — and the copied-never-approximated rule needs a file to point at. It also drifts from upstream invisibly, and it makes attribution vaguer, not cleaner.

**Making the record a run log.** Rejected: an append-only log grows without bound and buries the current truth under history — precisely the failure the git log already covers. Future sessions need what *is* decided, in one screenful.

## Consequences

The repository now carries third-party MIT-licensed material, and the obligations travel with it: `references/LICENSE` and the provenance headers must survive any restructuring, and upstream changes arrive only by deliberate re-sync. Legato is also the first skill that writes a durable artifact into the *target* project beyond a branch — `docs/repertoire/animation.md` is cross-session state, readable by any future agent, which is its value and its risk. The supersede-in-place contract in `guidance-template.md` is what keeps it from decaying into a changelog; defend that contract first if the skill is ever compressed. Adjacency went reciprocal in the same release: presto, encore, and jam each gained a legato clause and a boundary eval, and `shared/invariants.md` records the third no-Codex variance alongside legato's pinned-Opus scout. The remaining honest risks are a scout or reviewer tempted to start a dev server to manufacture a `VIEW` (forbidden in both prompts and the red flags), and value drift — an agent "improving" a curve from memory — which the citable references and the QC gate's exact-values check exist to catch.
