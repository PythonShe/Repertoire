# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Repertoire is a single Claude Code **plugin** that bundles self-authored skills and ships through its own one-entry **marketplace**. The repo root is simultaneously the marketplace root, the plugin root, and the source tree: `.claude-plugin/marketplace.json` lists exactly one plugin whose `source` is `"./"`, and `.claude-plugin/plugin.json` is that plugin's manifest. There is no build step and no compiled artifact — skills are Markdown, and installing the plugin ships the repo as-is.

One topology fact drives everything else: **adding a skill never touches the marketplace.** A new `skills/<name>/SKILL.md` is discovered automatically because the entire repo is one plugin. What *does* live in `marketplace.json` is the plugin's **catalog entry** — the display surface the `/plugin` browse/manage list reads (description, version, author, homepage, category, tags), mirrored from `plugin.json`.

The lever that makes existing installs update is the plugin `version`, and it lives in **two** places that must be bumped together: `version` in `.claude-plugin/plugin.json` drives resolution and caching (it wins silently), while `version` in the `marketplace.json` entry drives the number the `/plugin` UI shows. Keep them identical — if they drift, the UI shows one version while installs resolve another. Bumping either reaches users only after they run `/plugin marketplace update`.

## Commands

No test/lint/build toolchain exists. Validation and iteration happen through the Claude CLI:

```bash
claude plugin validate .     # validate plugin + marketplace manifests and skill frontmatter
claude --plugin-dir .        # run Claude with this repo loaded as a local plugin (no install)
/reload-plugins              # in-session, after editing a skill — picks up changes without a restart
```

Once installed, skills are namespaced by plugin name: invoke Maestro as `/repertoire:maestro`.

## Anatomy of a skill

A skill is a directory `skills/<name>/` containing `SKILL.md` (YAML frontmatter: `name`, `description`) plus any bundled prompt or reference files beside it.

The `description` is the **routing surface** — Claude reads it to decide whether to auto-invoke the skill, so it must be concrete, triggerable, and explicit about its trigger policy. Maestro's description is marked `MANUAL-ONLY` and tells Claude *not* to fire on generic "execute this plan" requests; that negative guidance is what keeps a powerful, expensive skill from auto-triggering. When you change what a skill does, update its `description` in the same edit — the description and the behavior are one contract.

Bundled `*-prompt.md` files are not loaded into the active context wholesale. The skill body names them, and they are pasted into freshly dispatched subagents at runtime — keeping the controlling context lean while each subagent gets a complete, purpose-built brief.

## Maestro: the subagent-driven pattern

Maestro (`skills/maestro/`) is the one substantive skill and the template for future ones. Understanding it requires reading `SKILL.md` together with its five role prompts — they form one system:

- **The conductor never plays.** The skill controller dispatches subagents and never reads source files or diffs itself; only compact reports and verdicts return to it, so its context survives a long plan. Reading the *plan* and running read-only git metadata (`merge-base`, `rev-parse`, `log`, `status`) is "bookkeeping" and allowed; reading *code* is "playing" and is delegated.
- **Three-phase pipeline.** Phase 1 builds each task group with a fresh implementer, one broad review, and one fix. Phase 2 runs a whole-branch adversarial panel — 3 diverse-lens Opus skeptics plus 1 cross-model Codex reviewer. Phase 3 is an evidence-based QC merge gate that **pushes but never merges**, escalating via `AskUserQuestion` after 3 `NOT_MERGEABLE` rounds.
- **One prompt per role.** `implementer-prompt.md`, `reviewer-prompt.md` (parameterized by lens — broad for Phase 1, one lens each for the panel), `codex-reviewer-prompt.md`, `fixer-prompt.md`, and `qc-prompt.md` are the subagent contexts. A phase described in `SKILL.md` is realized by its prompt file, so the two must stay consistent when you change behavior.

## Authoring conventions

To add a skill (keep this in sync with the README's process section):

1. Create `skills/<name>/SKILL.md` with `name` + `description` frontmatter; bundle any prompt/reference files alongside it.
2. Add a row to the **Skills** table in `README.md`.
3. `claude plugin validate .`, then commit.
4. Bump `version` in **both** `.claude-plugin/plugin.json` and the `marketplace.json` plugin entry — same value — when you want existing installs to pick it up. If you changed the plugin's user-facing `description`, edit it in both places too: the `marketplace.json` entry is what the `/plugin` UI displays.

Keep the skill name consistent across `plugin.json` keywords, `SKILL.md` frontmatter, the README table, and the invocation path.

**Commit convention.** Use `{type}: {description}`, where `type` is one of `feat`, `fix`, `refactor`, `docs`, or `chore`. Keep the subject imperative and scoped to one concern; split unrelated changes (a new skill vs. docs vs. a `version` bump) into separate commits.

When deciding where new behavior belongs, choose the layer deliberately: work that needs judgment, adaptation, or follow-up questions is a **skill**; deterministic, same-input-same-output checks or lookups belong in a bundled script or reference file, not a new skill.

## Local-only workspaces

`*-workspace/` directories sit beside a skill and are gitignored — never commit them. They hold skill-creator eval/optimizer scratch: for example `maestro-workspace/trigger-eval.json` is a set of `should_trigger` cases used to verify that Maestro's `description` routes correctly (fires when Maestro is named, stays silent on generic plan-execution requests). Treat these as disposable validation output.
