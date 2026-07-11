# Repertoire

**English** | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md)

A growing **repertoire** of self-authored [Claude Code](https://code.claude.com/docs)
skills, distributed as a single plugin. Install once, get every skill — and more
as the collection grows.

## Install

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

Then restart or run `/reload-plugins`. Skills are namespaced under the plugin,
e.g. `/repertoire:maestro`.

## Recommended setup

These skills are deliberately token-heavy: every run conducts an ensemble of
fresh subagents at medium-to-xhigh effort — build seats (implementers, fixers, QC gates)
on your session model, review seats (investigators, review panels, verifiers)
pinned to Opus — and that is where their reliability comes from. Plan accounts
accordingly:

- **Claude Max (5x or 20x), or API billing** — expected. Lighter plans are
  likely to hit usage limits mid-run.
- **A Codex account with OpenAI's
  [`codex-cc` plugin](https://github.com/openai/codex-plugin-cc) installed** —
  strongly suggested. Every skill dispatches a cross-model Codex agent
  (reviewer or investigator) through it. Skills degrade gracefully without Codex — they run
  the Opus-only panel and say so in their report — but the cross-model check
  is part of the design.

> **Model note:** These skills run on two tiers. The **build seats** —
> implementers, fixers, and the QC/final gates — are *unpinned*: they inherit
> your session model, so start the session on the most capable model you have.
> `/model best` resolves to **Fable 5** where you have access, otherwise the
> latest **Opus** — Fable's speed on the build seats, an automatic Opus fallback,
> and no hardcoded model name to break if Fable is ever suspended again. The
> **review seats** — investigators, review panels, and verifiers — stay pinned
> to **Opus** for a stable adversarial baseline. Run these skills on `best` or
> `opus` — **never Sonnet**.

## Skills

| Skill | Invoke | What it does |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | Hunts ideas before anything is built: establishes a talking range (an existing codebase, an open domain, or the neighborhood of a half-formed spark) and a focus dimension, then finds candidates through a paced dialogue — seed sketches, a running idea board, and on-demand bursts of 3-4 diverse-lens ideators — converges finalists, and drives them through a sequential vetting funnel (identity → compliance → demand → feasibility) where every kill verdict needs the user's confirmation before an idea dies. Ends at a ranked, vetted shortlist with an optional champion handed to Libretto — never a spec, never code. Auto-invokes on matching requests. |
| **Libretto** | `/repertoire:libretto` | Turns a rough idea into a build-ready spec through a guided design dialogue, then hardens it with an adversarial subagent review panel (2-3 diverse-lens Opus skeptics + a cross-model Codex reviewer) before a final user-approval gate. Delegates only context-gathering and review; ends at an approved spec and points to Maestro to build it — never auto-chains. Auto-invokes on matching requests. |
| **Score** | `/repertoire:score` | Turns an approved spec into a decision-complete implementation plan — movements of Maestro-ready task groups with exact paths, interfaces, and test expectations, but no function bodies — then hardens it with the same adversarial review panel before a final user-approval gate. When one goal needs several plans it scales to a plan set: a `00-overview.md` carrying the cross-plan contracts, authored by the controller, plus one plan file per part written by parallel plan-writer subagents (a single plan gets no overview). One structural checkpoint with the user; ends at an approved plan and points to Maestro to conduct it — never auto-chains. Auto-invokes on matching requests. |
| **Maestro** | `/repertoire:maestro` | Conducts subagent-driven execution of an implementation plan: groups related tasks, builds each group with a fresh implementer, then gates the whole branch behind an adversarial review panel (3 diverse-lens Opus skeptics + a cross-model Codex reviewer) and an evidence-based quality-control merge gate — while the conductor keeps its own context lean. Auto-invokes on matching requests; when not named explicitly, it confirms scope and cost before dispatching. |
| **Coda** | `/repertoire:coda` | Works an open PR's review feedback to a close: a clerk harvests every review, inline thread, conversation comment, and failing CI check; one read-only verifier tests each item against codebase reality (fix, push back, or ask — evidence decides); sequential fixers repair what survives; big or risky fixes face a 3-lens Opus panel, and every run is sealed by a staged final verdict — an evidence-based QC that reads each fix in full, then one cross-model Codex pass over the whole PR once QC clears; one approval gate covers pushing and posting the drafted thread replies. Never merges, never resolves threads. Auto-invokes on matching requests; when not named explicitly, it confirms scope and cost before dispatching. |
| **Encore** | `/repertoire:encore` | Revisits a finished codebase, feature, or module and enhances what already works: a scout profiles the target and proposes a lens roster (performance, security, robustness, DX, …), parallel diverse-lens Opus hunters call out opportunities, and one read-only verifier tests every call against codebase reality — refuted calls die on the record, feature-sized ones route to Libretto or Score, never built here. Only what the user picks at the set-list gate gets implemented, on a fresh `encore/` branch by sequential fixers; big or risky changes face a 3-lens Opus panel, and every run is sealed by the staged final verdict — evidence-based QC, then one cross-model Codex pass. Pushes and offers a PR behind one approval gate, never merges. Auto-invokes on matching requests. |
| **Tuner** | `/repertoire:tuner` | Hunts a bug to its root cause with two rival investigators — a Codex agent at xhigh reasoning effort dispatched the moment the bug brief exists, and a systematic Opus investigator (also at xhigh effort) primed by a triage scout's ranked fault surfaces — then cross-examines the two hypotheses as the confidence gate. The repair is test-first: a fixer commits a deliberately-red repro test before the minimal fix, a skeptical reviewer gates the fix, and a mechanical verifier proves red→green plus a green suite. Commits on a feature branch, never merges. Auto-invokes on matching requests; when not named explicitly, it confirms scope and cost before dispatching. |

## Local development

Test changes without installing, straight from a clone:

```shell
claude --plugin-dir .
/reload-plugins        # after edits, no restart needed
claude plugin validate .
```

## Repository layout

```text
Repertoire/                       repo root = plugin root = marketplace root
├── .claude-plugin/
│   ├── plugin.json               plugin manifest (name: repertoire)
│   └── marketplace.json          catalog listing this plugin (source "./")
├── skills/                       one directory per skill
│   └── <name>/                   eureka, libretto, score, maestro, coda, encore, tuner
│       ├── SKILL.md
│       ├── evals/evals.json      committed trigger evals
│       ├── *-template.md         bundled document structure (libretto, score)
│       └── *-prompt.md           bundled subagent prompt templates
├── shared/
│   ├── codex-reviewer-core.md    shared Codex invocation contract
│   └── invariants.md             canonical two-tier model policy
├── docs/
│   ├── adr/                      architecture decision records
│   └── authoring/                skill-authoring doctrine
├── CHANGELOG.md
└── README.md
```

## Adding a new skill

1. Create `skills/<name>/SKILL.md` (with YAML frontmatter: `name`,
   `description`, `when_to_use` — doctrine in `docs/authoring/skill-descriptions.md`).
   Bundle any supporting prompt/reference files alongside it, plus trigger
   evals at `skills/<name>/evals/evals.json`.
2. Add a row to the **Skills** table above.
3. `claude plugin validate .` then commit. New skills are discovered
   automatically — the whole repo is one plugin, so the catalog never needs a
   new entry.

Bump `version` — same value in **both** `.claude-plugin/plugin.json` and the
plugin entry in `.claude-plugin/marketplace.json` — when you want existing
installs to pick the change up (they see it after `/plugin marketplace update`).
