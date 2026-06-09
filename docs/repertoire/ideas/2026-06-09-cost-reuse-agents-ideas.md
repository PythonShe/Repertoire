# Cost & Reuse — Plugin Agents + Shared Prompts

**Status:** FINAL (Eureka funnel complete)
**Date:** 2026-06-09
**Range:** Anchored — enhance the Repertoire plugin via artifact types it doesn't yet ship.
**Posture:** Anchored (existing repo).
**Dimension:** Cost & reuse — plugin agents + shared prompts.

**Identity brief:** Repertoire is a single Claude Code plugin bundling 7 token-heavy,
MANUAL-ONLY skills that form an idea→spec→plan→build→review→enhance→debug pipeline,
themed on musical production. It serves developers who want structured,
ensemble-driven workflows with adversarial cross-model review (diverse-lens Opus
skeptics + a Codex seat). It refuses to auto-trigger heavy skills, to merge code
without explicit approval, and to add a build step.

**Design rules (this run):**
- **R1 — Selective extraction.** A role becomes a named agent only when its contract
  is stable and reusable. Subagents whose instruction is mostly task-specific /
  deferred per dispatch keep their standalone inline prompts.
- **R2 — Manual-only is the 1.x invariant.** New agents must not auto-delegate in the
  1.x line. Auto-invoke is a 2.0.0 concern, paired with recommending users disable
  overlapping plugins (e.g. superpowers). NOTE (feasibility-confirmed): agents have
  **no** auto-invoke off-switch (`disable-model-invocation` is skills-only), so R2 is
  enforced purely through disciplined MANUAL-ONLY `description` phrasing — a soft
  contract, exactly how the SKILLs self-police today.
- **R3 — Quality is non-negotiable.** Every judgment seat stays Opus; no Haiku /
  model-downshift in the roster. (This rule is what killed Tiered Ensemble.)

---

## Finalists (ranked)

### 1 · Shared Parts  ·  size S  ·  ADVANCE
**Pitch:** A shared reference layer holding the cross-cutting invariants that repeat
verbatim across skills, plus **one canonical Codex cross-model reviewer contract**
instead of the current ~6 near-identical copies; SKILL.md files and prompts link or
paste it rather than each restating it.

**Rough shape:** A top-level shared dir (e.g. `_shared/`) holding `codex-reviewer.md`
(the verbatim skeleton: adversarial-review runtime selection, the
`codex-companion.mjs` availability check, the "empty stdout = failed reviewer, not a
pass" rule, the `codex exec --cd` fallback) and small snippets (compact-report
format, evidence-not-vibes / no-performative-agreement, push-never-merge +
branch-naming, panel-dispatch contract). Each skill states only its thin per-skill
slots — object (branch / PR / plan), timing/scope rule, and finding-id format — in
~10 lines.

**Admitted trade-off:** Skills paste bundled files at runtime, so a shared file the
conductor pastes (rather than the subagent reads) must keep its per-skill slots
obvious or a skill ships an unsubstituted placeholder.

**Amendments (from vetting):**
- *Feasibility:* cross-directory shared reference files **work today** — verified
  against the `waza` plugin's top-level `rules/` referenced from sibling skills via
  relative path. No `plugin.json` registration needed. The simpler, skill-agnostic
  mechanism (a top-level `_shared/` dir) is preferred over an agent's `skills:`
  frontmatter (which only reaches agent-based roles).
- *Feasibility:* the 3 Codex prompts read (maestro/coda/score) share a **verbatim**
  skeleton and diverge only in object / timing / id-format — the canonical-contract +
  thin-params shape is confirmed feasible.
- *MVP cut:* start with the **Codex contract consolidation alone** (highest
  duplication, ~6 copies, lowest risk) + the compact-report snippet; defer the
  evidence-not-vibes and push-never-merge snippets to a follow-up once the linking
  pattern is proven.
- *Maintenance:* keep shared paste-ready snippets marked with loud `[FILL]` markers
  to prevent the unsubstituted-placeholder failure the Score Codex prompt warns of.

**Vetting card:** identity `fits` · compliance `none` · demand: real, **high**
confidence, maintainability-not-tokens, cleanest-justified of the slate (6× Codex
copies already diverging; evidence-policy language in 23 files, report-format in 26)
· feasibility `S` · **recommendation: ADVANCE** (ship first — lowest risk, proves the
linking pattern).

