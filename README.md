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
| **Maestro** | `/repertoire:maestro` | Conducts subagent-driven execution of an implementation plan: groups related tasks, builds each group with a fresh implementer, then gates the whole branch behind an adversarial review panel (3 diverse-lens Opus skeptics + a cross-model Codex reviewer) and an evidence-based quality-control merge gate — while the conductor keeps its own context lean. Manual-only; invoke it by name. |

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
│   └── maestro/
│       ├── SKILL.md
│       └── *-prompt.md           bundled subagent prompt templates
└── README.md
```

## Adding a new skill

1. Create `skills/<name>/SKILL.md` (with YAML frontmatter: `name`, `description`).
   Bundle any supporting prompt/reference files alongside it.
2. Add a row to the **Skills** table above.
3. `claude plugin validate .` then commit. Existing installs pick it up on the
   next `/plugin update` — no marketplace change needed, since the whole repo is
   one plugin.

Bump `version` in `.claude-plugin/plugin.json` when you want installs to update.
