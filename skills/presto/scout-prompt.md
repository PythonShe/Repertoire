# Scout Prompt Template (read-only recon)

The run's only investigation. One scout by default; a second **only** for a
genuinely separate surface — an external one (a library's API, a protocol, a
version's behavior) or an unrelated part of the codebase the change must meet.
Two is the ceiling.

Scouts are **read-only**: they never edit, and never run builds or tests. Fill
`Brief` and `Assignment` with real text — a scout handed a placeholder
investigates nothing.

```
Agent tool (model: opus, effort: xhigh):
  description: "Recon: [short request handle]"
  prompt: |
    You are scouting a codebase for one scoped change that is about to be
    built. Read, don't write — you make no edits, and you run no builds or
    tests. Your report is what the user will approve an approach from, so it
    has to be right and it has to be short.

    ## The request

    [Brief: the restated request, verbatim]

    **Acceptance:** [how anyone would know it works]

    ## Your assignment

    [Assignment: for the primary scout — "map where this change lands in this
    codebase and weigh the ways to build it". For a second scout — the
    separate surface only, e.g. "establish how library X's streaming API
    behaves in version Y, and what it costs us"; say plainly that the other
    surface is covered by another scout so this one does not duplicate it.]

    Work from: [directory]

    ## What to find

    1. **The landing zone** — the files and functions this change touches or
       sits beside, with `file:line` anchors. Name what already exists that
       does something similar; the change should look like its neighbors.
    2. **Patterns and constraints** — the local conventions the change must
       follow, and anything that forbids an otherwise obvious approach
       (a framework rule, an interface other code depends on, a migration
       already in flight).
    3. **Approaches** — one to three ways to build it, each with its trade-off
       in a line or two, and **your recommendation with the reason**. If there
       is genuinely only one sane way, say that; do not invent alternatives to
       look thorough.
    4. **The size call** — `S`, `M`, or `L`, with a rough file and line
       estimate. Be honest and be specific: this number decides whether the
       controller builds it directly or hands it to a subagent, and an
       optimistic call sends the work to the wrong seat.
       - `S` — a handful of files in one module, no new interface.
       - `M` — several files, or a new component inside an existing module.
       - `L` — spans modules, needs a new subsystem, or rests on a design
         decision nobody has made. Say so loudly: `L` means this work should
         leave the fast lane for `/repertoire:libretto` or `/repertoire:score`.
    5. **Open questions** — anything only the user can decide. These go
       straight into the approval gate, so raise them; a guess made here
       becomes a guess built into the code.

    ## Discipline

    Go deep enough to be right and stop. You are not auditing the codebase or
    reviewing its quality — you are answering "where does this go, how should
    it be built, and how big is it". Read the files that matter in full;
    don't skim the whole repo. If the request points at something that does
    not exist or that you cannot locate, say so — report BLOCKED with what you
    searched rather than guessing at a landing zone.

    ## Report format (compact — the controller stays lean)

    Keep it under ~25 lines. No diffs, no pasted code beyond a signature or a
    short snippet where it genuinely settles something.

    - **Status:** FOUND | BLOCKED
    - **Landing zone:** `path/file.ext:120` · one line on what lives there.
    - **Patterns & constraints:** one per line.
    - **Approaches:** one per line — name · trade-off · `RECOMMENDED` on one of
      them, with the reason.
    - **Size:** S | M | L · rough files/lines · one line of justification.
    - **Open questions:** one per line, or `none`.
```
