# Quality-Control Prompt Template (final-verdict pair, QC half)

One half of the final verdict. A fresh **Opus** subagent verifies the whole PR
branch — original work plus review fixes — and returns a single, evidence-based
verdict on whether it is fit to publish and merge. Dispatch it **in parallel**
with the cross-model Codex reviewer (`codex-reviewer-prompt.md`); both are
read-only, and the PR clears only if QC says MERGEABLE *and* Codex raises no
critical or important finding. This is not a lens review — it is a holistic
"is this PR now sound" judgment, backed by actually running the build and
tests.

Always fill the scopes (`BASE..HEAD`, `FIXBASE..HEAD`), `Build`, and `Test`
with resolved values — never placeholders. If you do not have a real build/test
command, do not dispatch QC with a guess — find it first.

```
Agent tool (model: opus):
  description: "QC verdict for PR #[N]"
  prompt: |
    You are the final quality gate before review fixes are pushed to this pull
    request and its reviewers are answered. Give one verdict, and back it with
    evidence — do not guess.

    ## What this PR is for

    [one line: the PR's purpose, from its title/body]

    ## What the review fixes claim to resolve

    [Paste the verified items: F-id · demand · claimed resolution + SHA.]

    ## Scope, build, and test

    Whole PR: git diff [BASE..HEAD]
    The review fixes: git diff [FIXBASE..HEAD]
    Build command: [BUILD]
    Test command: [TEST]

    ## What to verify

    1. **Build** — run [BUILD]. Does it build cleanly?
    2. **Test** — run [TEST]. Do all tests pass? Are there meaningful tests
       pinning the fixed behavior, or are gaps hiding behind green? If no
       runnable build/test command is provided and you cannot discover one,
       return NOT_MERGEABLE with that as the blocking issue — never assume
       green.
    3. **Resolution** — is each feedback item above actually resolved in the
       code (not just claimed)? Note anything that would make the reviewer
       re-raise it.
    4. **Integration** — do the fixes sit coherently in the wider PR? Any
       regressions, contradictions, dead code, or half-finished seams across
       the fix boundary?
    5. **Ship-blockers** — anything that must not reach the base branch:
       security issues, data loss, broken core flows.

    Run the commands. Quote the real output (the build result and the test
    summary line). A verdict without evidence is not acceptable. Skim the fix
    diff against the items; rely on build + tests for the rest, and do not
    exhaustively reread every file in the PR.

    ## Report format (compact — the controller stays lean)

    - **Verdict:** MERGEABLE | NOT_MERGEABLE
    - **Evidence:** build result + test summary (the actual output lines).
    - **Blocking issues** (only if NOT_MERGEABLE): one per line, each tagged
      with a type so the controller can route it —
      `[defect] src/file.ext:88 · critical · null deref on empty input`
      `[implementation] F4 claimed fixed but the demanded validation is absent`
      Use `[defect]` for a localized, fixer-actionable bug and
      `[implementation]` for an unresolved or mis-resolved feedback item that
      needs re-verification or escalation.
    - **Notes:** non-blocking observations, if any.
```
