# Context Scout Prompt (read-only)

Dispatch one **Explore** subagent after reading the spec to map the ground the
plan will sit on. The scout *reports facts*; it does not decompose the work or
propose tasks — that's your job, with the user at the structure checkpoint.
Reading the brief instead of the codebase keeps your own context lean for the
writing ahead.

Skip the scout only for a greenfield project with no existing code or docs to
fit into.

```
Agent tool (subagent_type: Explore):
  description: "Scout context for plan: [one-line topic]"
  prompt: |
    You are scouting an existing codebase so a planner can decompose an
    approved spec into implementation tasks. Report what's there — do NOT
    propose a task breakdown, write code, or redesign anything.

    ## What is being planned

    [one paragraph: what the spec commits to building, and the chosen approach]

    ## What the spec claims about this codebase

    [bullet the spec's concrete claims: files it names, interfaces it assumes,
    dependencies it relies on — so the scout can verify them]

    ## What to find

    - The files and modules each part of this work would most likely touch.
    - **Build and test commands** — the exact invocations (from the package
      manifest, CI config, README, or Makefile), including how to run a single
      test. These go into the plan header, so precision matters.
    - Testing conventions: framework, where tests live, how existing tests for
      similar code are structured and named.
    - Existing patterns a new implementation should follow (naming, error
      handling, module layout, how similar features are wired in).
    - Constraints the plan must respect — public interfaces, data schemas,
      dependencies, config.
    - **Spec drift:** check each claim listed above against the actual code.
      A file the spec names that doesn't exist, an interface with a different
      shape — flag it plainly; the planner must not build tasks on it.

    Search broadly; read excerpts, not whole files. If something is absent, say
    so plainly rather than guessing.

    ## Report format (compact — the planner stays lean)

    No code dumps, no task proposals. Keep it tight:

    - **Likely touch points:** `path` — one line on its role.
    - **Build/test commands:** exact invocations, incl. single-test form (or
      "none found — ask the user").
    - **Testing conventions:** framework, location, one `path` example.
    - **Patterns to follow:** bullet list, each with a `path` example.
    - **Constraints:** interfaces / schemas / deps the plan must respect.
    - **Spec drift:** each mismatch as `spec says X · code has Y` (or "none").
    - **Notes:** anything surprising or worth the planner's attention.
```
