# Changelog

## v2.5.1 — 2026-09-04
- Fixed an issue where the Codex fallback CLI (`codex exec`) could hang indefinitely when stdin is left open ([openai/codex#20919](https://github.com/openai/codex/issues/20919)).

## v2.5.0 — 2026-08-03
- Added optional GitHub stacked pull request support (`gh-stack`) for multi-plan sets in Score and Maestro.

## v2.4.1 — 2026-08-01
- Added `legato` skill for UI animation and motion design.
- Added persistent backlog tracking (`docs/repertoire/jam-backlog.md`) for Jam.

## v2.3.0 — 2026-07-31
- Added `jam` skill for autonomous, unattended repository improvements.

## v2.2.0 — 2026-07-25
- Added `presto` skill for rapid implementation of small, scoped changes.

## v2.1.0 — 2026-07-11
- Added multi-plan set support to `score` for larger projects.

## v2.0.0 — 2026-07-10
- Made all skills auto-invocable with optimized trigger evaluations.
- Added confirmation prompts before launching token-heavy pipelines.

## v1.7.0 — 2026-07-01
- Adopted two-tier model policy: build roles inherit the session model, while review roles use Opus.

## v1.6.2 — 2026-06-16
- Pinned subagents to Opus during temporary Claude Fable restrictions.

## v1.6.1 — 2026-06-09
- Subagent model tuning and shared review contract consolidation.

## v1.6.0 — 2026-06-07
- Added `encore` skill for codebase enhancement passes.

## v1.5.0 — 2026-06-07
- Added `eureka` skill for idea exploration and brainstorming.
- Added multilingual documentation (zh-CN, zh-TW, ja).

## v1.4.0 — 2026-06-06
- Added `tuner` skill for root-cause bug debugging.

## v1.3.0 — 2026-06-06
- Added `coda` skill for handling PR review comments and CI failures.

## v1.2.0 — 2026-06-06
- Added `score` skill for creating implementation plans.

## v1.1.0 — 2026-06-03
- Added `libretto` skill for creating technical specifications.

## v1.0.2 — 2026-06-03
- Plugin marketplace metadata and version sync fixes.

## v1.0.1 — 2026-06-03
- Documentation and license updates.

## v1.0.0 — 2026-06-03
- Initial release with the `maestro` skill for plan execution.
