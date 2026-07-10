# Codex Reviewer (cross-model adversarial review)

Compose this stub with the shared contract in `shared/codex-reviewer-core.md`
— the conductor reads both and assembles one brief. The core carries the
invocation mechanics; this stub carries what to review.

**Object under review:** the whole branch (`BASE..HEAD`) against the plan —
the 4th panel reviewer in Phase 2, a general pass beside the 3 single-lens
Opus reviewers. Run the call **in the same batch** as the 3 Opus reviewer
dispatches so all four run together. `$BASE` is the base ref you captured in
Phase 0; pass it as `--base "$BASE"`. If Codex is unavailable, run the 3 Opus
reviewers only and say so in your report.

**Runtime review prompt** (substitute the bracket before sending):

    whole-branch integration review against the plan: [one line on what the
    branch should do]

**Issues location format:** `file:line`.

**Fallback `codex exec` prompt** (replace `${BASE}` with the resolved value
and paste the plan summary before sending):

```text
You are an adversarial, cross-model reviewer. Break confidence in this change.
Inspect: git diff ${BASE}..HEAD, then read the changed files in full. Assume the
change is wrong until the code proves otherwise. Prioritize expensive,
hard-to-detect failures: auth/trust boundaries, data loss, rollback/idempotency,
races, empty/null/timeout paths, schema/version skew. Report only material
findings; one strong finding beats several weak ones.

This branch was built to satisfy:
[PASTE the plan summary / requirements]

Output ONLY (same shape as the other reviewers):
- Verdict: PASS | FAIL
- Issues: file:line · severity · one-line description and why it matters
No diffs, no restating the code.
```
