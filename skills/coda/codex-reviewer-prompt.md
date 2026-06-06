# Codex Reviewer (cross-model, whole-PR, final verdict)

The second half of the final-verdict pair. A different model sees different
things — it catches what a room full of Claudes will agree to overlook — so
Coda spends it where it counts: on the merge call, with **global scope**. Its
range is always the whole PR (`BASE..HEAD`), never just the fix range, and it
runs **in parallel with the QC agent** (both are read-only). The PR clears only
if QC says MERGEABLE *and* Codex raises no critical or important finding.

## Use the adversarial-review runtime, not codex-rescue

The right tool is Codex's **adversarial-review runtime**, which is purpose-built
to "break confidence in the change": it runs a skeptical, evidence-grounded
review and returns structured findings.

Do **not** use the `codex-rescue` subagent here. It is a write-capable forwarder
to Codex's `task` runner and explicitly refuses to run reviews — it would try to
*implement* changes instead of reviewing them. Wrong job entirely.

## Availability check

Look for the Codex plugin's companion script first, then the bare CLI:

```bash
COMPANION=$(ls -t ~/.claude/plugins/cache/*/codex/*/scripts/codex-companion.mjs 2>/dev/null | head -1)
[ -n "$COMPANION" ] && echo "plugin: $COMPANION"
command -v codex >/dev/null && echo "cli: $(command -v codex)"
```

If neither prints, treat Codex as **unavailable**: the final verdict rests on
the QC agent alone, and you note the omission in your report. That is the
documented fallback, not a silent drop.

## Preferred: the adversarial-review runtime

Pass flags as **separate shell arguments** (do not cram `--wait`/`--base` into
one quoted string — they would be read as positional text and ignored).
`$BASE` is the merge-base you captured in Phase 0 — the *whole-PR* base, not
`FIXBASE`. Put the PR's purpose and the items the fixes claim to resolve into
the prompt string, so Codex can judge resolution and not just code quality. Run
this Bash call **in the same batch** as the QC dispatch so the pair runs
together.

```bash
node "$COMPANION" adversarial-review --wait --base "$BASE" "final merge-verdict review of this whole PR: [one line on the PR's purpose]. The branch also claims to resolve this review feedback: [F-id one-liners]."
```

The exact argument signature can vary by companion version — if unsure, confirm
it on a tiny scope once before relying on it. The runtime prints a structured
result (a ship/no-ship summary plus findings with file, line range, confidence,
and a recommendation).

**Treat empty or missing stdout as a failed reviewer, not a pass.** If the call
returns nothing, note Codex as absent (as in the fallback) — do not fold a blank
result into the verdict as if it approved.

When it does return, normalize its output to the compact shape the controller
routes on:

- **Verdict:** PASS | FAIL
- **Issues:** `file:line · severity · one-line description`

Critical or important findings block the verdict and are routed like QC
blockers (`[defect]` → fixer, `[implementation]` → re-verify or escalate).

## Fallback: bare `codex exec`

If the plugin companion is absent but the `codex` CLI exists, run a headless
review with an inline adversarial prompt. **Replace `${BASE}` with the resolved
value before sending** (the single-quoted heredoc does not expand it). Verify
flags with `codex exec --help` — they vary by version.

```bash
codex exec --cd "[repo dir]" "$(cat <<PROMPT
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
PROMPT
)"
```

Either way, the controller never reads the diff itself — only the compact
verdict returns to you.
