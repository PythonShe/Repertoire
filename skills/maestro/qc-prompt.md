# Quality-Control Prompt Template (final merge gate)

The Phase 3 gate. A fresh **Opus** subagent verifies the whole branch and returns a
single, evidence-based verdict on whether it is fit to merge. This is not a lens
review — it is a holistic "is this done and safe to ship" judgment, backed by
actually running the build and tests.

Always fill `Scope`, `Build`, and `Test` with resolved values. If you do not have a
real build/test command, do not dispatch QC with a guess — find it first.

```
Agent tool (model: opus, effort: xhigh):
  description: "QC verdict for [branch]"
  prompt: |
    You are the final quality gate before this branch goes to the user for merge.
    Give one verdict, and back it with evidence — do not guess.

    ## What this branch was built to do

    [Paste the plan summary / the full set of requirements the branch must satisfy]

    ## Scope, build, and test

    Whole branch: git diff [Scope, e.g. BASE..HEAD]
    Build command: [BUILD]
    Test command: [TEST]

    ## What to verify

    1. **Build** — run `[BUILD]`. Does it build cleanly?
    2. **Test** — run `[TEST]`. Do all tests pass? Are there meaningful tests for
       the new behavior, or are gaps hiding behind green? If no runnable build/test
       command is provided and you cannot discover one, return NOT_MERGEABLE with
       that as the blocking issue — never assume green.
    3. **Requirements** — is every requirement above actually satisfied by the code
       (not just claimed)? Note anything missing.
    4. **Integration** — do the parts fit together coherently? Any contradictions,
       dead code, half-finished seams, or regressions across boundaries?
    5. **Ship-blockers** — anything that must not reach main: security issues, data
       loss, broken core flows.

    Run the commands. Quote the real output (the build result and the test summary
    line). A verdict without evidence is not acceptable. Skim the diff for the
    requirements; rely on build + tests for the rest, and do not exhaustively reread
    every file.

    ## Report format (compact — the controller stays lean)

    - **Verdict:** MERGEABLE | NOT_MERGEABLE
    - **Evidence:** build result + test summary (the actual output lines).
    - **Blocking issues** (only if NOT_MERGEABLE): one per line, each tagged with a
      type so the controller can route it —
      `[defect] path/file.ext:88 · critical · null deref on empty input`
      `[implementation] feature X from the plan is not built anywhere`
      Use `[defect]` for a localized, fixer-actionable bug and `[implementation]`
      for a missing/incorrect implementation or design gap a fixer cannot localize.
    - **Notes:** non-blocking observations, if any.
```
