# Repertoire

A single Claude Code plugin of self-authored skills, shipped through its own one-entry marketplace. The repo root is the marketplace root, the plugin root and the source tree: `.claude-plugin/marketplace.json` lists one plugin with `source: "./"`, and `.claude-plugin/plugin.json` is its manifest. No build step: skills are Markdown and installing ships the repo as-is.

## Plugin topology

- Adding a skill never touches the marketplace: `skills/<name>/SKILL.md` is discovered automatically. `marketplace.json` holds only the catalog entry the `/plugin` UI reads (description, version, author, homepage, category, tags), mirrored from `plugin.json`.
- `version` lives in both `plugin.json` (drives resolution and caching, wins silently) and the `marketplace.json` entry (what the UI shows). Bump both to the same value; the same goes for the user-facing `description`. Users get it after `/plugin marketplace update`.
- Installed skills are namespaced: `/repertoire:maestro`.

## Commands

No test, lint or build toolchain.

```bash
claude plugin validate .     # marketplace manifest only; does not parse skill frontmatter
ruby -ryaml -e 'Dir.glob("skills/*/SKILL.md").each { |f| YAML.safe_load(File.read(f).split("---")[1]) }' && echo OK   # frontmatter YAML (strict parsers reject bare ': ' in scalars)
claude --plugin-dir .        # run with this repo as a local plugin
/reload-plugins              # in-session, after editing a skill
```

## Skill anatomy

- `skills/<name>/SKILL.md` with frontmatter `name`, `description`, `when_to_use`; bundled prompt and reference files beside it; trigger evals committed at `skills/<name>/evals/evals.json`.
- `description` + `when_to_use` is the routing surface: every skill auto-invokes. `description` says what the skill does to what object; `when_to_use` carries positive triggers plus one adjacency clause per confusable neighbor, never negative trigger lists. Keep the pair under ~1,200 characters (the listing truncates at 1,536 silently). Doctrine and eval loop: `docs/authoring/skill-descriptions.md`.
- Behavior and routing surface are one contract: when a skill's behavior changes, update its routing surface in the same edit and re-run its trigger evals.
- Skills that dispatch subagents before any user gate (Maestro, Coda, Tuner, Presto, Jam, Legato) carry a cost gate: confirm scope via AskUserQuestion when not invoked by name.
- Bundled `*-prompt.md` files are pasted into freshly dispatched subagents at runtime, not loaded into the controller's context. A phase described in `SKILL.md` is realized by its prompt file; keep the two consistent.
- Judgment, adaptation or follow-up questions belong in a skill; deterministic checks or lookups belong in a bundled script or reference file.

## Design doctrine

- **The conductor never plays** (Maestro is the template). The controller dispatches subagents and never reads source or diffs itself; only compact reports return. Reading the plan and read-only git metadata is bookkeeping and allowed.
- **Presto is the scoped exception** ([ADR 0005](docs/adr/0005-the-controller-plays-in-presto.md)): its controller implements directly inside a size gate (<=~3 files, <=~150 lines, one module, no new subsystem, dependency, schema or public interface). Two guards must survive any edit: the reviewer is always a fresh subagent, and outgrowing the gate mid-build is a stop-and-hand-off.
- **Jam runs unattended** ([ADR 0006](docs/adr/0006-jam-runs-unattended.md)): scouts and an Opus selector stand in for the user; the docket is announced, not approved.
- Model-policy variances (which seats pin Opus, which skills have no Codex seat) and the required shape of every bare `codex exec` fallback live in `shared/invariants.md`; each SKILL.md carries a marked copy that must not drift from it.
- Maestro plan sets may declare a `stacked chain` execution shape that lands as GitHub stacked PRs ([ADR 0008](docs/adr/0008-stacked-chains-land-as-stacked-prs.md)).
- `skills/legato/references/` holds MIT-licensed copies from Emil Kowalski's skills repo ([ADR 0007](docs/adr/0007-legato-borrows-its-taste-and-keeps-a-record.md)). Keep `references/LICENSE`; never edit the copies except to re-sync upstream.
- Jam and Legato write standing records into the target project (`docs/repertoire/jam-backlog.md`, `docs/repertoire/animation.md`), superseded in place, never a changelog.

## Adding a skill

1. Create `skills/<name>/` as above. Keep the name consistent across `plugin.json` keywords, `SKILL.md` frontmatter, the README tables and the invocation path.
2. Add a row to the Skills table in `README.md` and in `README.ja.md`, `README.zh-CN.md`, `README.zh-TW.md`.
3. `claude plugin validate .`, re-run trigger evals if the routing surface changed, commit.
4. To release, bump `version` in both manifests and record the release in `CHANGELOG.md` with a short paragraph on why.

## Conventions

- Commit subjects have no scope: `type: description`, type one of `feat`, `fix`, `refactor`, `docs`, `chore`. A new skill, docs and a version bump are separate commits.
- `*-workspace/` directories beside skills are gitignored skill-creator scratch; never commit them. Trigger evals under `skills/<name>/evals/` are source.
