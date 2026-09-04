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

These skills are designed for deep reasoning and orchestrate specialized
subagents running at medium-to-xhigh effort — build roles (implementers,
fixers, QC gates) on your session model, and review roles (investigators,
review panels, verifiers) pinned to Opus. Recommended setup:

- **Claude Max (5x or 20x) or API billing** — recommended. Lighter plans may
  reach usage limits during intensive runs.
- **A Codex account with OpenAI's [`codex-cc` plugin](https://github.com/openai/codex-plugin-cc) installed** —
  recommended. Most skills dispatch an independent cross-model Codex agent
  (reviewer or investigator) for adversarial verification. If Codex is
  unavailable, skills gracefully fall back to Opus-only review panels.
  (Presto and Legato omit Codex review to keep latency low on focused, scoped
  changes; Jam omits it to ensure unattended runs never stall on external calls,
  pairing Opus with the session model instead.) If the plugin's companion script
  is missing, fallback calls to the `codex` CLI include stdin redirection
  (`< /dev/null`), file-based I/O, and execution timeouts to prevent hangs
  ([openai/codex#20919](https://github.com/openai/codex/issues/20919); see
  `shared/codex-reviewer-core.md`).
- **The `gh` CLI with GitHub's [`gh-stack` extension](https://github.com/github/gh-stack)** —
  optional. Enables Maestro to link multi-plan `stacked chain` sets into
  GitHub stacked pull requests (public preview). Without it, dependent plans
  are pushed as standard branches targeting the parent plan.

> **Model note:** These skills use a two-tier model policy. The **build roles** —
> implementers, fixers, and QC gates — are unpinned and inherit your session model.
> Start the session with the most capable model available: `/model best`
> resolves to **Fable 5** where available, otherwise the latest **Opus**.
> The **review roles** — investigators, review panels, and verifiers — stay
> pinned to **Opus** for a consistent adversarial baseline. (The one deliberate
> exception: Jam's second finale reviewer runs unpinned on the session model,
> as documented in `shared/invariants.md`.) Run these skills on `best` or
> `opus` — **never Sonnet**.

## Skills

| Skill | Invoke | What it does |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | Discovers and evaluates ideas before implementation. Establishes a search domain and focus area, then explores candidates through guided dialogue, idea boards, and multi-perspective ideation subagents. Finalists pass through a sequential validation funnel (identity alignment, compliance, demand, feasibility) where candidate elimination requires user confirmation. Produces a ranked, vetted shortlist for Libretto without generating specs or code. Auto-invokes on matching requests. |
| **Libretto** | `/repertoire:libretto` | Transforms a concept into a production-ready specification through a guided design dialogue, hardened by an adversarial subagent review panel (2–3 diverse-perspective Opus reviewers + a cross-model Codex reviewer) before a final user-approval gate. Delegates context gathering and review to subagents while keeping orchestrator context lean. Concludes with an approved specification ready for Score or Maestro. Auto-invokes on matching requests. |
| **Score** | `/repertoire:score` | Converts an approved specification into a decision-complete implementation plan with exact file paths, interfaces, and test expectations, validated by an adversarial review panel before a user-approval gate. For larger initiatives, Score scales to a multi-plan set: an orchestrator-authored `00-overview.md` defining cross-plan contracts, alongside individual plan files authored by parallel subagents. The overview declares the execution shape (`parallel` branches, or a `stacked chain` linked via GitHub stacked pull requests). Concludes with an approved plan ready for Maestro. Auto-invokes on matching requests. |
| **Maestro** | `/repertoire:maestro` | Orchestrates plan execution using specialized subagents: groups related tasks, executes each group with a fresh implementer, and validates the branch with an adversarial review panel (3 diverse-perspective Opus reviewers + a cross-model Codex reviewer) and an evidence-based QC merge gate. For `stacked chain` plan sets, Maestro executes one plan per run and links dependent branches into GitHub stacked pull requests (`gh stack link`), with merges strictly managed by the user. Auto-invokes on matching requests, confirming scope and cost before starting. |
| **Coda** | `/repertoire:coda` | Resolves pull request review feedback and CI failures to completion. Gathers all reviews, inline threads, discussion comments, and failing checks; verifies each item against the codebase with a read-only analyzer (to fix, push back, or clarify); and applies fixes sequentially. High-risk fixes face a 3-perspective Opus panel, followed by build/test QC and a cross-model Codex review. Pushing changes and posting reply comments are confirmed in a single user approval gate. Never merges PRs or resolves threads autonomously. Auto-invokes on matching requests, confirming scope and cost before starting. |
| **Encore** | `/repertoire:encore` | Audits a completed codebase, feature, or module to uncover enhancement opportunities across performance, security, robustness, and developer experience. Specialized Opus subagents identify improvements, which are verified against codebase reality by a read-only analyzer. Only user-selected items from an interactive selection gate are implemented on a dedicated `encore/` branch, sealed by build/test QC and cross-model Codex verification. Submits a pull request upon user approval; never merges directly. Auto-invokes on matching requests. |
| **Presto** | `/repertoire:presto` | A streamlined workflow for small, well-scoped changes completed in a single session without requiring upfront specifications or multi-stage plans. Opus scouts inspect the target code and propose implementation options for user approval before changes begin. Small tasks (≤150 lines, ≤3 files) are implemented directly by the controller to minimize latency, while larger tasks are delegated to an implementer subagent. Concludes with an independent Opus review, a repair pass, and build/test QC. If scope expands or QC fails repeatedly, tasks smoothly escalate to Libretto, Score, or Maestro. Operates without cross-model Codex review to optimize speed for localized edits. Auto-invokes on matching requests, confirming the brief and branch before scouting. |
| **Jam** | `/repertoire:jam` | An autonomous, unattended development session that independently discovers, implements, and verifies repository improvements. Three parallel Opus scouts analyze the repository from an end-user perspective (non-breaking features, bug fixes, UI/UX polish); an Opus selector curates a balanced 3–5 task docket for the session. Subagents build and review tasks sequentially, cleanly reverting unviable changes rather than stalling. Concludes with parallel whole-branch reviews (pairing an Opus reviewer with the session model) and build/test QC verification. Unselected candidates and deferred tasks are recorded in `docs/repertoire/jam-backlog.md` for subsequent runs. Pushes only upon request; never merges directly. Auto-invokes on matching requests, confirming scope and cost before starting. |
| **Legato** | `/repertoire:legato` | A dedicated motion design skill for UI animation polish on user-specified surfaces. An Opus scout audits target components against Emil Kowalski's motion standards (bundled under MIT license in `references/`), proposing precise timing and easing values. The user confirms direction at an interactive checkpoint, followed by subagent implementation, an Opus review against core animation principles, and build/test QC verification (with optional browser inspection for running apps). Saves durable motion guidance to `docs/repertoire/animation.md` for future sessions. Application-wide redesigns route to Libretto, Score, or Maestro. Auto-invokes on matching requests, confirming the brief and branch before scouting. |
| **Tuner** | `/repertoire:tuner` | Investigates bugs to their root cause using dual investigators — a Codex agent and an Opus investigator guided by triage fault analysis — cross-evaluating their hypotheses before applying fixes. Follows a test-driven repair process: commits a failing reproduction test before implementing minimal fixes, followed by independent review and red-to-green verification. Commits to a feature branch; never merges directly. Auto-invokes on matching requests, confirming scope and cost before starting. |

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
│   └── <name>/                   eureka, libretto, score, maestro, coda, encore, tuner, presto, jam, legato
│       ├── SKILL.md
│       ├── evals/evals.json      committed trigger evals
│       ├── *-template.md         bundled document structure (libretto, score, legato)
│       ├── *-prompt.md           bundled subagent prompt templates
│       └── references/           bundled third-party reference material (legato; MIT-licensed, see its LICENSE)
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
3. Run `claude plugin validate .` then commit. New skills are discovered
   automatically — the whole repo is one plugin, so the catalog never needs a
   new entry.

Bump `version` — same value in **both** `.claude-plugin/plugin.json` and the
plugin entry in `.claude-plugin/marketplace.json` — when you want existing
installs to pick the change up (they see it after `/plugin marketplace update`).