### 2 · The Roster  ·  size M (full) / S (MVP)  ·  ADVANCE
**Pitch:** Extract the recurring, stable-contract subagent roles into named
`agents/*.md` definitions; skills dispatch by `subagent_type: repertoire:<role>` and
pass only task-specific parameters inline, instead of pasting a full `*-prompt.md`
every dispatch.

**Rough shape:** New `agents/` dir at plugin root. Ship the **code-diff
`repertoire:reviewer`** first as POC (the diverse-lens skeptic shared by Maestro /
Coda / Encore + Maestro's Phase-1 broad reviewer — same scope model, same
"assume-wrong-until-proven", same `file:line · severity` report). Wire only Maestro's
Phase-1 + Phase-2 to it; leave the others untouched until the POC proves out. Add
`fixer` as the clean second extraction.

**Admitted trade-off:** Over-abstraction risk and a layer of indirection; the
reviewer fragments by object, so the roster must stay selective (R1).

**Amendments (from vetting):**
- *Demand (reframe):* the **per-run token pitch is dropped** — an agent's body still
  loads into the subagent at every dispatch, so the only token win is leaner
  *controller* context. The honest benefit is **maintainability + consistency** (the
  6 diverged Codex copies show the drift this cures).
- *Feasibility/R1 — scope:* `repertoire:reviewer` = the **code-diff skeptic only**.
  Score's plan-reviewer is a different artifact → stays inline (or a separate
  `repertoire:plan-reviewer` later, not folded in). `verifier` is **three different
  jobs** (Coda fixes-vs-feedback, Encore calls-vs-reality, Tuner red→green) → do not
  unify. `implementer` exists only in Maestro → extraction is premature. Extraction
  order: **reviewer (POC) → fixer → maybe qc**.
- *Feasibility/R2:* no auto-invoke off-switch for agents → MANUAL-ONLY enforced purely
  via `description` phrasing (soft contract).
- *Compliance (value-add):* give agent defs least-privilege `tools:` allowlists —
  *tightens* security posture.
- *Feasibility/maintenance:* update CLAUDE.md authoring doctrine (some roles in
  `agents/`, some still inline) or the mental model rots.
- *Mechanism confirmed:* plugins ship `agents/*.md`; dispatched by plugin-namespaced
  `subagent_type`; Repertoire's Tuner already dispatches `codex:codex-rescue`
  cross-plugin. `model: opus` is valid (R3 holds).

**Vetting card:** identity `fits` · compliance `none` (+least-priv tools value-add) ·
demand: real but maintainer-concentrated, high (cost-mechanics) / med (adoption) ·
feasibility `M` full / `S` MVP · **recommendation: ADVANCE** (the headline "agents"
answer; follow Shared Parts).

---

## Dropped at vetting
- **Tiered Ensemble** — *demand → no-gap, re-killed, user-confirmed drop.* Route roles
  to cheaper models for cost. Killed because the one big safe downshift (scouts →
  Haiku via the built-in `Explore` agent) **already ships**; the remaining
  downshiftable slice is ~10-20% of a run and the judgment seats must stay Opus, so
  the realistic saving is single-digit %. Under the user's **R3** constraint ("no
  performance drop, no Haiku") the re-judgment found no role safe to downshift (even
  the verifier requires diagnosed-reason judgment) and no Opus `effort` dial exposed,
  collapsing the idea to the Opus-everywhere status quo. The insight is preserved as
  design rule **R3**.

## Parked (banked)
- **First Chair** — folded into The Roster as its POC slice (code-diff reviewer first).
- **Section Parts** / **Guest Soloist** — folded into Shared Parts.
- **Revisit scouts-on-Haiku** — scouts currently use the built-in `Explore` agent
  (Haiku). If R3 ("no Haiku") is taken to its strict conclusion, whether to move
  scouts onto Opus/Sonnet is a separate, out-of-dimension change worth its own look.
- **`hooks/` for DX automation** — version-sync (plugin.json ↔ marketplace.json),
  README propagation across 4 languages, `claude plugin validate` gate. Out of this
  run's dimension (Cost & reuse), but the strongest candidate for a future
  "DX & release automation" Eureka run.
