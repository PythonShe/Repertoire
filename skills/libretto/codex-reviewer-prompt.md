# Codex Reviewer (cross-model spec review)

The cross-model panel reviewer. A different model sees different things — it
catches the ambiguity and the unstated assumption a room full of Claudes will read
right past. It does a **general** pass over the whole spec, so it complements the
single-lens Opus reviewers rather than repeating one of them.

## Use the adversarial-review runtime, not codex-rescue

The right tool is Codex's **adversarial-review runtime**, purpose-built to break
confidence in an artifact: a skeptical, evidence-grounded review that returns
structured findings.

Do **not** use the `codex-rescue` subagent here. It is a write-capable forwarder
to Codex's `task` runner and refuses to run reviews — it would try to *implement*
the spec instead of reviewing it. Wrong job entirely.

## Availability check

Look for the Codex plugin's companion script first, then the bare CLI:

```bash
COMPANION=$(ls -t ~/.claude/plugins/cache/*/codex/*/scripts/codex-companion.mjs 2>/dev/null | head -1)
[ -n "$COMPANION" ] && echo "plugin: $COMPANION"
command -v codex >/dev/null && echo "cli: $(command -v codex)"
```

If neither prints, treat Codex as **unavailable**: run the Opus lenses only and
note the omission in your report. That is the documented fallback, not a silent
drop.

## Preferred: the adversarial-review runtime

Pass flags as **separate shell arguments** (don't cram them into one quoted
string — they'd be read as positional text and ignored). Run this in the **same
batch** as the Opus reviewer dispatches so they all run together.

**Substitute `[SPEC_FILE_PATH]` and the `[one line]` summary with real values
before sending** — the runtime receives the prompt as literal text, so an
unsubstituted placeholder makes it review nothing. Keep the spec's full path in
the prompt so the runtime knows which file to read.

```bash
node "$COMPANION" adversarial-review --wait "review the design spec at [SPEC_FILE_PATH] for gaps, contradictions, ambiguity, and scope creep that would mislead an implementer: [one line on what the spec should deliver]"
```

Keep `--wait` — without it the call can return immediately with empty stdout,
which you'd then misread as "Codex absent" and drop the reviewer for the wrong
reason. The exact argument signature varies by companion version — if unsure,
confirm it on a tiny scope once before relying on it. The runtime prints a
structured result (a summary plus findings).

**Treat empty or missing stdout as a failed reviewer, not a pass.** If the call
returns nothing, note Codex as absent — do not fold a blank result into the panel
as if it approved.

When it does return, normalize its output to the same compact shape the Opus
reviewers use and fold it into the consolidated findings:

- **Verdict:** PASS | FAIL
- **Issues:** `[Section] · severity · one-line gap`

## Fallback: bare `codex exec`

If the plugin companion is absent but the `codex` CLI exists, run a headless
review with an inline adversarial prompt. **Replace `[SPEC_FILE_PATH]` and the
repo dir before sending** (the single-quoted heredoc does not expand variables).
Verify flags with `codex exec --help` — they vary by version.

```bash
codex exec --cd "[repo dir]" "$(cat <<'PROMPT'
You are an adversarial, cross-model reviewer of a design spec. Break confidence in
it. Read the spec file in full, then assume it is flawed until it proves otherwise.
Hunt for the expensive failures: requirements vague enough to build the wrong
thing, sections that contradict each other, undefined interfaces or data shapes,
unstated assumptions, missing error/edge-case handling, and scope that should have
been cut or decomposed. Report only material gaps; one strong finding beats five
weak ones.

Spec file: [SPEC_FILE_PATH]
This spec is meant to deliver:
[PASTE one paragraph: the idea and the agreed approach]

Output ONLY (same shape as the other reviewers):
- Verdict: PASS | FAIL
- Issues: [Section] · severity · one-line gap and why it matters
No rewrites, no restating the spec.
PROMPT
)"
```

Either way, the author folds only the compact verdict into the consolidated
findings — then revises the spec and commits.
