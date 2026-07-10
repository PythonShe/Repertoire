# Codex Reviewer — shared invocation contract

Every skill that seats a cross-model Codex reviewer bundles a thin
`codex-reviewer-prompt.md` stub that composes with this file: this core
carries the invocation mechanics — how to find Codex, call it, and handle
what comes back — and the stub carries what to review, the review-prompt
text, and the timing rule. The conductor reads both and assembles one brief.
A different model sees different things — it catches what a room full of
Claudes will agree to overlook.

## Use the adversarial-review runtime, not codex-rescue

The right tool is Codex's **adversarial-review runtime**, purpose-built to
"break confidence" in the artifact under review: it runs a skeptical,
evidence-grounded review and returns structured findings.

Do **not** use the `codex-rescue` subagent here. It is a write-capable
forwarder to Codex's `task` runner and explicitly refuses to run reviews — it
would try to *implement* changes instead of reviewing them. Wrong job entirely.

## Availability check

Look for the Codex plugin's companion script first, then the bare CLI:

```bash
COMPANION=$(ls -t ~/.claude/plugins/cache/*/codex/*/scripts/codex-companion.mjs 2>/dev/null | head -1)
[ -n "$COMPANION" ] && echo "plugin: $COMPANION"
command -v codex >/dev/null && echo "cli: $(command -v codex)"
```

If neither prints, treat Codex as **unavailable**: proceed without it — the
stub says what remains (the Opus reviewers alone, or the QC verdict alone) —
and note the omission in your report. That is the documented fallback, not a
silent drop; never silently drop a reviewer.

## Preferred: the adversarial-review runtime

```bash
node "$COMPANION" adversarial-review --wait [--base "$BASE"] "[the stub's runtime review prompt]"
```

- Pass flags as **separate shell arguments** — do not cram `--wait`/`--base`
  into one quoted string; they would be read as positional text and ignored.
- Keep `--wait` — without it the call can return immediately with empty
  stdout, which you would then misread as "Codex absent" and drop the
  reviewer for the wrong reason.
- Include `--base "$BASE"` only when the stub reviews a git range (the stub
  says which ref `$BASE` is); document reviews (a spec or plan) pass no
  `--base`.
- **Substitute every placeholder with real values before sending** — the
  runtime receives the prompt as literal text, so an unsubstituted
  placeholder makes it review nothing. When the object under review is a
  file, keep its full path in the prompt so the runtime knows what to read.
- The exact argument signature can vary by companion version — if unsure,
  confirm it on a tiny scope once before relying on it.

The runtime prints a structured result (a ship/no-ship summary plus findings
with location, confidence, and a recommendation).

**Treat empty or missing stdout as a failed reviewer, not a pass.** If the
call returns nothing, note Codex as absent (as in the fallback) — do not fold
a blank result into the panel or the verdict as if it approved.

When it does return, normalize its output to the same compact shape the other
reviewers use and fold it into the consolidated findings:

- **Verdict:** PASS | FAIL
- **Issues:** `location · severity · one-line description` — the stub defines
  the location format (`file:line`, `[Section]`, `[Movement N, Task N.M]`, …).

## Fallback: bare `codex exec`

If the plugin companion is absent but the `codex` CLI exists, run a headless
review with the stub's inline adversarial prompt:

```bash
codex exec --cd "[repo dir]" "$(cat <<'PROMPT'
[the stub's fallback review prompt]
PROMPT
)"
```

- **Replace every placeholder — `${BASE}`, file paths, paste blocks, and the
  repo dir — before sending**: the single-quoted heredoc does not expand
  variables.
- Verify flags with `codex exec --help` — they vary by version.

Either way, the conductor never reads the diff or the artifact itself — only
the compact verdict returns to it.
