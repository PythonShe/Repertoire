# Quality-Control Prompt Template (the finale's gate)

The last seat of the session. A fresh subagent verifies the whole branch and
returns one evidence-based verdict. Not a lens review — a holistic "does this
session's work actually ship" judgment, backed by running the build and the
tests.

Always fill `Scope`, `Build`, and `Test` with resolved values. If Phase 0
recorded `none (user-confirmed)` for a command, pass that token through —
never a guess.

```
Agent tool (effort: xhigh):
  description: "Jam QC verdict for [branch]"
  prompt: |
    You are the final quality gate of an autonomous improvement session. No
    user has reviewed this branch — your verdict is what stands between it
    and the user's screen. Give one verdict, and back it with evidence.

    ## What this session set out to ship

    [The docket: every job's title, brief, and acceptance. Mark jobs that
    were dropped or amputated — their work must be ABSENT, and cleanly so.]

    [Unfixed or disputed findings carried from earlier phases, if any.]

    ## Scope, build, and test

    Whole branch: git diff [Scope, e.g. BASE..HEAD]
    Build command: [BUILD]
    Test command: [TEST]

    ## What to verify

    1. **Build** — run `[BUILD]`. Does it build cleanly? If the command is
       `none (user-confirmed)`, judge buildability by reading and say so in
       your evidence.
    2. **Test** — run `[TEST]`. Do all tests pass? Are there meaningful
       tests for the new behavior, or are gaps hiding behind green? If the
       command is `none (user-confirmed)`, judge by reading and say so in
       your evidence; if no command was provided at all and you cannot
       discover one, return NOT_SHIPPABLE with that as the blocker — never
       assume green.
    3. **Acceptance** — for each *shipped* docket job: is its acceptance
       line actually satisfied by the code, not just claimed?
    4. **Non-breaking** — this session promised users non-breaking work.
       Any public-interface or observable-behavior break is a blocker.
    5. **Clean amputations** — for each dropped or amputated job: no debris.
       No dangling imports, no dead flags, no docs describing behavior the
       branch no longer has.
    6. **Ship-blockers** — anything that must not reach the user: security
       issues, data loss, broken core flows.

    Run the commands. Quote the real output (the build result and the test
    summary line). A verdict without evidence is not acceptable. Skim the
    diff for the acceptance checks; rely on build + tests for the rest, and
    do not exhaustively reread every file.

    ## Retry round (only on re-runs)

    [First QC round: omit this section entirely. Retry rounds: paste the
    prior round's blocking issues and the fix commits made since — SHA · one
    line each.]

    If this section is present, a previous QC round already examined this
    branch. Do not re-derive everything: run `[BUILD]` and `[TEST]` in full
    as above, read the diff of the fix commits listed here in full, and
    verify each prior blocker is actually resolved — then check only where
    those fixes touch the rest of the branch. Anything new you stumble on
    still blocks; the retry narrows the read, not the standard.

    ## Report format (compact — the controller stays lean)

    - **Verdict:** SHIPPABLE | NOT_SHIPPABLE
    - **Evidence:** build result + test summary (the actual output lines).
    - **Blocking issues** (only if NOT_SHIPPABLE): one per line, each tagged
      so the controller can route it —
      `[defect] path/file.ext:88 · critical · null deref on empty input`
      `[implementation] job 3's acceptance is not satisfied anywhere`
      Use `[defect]` for a localized, fixer-actionable bug and
      `[implementation]` for a job whose build is missing or wrong in a way
      a fixer cannot localize — the controller re-dispatches or amputates.
    - **Notes:** non-blocking observations, if any.
```
