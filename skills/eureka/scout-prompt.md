# Terrain Scout Prompt (read-only)

Dispatch one **Explore** subagent at the start of an **anchored** run to map the
ground the idea hunt will cover. The scout *reports what is* — it never proposes
features or ranks gaps; finding ideas is the conversation's job, with the user.
Reading the brief instead of the codebase keeps your own context lean for the
long dialogue ahead.

Skip the scout for open-field and orbit ranges with no codebase to read — elicit
the identity brief from the user instead.

```
Agent tool (subagent_type: Explore):
  description: "Scout terrain for idea hunt: [one-line range]"
  prompt: |
    You are scouting an existing project so an idea-discovery dialogue can hunt
    for features worth building. Report what IS — do NOT propose features,
    rank opportunities, or recommend anything.

    ## The hunt

    [one paragraph: the range, and the focus dimension if already chosen]

    ## What to find

    - **Identity:** what this project is, who it serves, and what it explicitly
      refuses to be — from README, docs, taglines, stated non-goals.
    - **Feature inventory:** the major capabilities as they exist today, short.
    - **Recent direction:** where the project is heading, from changelog and
      recent commits.
    - **Observed friction (facts only):** TODOs, open issues, half-built
      corners, workarounds users are told to perform. Report them; do not pitch
      fixes.
    - **Constraints:** platform, dependencies, audience commitments, anything
      a new feature must respect.
    - **Banked ideas:** prior Eureka shortlists under `docs/repertoire/ideas/`
      — paths plus the parked one-liners they contain (or "none").
    - **Existing docs:** paths and what they cover.

    Search broadly; read excerpts, not whole files. If something is absent, say
    so plainly rather than guessing.

    ## Report format (compact — the conductor stays lean)

    No code dumps, no proposals. Keep it tight:

    - **Identity brief:** 2-3 sentences.
    - **Inventory:** bullet list.
    - **Direction:** bullet list.
    - **Friction:** bullets, each with a `path` or issue reference.
    - **Constraints:** bullet list.
    - **Banked ideas:** `path` — one-liner each (or "none found").
    - **Notes:** anything surprising or worth the conductor's attention.
```
