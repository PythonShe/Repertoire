# Target Scout Prompt (read-only)

Dispatch one **Explore** subagent at the start of the run to profile the
target and propose the lens roster. Unlike a Eureka scout, this one *does*
propose — but only **lenses**, never enhancements: deciding what the code
needs changed is the hunters' and verifier's job, and pre-empting them here
would anchor the whole hunt on one agent's first impression. Reading the
brief instead of the codebase keeps your own context lean for the long run
ahead.

```
Agent tool (subagent_type: Explore):
  description: "Scout encore target: [one-line target]"
  prompt: |
    You are profiling a finished piece of code so an enhancement pass can
    choose which quality lenses to hunt it with. Report what IS and propose
    LENSES — do NOT propose specific enhancements, fixes, or refactors.

    ## The target

    [one paragraph: what the user named — paths, feature, or whole repo —
    plus the SINCE ref if the scope is "what shipped since X", and the
    user's stated ambition, if any]

    Scope paths: [SCOPE]
    Work from: [directory]

    ## What to find

    - **Profile:** what this target is, its stack and languages, the
      surfaces it exposes (CLI, API, UI, library), and rough size.
    - **Build & test:** the exact build and test commands, from manifests,
      CI config, or docs — quote them precisely, or say "not found".
    - **Heat:** churn hotspots (`git log --oneline --since` / path
      frequency), TODO/FIXME density, areas issues or docs complain about —
      facts with paths, not judgments.
    - **Test landscape:** where tests live, what looks covered, what
      plainly isn't.
    - **Constraints:** platform commitments, supported versions, public
      contracts, anything an enhancement must not break.
    - **Prior encores:** dossiers under `docs/repertoire/encores/` — for
      each, the path plus its refuted calls (with one-line reasons),
      declined calls, and routed calls. These become this run's exclusion
      list (or report "none").
    - **Lens proposal:** from the menu — performance, security, robustness,
      maintainability, dx-ergonomics, test-coverage, observability,
      accessibility, dependency-health, docs-accuracy (or a coined lens the
      target plainly demands) — propose the 3-4 that fit this target best,
      RANKED, each with a one-line evidence-backed rationale ("hot loop in
      parser + zero benchmarks → performance"), plus up to 2 runner-ups.
      A lens that cannot apply (accessibility on a headless library) must
      not appear.

    Search broadly; read excerpts, not whole files. If something is absent,
    say so plainly rather than guessing.

    ## Report format (compact — the conductor stays lean)

    No code dumps, no enhancement ideas. Keep it tight:

    - **Profile:** 2-3 sentences.
    - **Build:** `command` (or "not found")  **Test:** `command` (or "not
      found")
    - **Heat:** bullets, each with a `path`.
    - **Test landscape:** bullets.
    - **Constraints:** bullets.
    - **Prior encores:** `path` — refuted/declined/routed one-liners (or
      "none found").
    - **Lens proposal:** ranked list, one rationale line each; then
      runner-ups.
    - **Notes:** anything surprising or worth the conductor's attention.
```
