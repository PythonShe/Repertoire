# Repertoire

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

## Skills

| Skill | Invoke | What it does |
| :---- | :----- | :----------- |
| **Libretto** | `/repertoire:libretto` | Turns a rough idea into a build-ready spec through a guided design dialogue, then hardens it with an adversarial subagent review panel (2-3 diverse-lens Opus skeptics + a cross-model Codex reviewer) before a final user-approval gate. Delegates only context-gathering and review; ends at an approved spec and points to Maestro to build it — never auto-chains. Manual-only; invoke it by name. |
| **Score** | `/repertoire:score` | Turns an approved spec into a decision-complete implementation plan — movements of Maestro-ready task groups with exact paths, interfaces, and test expectations, but no function bodies — then hardens it with the same adversarial review panel before a final user-approval gate. One structural checkpoint with the user; ends at an approved plan and points to Maestro to conduct it — never auto-chains. Manual-only; invoke it by name. |
| **Maestro** | `/repertoire:maestro` | Conducts subagent-driven execution of an implementation plan: groups related tasks, builds each group with a fresh implementer, then gates the whole branch behind an adversarial review panel (3 diverse-lens Opus skeptics + a cross-model Codex reviewer) and an evidence-based quality-control merge gate — while the conductor keeps its own context lean. Manual-only; invoke it by name. |
| **Coda** | `/repertoire:coda` | Works an open PR's review feedback to a close: a clerk harvests every review, inline thread, conversation comment, and failing CI check; one read-only verifier tests each item against codebase reality (fix, push back, or ask — evidence decides); sequential fixers repair what survives; big or risky fixes face a 3-lens Opus panel, and every run is sealed by a staged final verdict — an evidence-based QC that reads each fix in full, then one cross-model Codex pass over the whole PR once QC clears; one approval gate covers pushing and posting the drafted thread replies. Never merges, never resolves threads. Manual-only; invoke it by name. |
| **Tuner** | `/repertoire:tuner` | Hunts a bug to its root cause with two rival investigators — a Codex agent at xhigh reasoning effort dispatched the moment the bug brief exists, and a systematic Opus investigator primed by a triage scout's ranked fault surfaces — then cross-examines the two hypotheses as the confidence gate. The repair is test-first: a fixer commits a deliberately-red repro test before the minimal fix, a skeptical reviewer gates the fix, and a mechanical verifier proves red→green plus a green suite. Commits on a feature branch, never merges. Manual-only; invoke it by name. |

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
├── skills/
│   ├── libretto/
│   │   ├── SKILL.md
│   │   ├── spec-template.md      bundled spec structure
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── score/
│   │   ├── SKILL.md
│   │   ├── plan-template.md      bundled plan structure
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── maestro/
│   │   ├── SKILL.md
│   │   └── *-prompt.md           bundled subagent prompt templates
│   ├── coda/
│   │   ├── SKILL.md
│   │   └── *-prompt.md           bundled subagent prompt templates
│   └── tuner/
│       ├── SKILL.md
│       └── *-prompt.md           bundled subagent prompt templates
└── README.md
```

## Adding a new skill

1. Create `skills/<name>/SKILL.md` (with YAML frontmatter: `name`, `description`).
   Bundle any supporting prompt/reference files alongside it.
2. Add a row to the **Skills** table above.
3. `claude plugin validate .` then commit. New skills are discovered
   automatically — the whole repo is one plugin, so the catalog never needs a
   new entry.

Bump `version` — same value in **both** `.claude-plugin/plugin.json` and the
plugin entry in `.claude-plugin/marketplace.json` — when you want existing
installs to pick the change up (they see it after `/plugin marketplace update`).
