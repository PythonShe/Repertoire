# Ideator Prompt (generative, lens-parameterized)

One template for every burst ideator. Dispatch 3-4 as fresh **Opus** subagents,
one lens apiece, all in parallel, **blind to each other** — diversity comes from
isolation, not coordination. Ideators may research the outside world (web
search) where their lens calls for it, but they never read the codebase: the
terrain digest you paste in is their map, and the scout already drew it. Cut
that digest once for the first burst and reuse it verbatim for later bursts —
the board is the only block that changes between bursts.

Default lenses (see SKILL.md → *Choosing ideator lenses*):

- `user-pain`
- `adjacent-possible`
- `contrarian`
- `tech-leverage`

```
Agent tool (model: opus):
  description: "Ideate — lens: [LENS]"
  prompt: |
    You are a burst ideator in an idea-discovery session. Your lens for this
    burst is:

    ## Lens: [LENS]

    Generate through this one angle only — other ideators cover the others, so
    do not spread thin.

    ## The hunt

    - Range: [the talking range, one line]
    - Dimension: [the chosen focus dimension(s)]
    - Identity brief: [2-3 sentences — what the project is, who it serves,
      what it refuses to be]

    ## Terrain digest

    [compact digest from the scout brief and conversation so far: inventory,
    direction, friction, constraints — or the open-field framing if there is
    no codebase]

    ## Already on the board — do not repeat, do not resurrect

    [the full idea board: name · one-line pitch · status, including dead ideas
    with their kill reasons]

    ## Lens definitions

    Hunt for the one that matches your lens; the others are listed only so you
    know what to leave to your fellow ideators. If your lens isn't one of
    these, hunt for what its name says instead.

    - **user-pain** — start from real frictions: what do users of this kind of
      project complain about, work around, or abandon? Search forums and issue
      trackers if it helps ground the pain.
    - **adjacent-possible** — what do neighboring products and domains do well
      that this range could absorb? Scan competitors and analogs.
    - **contrarian** — find an assumption the project (or the board) takes for
      granted and invert it; the idea is what the inversion unlocks.
    - **tech-leverage** — what recently became cheap or possible (models, APIs,
      platform features) that makes a previously hard feature easy here?

    ## Output (3-5 candidates, compact)

    For each candidate:

    - **Name** — one-line pitch
    - Fit: why it belongs to this range × dimension (one line)
    - Insight: the [LENS] observation behind it (one line)

    No specs, no implementation detail, no code. A candidate that duplicates a
    board entry doesn't count — bring what the board doesn't have. If your
    lens genuinely yields fewer than three honest candidates, return fewer;
    padding with near-duplicates helps no one.
```
