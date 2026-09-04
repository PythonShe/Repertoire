# Repertoire

**English** | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md)

A collection of curated [Claude Code](https://code.claude.com/docs) skills, distributed as a single plugin. Install once to get all skills.

## Install

```shell
/plugin marketplace add PythonShe/Repertoire
/plugin install repertoire@repertoire
```

Then restart or run `/reload-plugins`. Skills are namespaced under `/repertoire:<skill>`.

## Recommended setup

These skills make extensive use of subagents and reasoning tokens. Recommended setup:

- **Claude Max (5x or 20x) or API billing**: Recommended. Lighter tiers may reach rate limits quickly.
- **OpenAI [`codex-cc` plugin](https://github.com/openai/codex-plugin-cc)** (optional): Enables cross-model review for supported skills. Falls back cleanly to Claude-only review if not installed.
- **GitHub [`gh-stack` extension](https://github.com/github/gh-stack)** (optional): Enables Maestro to link multi-plan sets into GitHub stacked PRs.
- **Model selection**: Run sessions on `/model best` or `opus` (**avoid Sonnet**). Build tasks inherit your session model, while review tasks use Opus.

## Skills

| Skill | Invoke | What it does |
| :---- | :----- | :----------- |
| **Eureka** | `/repertoire:eureka` | Explores and vets project ideas through structured brainstorming, producing a prioritized shortlist of viable concepts. |
| **Libretto** | `/repertoire:libretto` | Refines rough concepts into complete, implementation-ready technical specifications. |
| **Score** | `/repertoire:score` | Breaks down specifications into detailed implementation plans (including multi-plan sets for large projects). |
| **Maestro** | `/repertoire:maestro` | Executes implementation plans end-to-end, managing task implementation, code review, and quality verification. |
| **Coda** | `/repertoire:coda` | Triages and resolves open PR feedback, addressing review comments and fixing failing CI checks. |
| **Encore** | `/repertoire:encore` | Audits an existing codebase or module to discover and implement improvements in performance, security, and quality. |
| **Presto** | `/repertoire:presto` | Fast-track implementation for small, focused changes (≤150 lines) without requiring upfront specs or plans. |
| **Jam** | `/repertoire:jam` | Autonomous, unattended session that finds, implements, and verifies practical repository improvements. |
| **Legato** | `/repertoire:legato` | Audits and refines UI animations and transitions against professional motion standards. |
| **Tuner** | `/repertoire:tuner` | Diagnoses tricky bugs to their root cause and applies test-driven fixes. |

## Local development

Test changes directly from a clone:

```shell
claude --plugin-dir .
/reload-plugins
```

## Repository layout

```text
Repertoire/                       repo root = plugin root = marketplace root
├── .claude-plugin/
│   ├── plugin.json               plugin manifest
│   └── marketplace.json          marketplace catalog entry
├── skills/                       one directory per skill
│   └── <name>/                   eureka, libretto, score, maestro, coda, encore, tuner, presto, jam, legato
├── shared/                       shared subagent contracts and policies
├── docs/                         architecture decision records and authoring guides
├── CHANGELOG.md
└── README.md
```

## Adding a new skill

1. Create `skills/<name>/SKILL.md` (with `name`, `description`, and `when_to_use` in YAML frontmatter). Include trigger evals at `skills/<name>/evals/evals.json`.
2. Add a row to the **Skills** table above.
3. Validate and commit:
   ```shell
   claude plugin validate .
   ```

To release updates to existing installs, increment `version` in **both** `.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`.
