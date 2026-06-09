# Codex Reviewer (cross-model adversarial review)

The 4th panel reviewer in Phase 2. A different model sees different things — it
catches what a room full of Claudes will agree to overlook.

## Use the adversarial-review runtime, not codex-rescue

The right tool is Codex's **adversarial-review runtime**, which is purpose-built to
"break confidence in the change": it runs a skeptical, evidence-grounded review and
returns structured findings.

Do **not** use the `codex-rescue` subagent here. It is a write-capable forwarder to
Codex's `task` runner and explicitly refuses to run reviews — it would try to
*implement* changes instead of reviewing them. Wrong job entirely.

## Availability check

Look for the Codex plugin's companion script first, then the bare CLI:

```bash
COMPANION=$(ls -t ~/.claude/plugins/cache/*/codex/*/scripts/codex-companion.mjs 2>/dev/null | head -1)
[ -n "$COMPANION" ] && echo "plugin: $COMPANION"
command -v codex >/dev/null && echo "cli: $(command -v codex)"
```

If neither prints, treat Codex as **unavailable**: run the 3 Fable 5 reviewers only
and note the omission in your report. That is the documented fallback, not a silent
drop.

## Preferred: the adversarial-review runtime

Pass flags as **separate shell arguments** (do not cram `--wait`/`--base` into one
quoted string — they would be read as positional text and ignored). `$BASE` is the
base ref you captured in Phase 0. Run this Bash call **in the same batch** as the 3
Fable 5 reviewer dispatches so all four run together.

```bash
node "$COMPANION" adversarial-review --wait --base "$BASE" "whole-branch integration review against the plan: [one line on what the branch should do]"
```

The exact argument signature can vary by companion version — if unsure, confirm it
on a tiny scope once before relying on it. The runtime prints a structured result
(a ship/no-ship summary plus findings with file, line range, confidence, and a
recommendation).

**Treat empty or missing stdout as a failed reviewer, not a pass.** If the call
returns nothing, note Codex as absent (as in the fallback) — do not fold a blank
result into the panel as if it approved.

When it does return, normalize its output to the same compact shape the Fable 5
reviewers use and fold it into the consolidated findings:

- **Verdict:** PASS | FAIL
- **Issues:** `file:line · severity · one-line description`

## Fallback: bare `codex exec`

If the plugin companion is absent but the `codex` CLI exists, run a headless review
with an inline adversarial prompt. **Replace `<base-ref>` with the resolved `$BASE`
before sending** (the single-quoted heredoc does not expand it). Verify flags with
`codex exec --help` — they vary by version.

```bash
codex exec --cd "[repo dir]" "$(cat <<PROMPT
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
PROMPT
)"
```

Either way, the controller never reads the diff itself — only the compact verdict
returns to you.
