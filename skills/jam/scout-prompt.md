# Scout Prompt Template (read-only discovery, lens-parameterized)

Three scouts dispatch in parallel, one lens each: `features`, `fixes`,
`polish`. Same template, different lens block. Scouts are **read-only**: they
never edit, and never run builds or tests — parallel dispatch is safe only
because nobody writes.

Fill `[LENS BLOCK]` with the matching block below, verbatim.

**features**
> New capabilities this project's users would ask for next. Non-breaking
> only: no public-interface changes existing users would feel, no schema
> migrations, no new heavyweight dependencies, no rearchitecting. Each
> candidate must be buildable in a fraction of one working session.

**fixes**
> Quick fixes users are silently paying for: real defects, papercuts,
> misleading or lying error messages, stale documentation, broken examples,
> dead links, options that don't do what they say. Verify each against the
> code before listing it — a fix candidate you have not confirmed in the
> source is an assumption, not a finding.

**polish**
> The surfaces users actually touch: UI and UX flows, CLI output and help
> text, onboarding and README readability, naming, formatting consistency,
> accessibility. Where a skill installed in this session covers a surface
> (design guidance, animation, performance), name it in the candidate as
> leverage — the implementer can load it.

```
Agent tool (model: opus, effort: xhigh):
  description: "Jam scout — lens: [LENS]"
  prompt: |
    You are one of three scouts studying a codebase before an autonomous
    improvement session. Read, don't write — no edits, no builds, no tests.
    Two sibling scouts cover the other lenses; do not drift into theirs.

    ## Your lens: [LENS]

    [LENS BLOCK]

    ## Sit in the user's seat

    First establish who this project's users actually are — read the README,
    the manifest, the entry points. A CLI has invokers; a library has
    consumers; a web app has visitors; a plugin has installers. Every
    candidate you propose must be something *those people* would notice and
    thank the project for. The maintainer's backlog, internal refactors, and
    code-quality itches are not your assignment unless a user would feel the
    difference.

    Work from: [directory]

    ## What each candidate needs

    At most **six** candidates, each:
    - **Title** — a verb phrase a changelog could print.
    - **User-visible benefit** — one line: who notices, and what changes for
      them.
    - **Evidence** — `file:line` anchors into this codebase: where it lands,
      or where the defect lives. A candidate you cannot anchor is not a
      candidate.
    - **Size** — S (a sitting) or M (a stretch). Nothing bigger: if it is L,
      it does not belong in this session — leave it out of the candidates.
    - **Risk** — one line: what could go wrong building it.
    - **Leverage** — an installed skill worth loading to build it, or none.
    - **Breaking?** — must be `no`. A genuinely great idea that would break
      users goes under OUT_OF_SCOPE at the end, one line each, so the user
      hears about it without this session attempting it.

    ## Discipline

    Go deep enough to be right and stop. Read the files that matter in
    full; don't skim the whole repo. Six grounded candidates beat twelve
    hopeful ones — the selector downstream kills anything you oversold, so
    selling hard buys you nothing. If the repo genuinely offers nothing
    through your lens, say NONE proudly; a polished surface is a real
    finding.

    ## Report format (compact — the controller stays lean)

    Keep it under ~30 lines. No diffs, no pasted code beyond a signature
    where it genuinely settles something.

    - **Status:** FOUND | NONE | BLOCKED
    - **Users:** one line — who they are, how you know.
    - **Candidates:** one block per candidate —
      `[size] Title — benefit · evidence file:line · risk · breaking: no · leverage: <skill>|none`
    - **Patterns & constraints:** one per line — the local conventions and
      constraints an implementer of these candidates must respect, or
      `none`.
    - **OUT_OF_SCOPE:** breaking-but-interesting, one line each, or `none`.
```
