# Verifier Prompt Template (mechanical four-leg proof)

The terminal gate. A fresh subagent that runs, quotes, and judges
nothing the output does not show. The fixer's two-commit discipline (red test
committed before the fix) is what makes the red leg possible: with the fix
commits reverted in place, the repro test must fail again — and fail for the
*diagnosed* reason, in the real working environment, with all dependencies and
build artifacts present.

Always fill the SHAs and commands with resolved values from the fixer's
reports — never placeholders, never a guessed test command. List **every** fix
commit accrued across re-fix passes: the red leg reverts them all.

```
Agent tool (effort: xhigh):
  description: "Verify fix for [bug one-liner]"
  prompt: |
    You are the final verifier of a bug fix. Your verdict is mechanical:
    commands and their quoted output. You commit no changes and leave the
    branch exactly as you found it.

    ## What was fixed
    [PASTE: root cause one-liner; repro test file/name; TEST_SHA (the test
    commit); FIX_SHAS (every fix commit, oldest→newest); the fixer's recorded
    red proof — the failing output it observed before the fix existed]

    Work from: [directory]   Branch: [branch]
    Build: [BUILD]   Test: [TEST]
    Repro test command: [exact single-test command from the fixer's report]

    ## The proof — all four legs, in order

    1. **Red leg.** Confirm `git status` is clean, then un-apply the fix in
       the working tree without committing:
           git revert --no-commit [FIX_SHAS, newest first]
       Run the repro test command. It must FAIL, and fail for the *diagnosed*
       reason — compare against the fixer's recorded red proof: the same
       assertion or error, not an import failure or environment crash. Quote
       the failing line. Then restore and confirm clean:
           git reset --hard HEAD && git status
       - Repro test PASSES with the fix reverted → the proof is void: restore,
         report NOT_VERIFIED with that output, and stop — do not rationalize
         a green red-leg.
       - It fails for a *different* reason than diagnosed → the leg is
         invalid, not red: restore, report NOT_VERIFIED with both outputs.
       - The revert conflicts → `git revert --abort`, then fall back to a
         disposable worktree: `git worktree add /tmp/tuner-verify-[short
         FIX_SHA] [TEST_SHA]`, prepare it so the test can actually run
         (install dependencies / build as the project requires), run the
         repro test there, then `git worktree remove --force` it. If no route
         yields a valid red leg, report NOT_VERIFIED with the evidence.
    2. **Green leg.** On the branch at HEAD, run the repro test command. It
       must PASS.
    3. **Suite leg.** Run the full `[TEST]`. All green — quote the summary
       line. If no runnable test command was provided and you cannot discover
       one, return NOT_VERIFIED with that as the failing leg — never assume
       green.
    4. **Build leg.** Run `[BUILD]`. Clean — quote the result.

    ## Report format (compact — the controller stays lean)

    - **Verdict:** VERIFIED | NOT_VERIFIED
    - **Evidence:** one quoted output line per leg (red, green, suite, build).
    - If NOT_VERIFIED: which leg broke, and the output that shows it.
    Confirm the working tree ended clean (`git status`) and HEAD unmoved.
```
