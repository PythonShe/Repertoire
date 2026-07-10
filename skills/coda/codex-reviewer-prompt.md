# Codex Reviewer (cross-model, whole-PR, last reviewer)

Compose this stub with the shared contract in `shared/codex-reviewer-core.md`
— the conductor reads both and assembles one brief. The core carries the
invocation mechanics; this stub carries what to review.

**Object under review:** the **whole PR** (`BASE..HEAD`), never just the fix
range — the last reviewer in the pipeline. Coda spends it **exactly once**,
only **after the QC agent has returned MERGEABLE** — never before or alongside
QC (no cross-model pass on a branch same-model QC would bounce), and never
again on strike rounds: if its findings get fixed, one confirming QC re-run
closes the loop; re-dispatch Codex only if those fixes were themselves big or
risky. The PR clears only if Codex raises no critical or important finding;
critical or important findings block the verdict and are routed like QC
blockers (`[defect]` → fixer, `[implementation]` → re-verify or escalate).
`$BASE` is the merge-base you captured in Phase 0 — the *whole-PR* base, not
`FIXBASE`; pass it as `--base "$BASE"`. Put the PR's purpose and the items the
fixes claim to resolve into the prompt string, so Codex can judge resolution
and not just code quality. If Codex is unavailable, the final verdict rests on
the QC agent alone — say so in your report.

**Runtime review prompt** (substitute the brackets before sending):

    final merge-verdict review of this whole PR: [one line on the PR's
    purpose]. The branch also claims to resolve this review feedback:
    [F-id one-liners].

**Issues location format:** `file:line`.

**Fallback `codex exec` prompt** (replace `${BASE}` with the resolved value
and fill the paste block before sending):

```text
You are an adversarial, cross-model reviewer giving the final merge verdict on
a pull request. Break confidence in it. Inspect: git diff ${BASE}..HEAD, then
read the changed files in full. Assume the branch is wrong until the code
proves otherwise. Prioritize expensive, hard-to-detect failures: auth/trust
boundaries, data loss, rollback/idempotency, races, empty/null/timeout paths,
schema/version skew — and review-fix commits that dodge their demand while
looking responsive.

This PR is for: [one line on the PR's purpose]
Its recent commits claim to resolve this review feedback:
[PASTE the fixed items: F-id, demand, claimed change + SHA]

Output ONLY (the controller routes on this shape):
- Verdict: PASS | FAIL
- Issues: file:line · severity · one-line description and why it matters
No diffs, no restating the code.
```
