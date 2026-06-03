# Context Scout Prompt (read-only)

Dispatch one **Explore** subagent at the start of a design pass to map the ground
the spec will sit on. The scout *reports facts*; it does not propose a design —
that's your job, with the user. Reading the brief instead of the codebase keeps
your own context lean for the long dialogue ahead.

Skip the scout only for a greenfield idea with no existing code or docs to fit
into.

```
Agent tool (subagent_type: Explore):
  description: "Scout context for: [one-line topic]"
  prompt: |
    You are scouting an existing codebase so a designer can write a spec that
    fits it. Report what's there — do NOT propose a design, write code, or
    recommend an approach.

    ## The idea being designed

    [one paragraph: what the user wants to build or change]

    ## What to find

    - The files, modules, and entry points this work would most likely touch.
    - Existing patterns and conventions a new design should follow (naming, error
      handling, testing style, how similar features are structured here).
    - Relevant prior art: anything in the codebase that already does something
      similar, and where.
    - Constraints the design must respect — public interfaces, data schemas,
      dependencies, config.
    - Any existing docs or specs on this area (note their paths).

    Search broadly; read excerpts, not whole files. If something is absent, say
    so plainly rather than guessing.

    ## Report format (compact — the designer stays lean)

    No code dumps, no design proposals. Keep it tight:

    - **Likely touch points:** `path` — one line on its role.
    - **Conventions to follow:** bullet list, each with a `path` example.
    - **Prior art:** `path` — what it does that's relevant.
    - **Constraints:** interfaces / schemas / deps the design must respect.
    - **Existing docs:** `path` — what it covers (or "none found").
    - **Notes:** anything surprising or worth the designer's attention.
```
