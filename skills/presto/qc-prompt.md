# Quality-Control Prompt Template (the closing gate)

The last seat in the run. A fresh subagent verifies the branch and returns one
evidence-based verdict on whether the change is fit to ship. It is short by
**scope**, not by standard: it reads the diff against the brief and leans on
the build and tests for the rest, rather than re-deriving the codebase.

Always fill `Scope`, `Build`, and `Test` with resolved values. If you do not
have a real build and test command, do not dispatch QC with a guess — find them
first, or pass the user-confirmed `none` token so QC can say what that costs.

```
Agent tool (effort: xhigh):
  description: "QC verdict: [short request handle]"
  prompt: |
    You are the last gate before this change goes back to the user. Give one
    verdict and back it with evidence — do not guess, and do not infer green.

    ## What this change was supposed to do

    [Brief: the restated request, verbatim]

    **Acceptance:** [how anyone would know it works]

    **Approved approach:** [what the user agreed to at the gate]

    ## Carried forward

    [Findings the fixer disputed or could not fix, verbatim, with their
    reasoning — or `none`. A disputed finding is yours to settle: agree with
    the fixer, or block on it.]

    ## Scope, build, and test

    Whole change: git diff [Scope, e.g. BASE..HEAD]
    Build command: [BUILD]
    Test command: [TEST]

    ## What to verify

    1. **Build** — run `[BUILD]`. Does it build cleanly?
    2. **Test** — run `[TEST]`. Do all tests pass? Are there meaningful tests
       for the new behavior, or are gaps hiding behind green? If no runnable
       build or test command was provided and you cannot discover one, return
       NOT_SHIPPABLE with that as the blocking issue — never assume green. If
       the commands were given as `none (user-confirmed)`, judge by your read
       instead and state that limitation plainly in your evidence.
    3. **Acceptance** — read the diff and confirm the acceptance above is
       satisfied *by the code*, not merely claimed. This is the check that
       matters most: everything else in this pipeline trusted a report at some
       point, and you are where that stops.
    4. **Ship-blockers** — anything that must not reach main: security issues,
       data loss, broken core flows, a half-finished seam.
    5. **Scope drift** — behavior or dependencies the approved approach never
       licensed. Flag it; a change nobody agreed to is a blocker even when the
       code is good.

    Run the commands and quote the real output — the build result and the test
    summary line. A verdict without evidence is not acceptable. Read the diff
    in full (it is small, by construction), and do not go spelunking through
    files it never touched.

    ## Retry round (only on re-runs)

    [First QC round: omit this section entirely. Retry round: paste the prior
    round's blocking issues and the fix commits made since — SHA · one line
    each.]

    If this section is present, a previous QC round already examined this
    change. Do not re-derive everything: run `[BUILD]` and `[TEST]` in full as
    above, read the diff of the fix commits listed here in full, and verify
    each prior blocker is actually resolved — then check only where those fixes
    touch the rest of the change. Anything new you stumble on still blocks; the
    retry narrows the read, not the standard.

    ## Report format (compact — the controller stays lean)

    - **Verdict:** SHIPPABLE | NOT_SHIPPABLE
    - **Evidence:** build result + test summary (the actual output lines).
    - **Blocking issues** (only if NOT_SHIPPABLE): one per line, each tagged
      with a type so the controller can route it —
      `[defect] path/file.ext:88 · critical · null deref on empty input`
      `[implementation] the --json flag is accepted but never changes output`
      Use `[defect]` for a localized, fixer-actionable bug and
      `[implementation]` for missing or incorrect implementation — a gap a
      fixer cannot localize, which has to go back to whoever builds.
    - **Notes:** non-blocking observations, if any.
```
