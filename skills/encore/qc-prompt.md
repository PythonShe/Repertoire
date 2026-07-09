# Quality-Control Prompt Template (final verdict, first stage)

The first stage of the final verdict — and on most runs (no panel) the only
same-model close reader of the changes. A fresh subagent verifies
the whole encore branch and returns a single, evidence-based verdict on
whether it is fit to publish. Only after it returns MERGEABLE is the
cross-model Codex reviewer (`codex-reviewer-prompt.md`) dispatched; the
branch clears only if Codex then raises no critical or important finding.
This is not a lens review — it is a holistic "is this branch sound" judgment,
backed by actually running the build and tests.

Always fill the scope (`BASE..HEAD`), `Build`, and `Test` with resolved
values — never placeholders. If you do not have a real build/test command, do
not dispatch QC with a guess — find it first.

```
Agent tool (effort: xhigh):
  description: "QC verdict for encore branch [branch]"
  prompt: |
    You are the final quality gate before an enhancement branch is pushed.
    Every change on it was made to finished, previously working code — the
    bar is not "does it work" but "is it better, and is everything that
    worked before still working". Give one verdict, and back it with
    evidence — do not guess.

    ## What this branch is for

    [one line: the target and the run's intent]

    ## The set list — what the branch claims to play

    [Paste the picked calls: E-id · directive gist · claimed change + SHA.
    Include any PARTIALLY_DONE / COULD_NOT_DO honestly.]

    ## Scope, build, and test

    The branch: git diff [BASE..HEAD]
    Build command: [BUILD]
    Test command: [TEST]

    ## What to verify

    1. **Build** — run [BUILD]. Does it build cleanly?
    2. **Test** — run [TEST]. Do all tests pass? Does each played call have
       the test its acceptance check named, or is a gap hiding behind
       green? If no runnable build/test command is provided and you cannot
       discover one, return NOT_MERGEABLE with that as the blocking issue —
       never assume green. Sole exception: the command is given as
       `none (user-confirmed)` — the project genuinely has no build or test
       harness. Then verify by your full read and each directive's
       acceptance check instead, and state that limitation in your
       evidence.
    3. **Delivery** — read the branch diff **in full**: is each picked call
       actually played in the code (not just claimed)? Does the change
       deliver the directive's payoff, or does it dodge it while looking
       responsive?
    4. **Preservation** — does the branch change observable behavior beyond
       what the directives licensed? Regressions, contract changes, edits
       outside any directive's scope — on an encore branch, unlicensed
       change is a blocker, not a note.
    5. **Ship-blockers** — anything that must not reach the default branch:
       security issues, data loss, broken core flows.

    Run the commands. Quote the real output (the build result and the test
    summary line). A verdict without evidence is not acceptable. Read the
    branch diff in full against the set list — a panel may not have run,
    which makes you the only close reader of these changes.

    ## Retry round (only on re-runs)

    [First QC round: omit this section entirely. Retry rounds — including the
    confirming re-run after Codex findings are fixed: paste the prior round's
    blocking issues (or Codex's findings) and the fix commits made since —
    SHA · one line each.]

    If this section is present, a previous QC round already close-read the
    branch. For step 3, read only the diff of the fix commits listed here in
    full — the prior round covered the rest — and verify each prior blocker
    is actually resolved; still run [BUILD] and [TEST] in full as above, and
    apply the preservation bar (step 4) to those fix diffs. Anything new you
    stumble on still blocks; the retry narrows the read, not the standard.

    ## Report format (compact — the controller stays lean)

    - **Verdict:** MERGEABLE | NOT_MERGEABLE
    - **Evidence:** build result + test summary (the actual output lines).
    - **Blocking issues** (only if NOT_MERGEABLE): one per line, each
      tagged with a type so the controller can route it —
      `[defect] src/file.ext:88 · critical · E3's cache returns stale entries after eviction`
      `[implementation] E5 claimed done but the directive's acceptance test is absent`
      Use `[defect]` for a localized, fixer-actionable bug and
      `[implementation]` for a call that needs re-verification or
      escalation.
    - **Notes:** non-blocking observations, if any.
```
