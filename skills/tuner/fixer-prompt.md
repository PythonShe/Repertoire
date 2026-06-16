# Fixer Prompt Template (red→green, two commits)

Dispatched once the cross-examination has settled the root cause. A fresh
**Opus** subagent that repairs test-first: the red repro test is committed
*before* the fix exists, so the verifier can later prove red→green mechanically
from the commit history.

For a re-fix pass (after a reviewer FAIL or verifier NOT_VERIFIED), add a
`## Findings to address` section with the gate's findings pasted in — the red
test usually already exists, so the fixer extends it only if the findings show
it pins the bug too loosely.

```
Agent tool (model: opus, effort: xhigh):
  description: "Fix [bug one-liner]"
  prompt: |
    You are fixing a diagnosed bug. The investigation is done and was
    cross-examined between two independent investigators — your job is the
    repair, test-first.

    ## The agreed root cause
    [PASTE: mechanism with file:line, evidence chain, proposed minimal fix]

    ## Bug brief
    [PASTE the bug brief — symptom, expected, repro]

    Work from: [directory]   Branch: [branch]
    Build: [BUILD]   Test: [TEST]
    Confirm you are on the named branch before committing.

    ## Discipline — in this exact order
    1. **Repro test first.** Write the minimal test that captures the bug:
       assert the *expected* behavior, not the absence of a crash. Run it — it
       must FAIL, for the diagnosed reason. Quote the failing output.
       - If it will not go red, STOP. Do not fix anything. Report
         CAUSE_DISPUTED with what you observed — a repro that passes means the
         diagnosis is suspect, and that has to go back to the judges.
    2. **Commit the test** on its own: `test: red repro for [bug]`. Yes, this
       commit is deliberately red — it is the verifier's proof point.
    3. **Minimal fix.** Change exactly what the diagnosed mechanism demands —
       no refactors, no cleanups, no defensive special-cases that hide the
       symptom while the defect lives on. If the fix keeps growing beyond the
       diagnosed mechanism, stop and report instead of forcing it.
    4. **Prove green.** The repro test passes; the full `[TEST]` suite passes.
       Quote both results.
    5. **Commit the fix** separately: `fix: [root cause one-liner]`.

    ## Report format (compact — the controller stays lean)

    - **Status:** FIXED | COULD_NOT_FIX | CAUSE_DISPUTED
    - **Commits:** TEST_SHA (the test commit), FIX_SHA (the fix commit). On a
      re-fix pass, every new fix commit SHA — the verifier reverts all of them.
    - **Repro test command:** the exact command that runs the single repro test
      (the verifier needs this verbatim).
    - **Red proof:** the failing line before the fix. **Green proof:** repro +
      suite result lines after.
    - What you changed, one line per file.
    - Anything that surprised you — that goes to the reviewer.
```
