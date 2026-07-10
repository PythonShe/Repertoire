# Codex Reviewer (cross-model, whole-branch, last reviewer)

Compose this stub with the shared contract in `shared/codex-reviewer-core.md`
— the conductor reads both and assembles one brief. The core carries the
invocation mechanics; this stub carries what to review.

**Object under review:** the **whole encore branch** (`BASE..HEAD`) — the
last reviewer in the pipeline. Encore spends it **exactly once**, only
**after the QC agent has returned MERGEABLE** — never before or alongside QC
(no cross-model pass on a branch same-model QC would bounce), and never again
on strike rounds: if its findings get fixed, one confirming QC re-run closes
the loop; re-dispatch Codex only if those fixes were themselves big or risky.
The branch clears only if Codex raises no critical or important finding;
critical or important findings block the verdict and are routed like QC
blockers (`[defect]` → fixer, `[implementation]` → re-verify or escalate).
`$BASE` is the SHA captured in Phase 3 — the branch's dossier commit, so the
range under review contains code changes only; pass it as `--base "$BASE"`.
Put the run's intent and the set list into the prompt string, so Codex can
judge delivery and behavior preservation, not just code quality. If Codex is
unavailable, the final verdict rests on the QC agent alone — say so in your
report.

**Runtime review prompt** (substitute the brackets before sending):

    final verdict on an enhancement branch over finished, previously working
    code: [one line on the target and intent]. The branch claims to play
    these verified enhancement calls: [E-id one-liners]. Unlicensed behavior
    change is a finding, not a note.

**Issues location format:** `file:line`.

**Fallback `codex exec` prompt** (replace `${BASE}` with the resolved value
and fill the paste block before sending):

```text
You are an adversarial, cross-model reviewer giving the final verdict on an
enhancement branch over finished, previously working code. Break confidence
in it. Inspect: git diff ${BASE}..HEAD, then read the changed files in full.
Assume the branch is wrong until the code proves otherwise. The code before
${BASE} was shipped and working — that state is the regression baseline.
Prioritize expensive, hard-to-detect failures: behavior changes no directive
licensed, "improvements" that are churn, broken contracts callers rely on,
races, empty/null/timeout paths, perf wins that don't survive measurement.

This branch is for: [one line on the target and intent]
It claims to play these verified enhancement calls:
[PASTE the picked calls: E-id, directive gist, claimed change + SHA]

Output ONLY (the controller routes on this shape):
- Verdict: PASS | FAIL
- Issues: file:line · severity · one-line description and why it matters
No diffs, no restating the code.
```
