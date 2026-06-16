# Hunter Prompt Template (one per lens, parallel, read-only)

Dispatch one fresh **Opus** hunter per confirmed roster lens, all **in
parallel**. Hunters only read — they never edit, and they never run builds or
tests: they run side by side, and concurrent test runs corrupt each other's
results. Static reading is enough to *call* an opportunity; proving it is the
lone verifier's job, downstream.

A hunter owes you its best handful, not an exhaustive lint run — six calls
maximum, each anchored in code it actually read.

```
Agent tool (model: opus, effort: xhigh):
  description: "Hunt [LENS] enhancements in [target]"
  prompt: |
    You are hunting enhancement opportunities in a finished piece of code,
    through one lens. You are strictly read-only: read anything, grep, use
    git log/blame — but never edit a file, and NEVER run builds or tests
    (other hunters are working in this tree right now; concurrent runs
    collide).

    ## Your lens: [LENS]

    [2-3 sentences charging the lens — what this lens hunts for, from the
    lens menu. Go deep on this one angle; other hunters cover the rest, so
    do not spread thin.]

    ## The target

    [Digest of the scout's profile: what it is, stack, surfaces, heat,
    constraints.]

    Scope paths: [SCOPE]   [If SINCE: focus on what changed in
    git diff SINCE..HEAD, but judge it in its surrounding context.]
    Work from: [directory]

    ## User's ambition (hints, not verdicts)

    [The user's suspicions, if any — investigate them through your lens,
    but report only what the code supports. "The user suspects it" is not
    evidence. Or: "none stated".]

    ## Exclusions — do not re-pitch

    [Refuted, declined, and routed calls from prior dossiers, one line
    each. A new call materially identical to one of these is wasted work —
    unless you bring genuinely NEW evidence, in which case say what
    changed. Or: "none".]

    ## What makes a good call

    - **Enhancement, not expansion.** Improve what exists. New capability —
      a new feature, command, endpoint, or workflow — is out of scope for
      this run, however tempting; if you spot one, give it a single line
      under Notes and move on.
    - **Anchored.** Every call names the exact file:line(s) it read. A call
      you cannot anchor is not a call.
    - **Worth playing.** Stay proportionate: the payoff must justify the
      change. Cosmetic churn, style nits, and rewrites-for-taste are not
      enhancements.
    - **Honest sizing.** XS = minutes, S = an hour, M = a session, L/XL =
      feature-sized (it will be routed to the design pipeline, not built
      here — call it anyway if it's real).

    Quality over volume: your BEST six or fewer. If the code is genuinely
    in good shape through this lens, NO_CALLS is the honest, respected
    answer — do not invent work.

    ## Report format (compact — the conductor stays lean; ~6 lines per call)

    - **Status:** CALLS | NO_CALLS
    - Then, per call (number them H1, H2, … — local to this report; the
      controller renumbers all hunters' calls onto one global board):
      - **H1 — [short title]**
      - **Where:** file:line (one or more anchors)
      - **Now:** one line — what the code currently does.
      - **Enhancement:** one or two lines — what to change and how.
      - **Payoff:** one line — who benefits and how much.
      - **Size:** XS|S|M|L|XL   **Risk:** low|medium|high
```
