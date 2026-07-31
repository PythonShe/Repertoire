# Quality-Control Prompt Template (the closing gate)

The last seat in the run. A fresh subagent verifies the branch and returns
one evidence-based verdict. It is short by **scope**, not by standard: it
reads the diff against the spec and leans on the build and tests for the
rest. Always fill `Scope`, `Build`, and `Test` with resolved values — or the
user-confirmed `none` token, never a guess.

```
Agent tool (effort: xhigh):
  description: "QC verdict: [short surface handle]"
  prompt: |
    You are the last gate before this motion change goes back to the user.
    Give one verdict and back it with evidence — do not guess, and do not
    infer green.

    ## What this change was supposed to do

    [Brief: the restated request, verbatim]

    **Acceptance:** [the feel goal in words, plus anything mechanical]

    **Approved motion spec:** [the spec the user locked at the gate, with
    its exact values]

    ## Carried forward

    [Findings the fixer disputed or could not fix, and any feel notes from
    the reviewer, verbatim — or `none`. A disputed finding is yours to
    settle: agree with the fixer, or block on it.]

    ## Scope, build, and test

    Whole change: git diff [Scope, e.g. BASE..HEAD]
    Build command: [BUILD]
    Test command: [TEST]

    ## What to verify

    1. **Build** — run `[BUILD]`. Does it build cleanly?
    2. **Test** — run `[TEST]`. Do all tests pass? If no runnable command
       was provided and you cannot discover one, return NOT_SHIPPABLE with
       that as the blocking issue — never assume green. Given
       `none (user-confirmed)`, judge by your read and state that limitation
       in your evidence.
    3. **The spec, satisfied by the code** — read the diff and confirm every
       line of the approved spec is implemented with its exact values, and
       that nothing outside the spec was animated. A value that drifted from
       the spec is a blocker even when it looks tasteful.
    4. **Reduced motion** — wherever the diff adds movement, confirm
       `prefers-reduced-motion` handling exists and is gentler-not-zero, and
       hover motion is gated. Missing handling is a blocker.
    5. **Ship-blockers** — anything that must not reach main: broken core
       flows, layout-property animation on a hot path, a half-finished seam,
       a dependency the spec never licensed.

    Run the commands and quote the real output — the build result and the
    test summary line. A verdict without evidence is not acceptable. Read
    the diff in full (it is small, by construction); do not go spelunking
    through files it never touched. What you cannot verify from code —
    feel on a real device, a gesture's momentum — list under Feel checks for
    the user rather than blocking on it.

    ## Retry round (only on re-runs)

    [First QC round: omit this section entirely. Retry round: paste the
    prior round's blocking issues and the fix commits made since — SHA · one
    line each.]

    If this section is present, run `[BUILD]` and `[TEST]` in full as above,
    read the diff of the listed fix commits in full, and verify each prior
    blocker is actually resolved — then check only where those fixes touch
    the rest of the change. Anything new you stumble on still blocks; the
    retry narrows the read, not the standard.

    ## Report format (compact — the controller stays lean)

    - **Verdict:** SHIPPABLE | NOT_SHIPPABLE
    - **Evidence:** build result + test summary (the actual output lines).
    - **Blocking issues** (only if NOT_SHIPPABLE): one per line, typed —
      `[defect] path/file.ext:88 · critical · movement added with no reduced-motion handling`
      `[implementation] the drawer spec's spring is still a 400ms keyframe`
      Use `[defect]` for a localized, fixer-actionable issue and
      `[implementation]` for a gap that goes back to an implementer.
    - **Feel checks for the user:** the interactions a human should try by
      hand, one line each, or `none`.
    - **Notes:** non-blocking observations, if any.
```
