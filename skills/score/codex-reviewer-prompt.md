# Codex Reviewer (cross-model plan review)

The cross-model panel reviewer. A different model sees different things — it
catches the underspecified task and the unstated dependency a room full of
Claudes will read right past. It does a **general** pass over the whole plan,
so it complements the single-lens Fable 5 reviewers rather than repeating one of
them.

## Use the adversarial-review runtime, not codex-rescue

The right tool is Codex's **adversarial-review runtime**, purpose-built to
break confidence in an artifact: a skeptical, evidence-grounded review that
returns structured findings.

Do **not** use the `codex-rescue` subagent here. It is a write-capable
forwarder to Codex's `task` runner and refuses to run reviews — it would try
to *implement* the plan instead of reviewing it. Wrong job entirely.

## Availability check

Look for the Codex plugin's companion script first, then the bare CLI:

```bash
COMPANION=$(ls -t ~/.claude/plugins/cache/*/codex/*/scripts/codex-companion.mjs 2>/dev/null | head -1)
[ -n "$COMPANION" ] && echo "plugin: $COMPANION"
command -v codex >/dev/null && echo "cli: $(command -v codex)"
```

If neither prints, treat Codex as **unavailable**: run the Fable 5 lenses only and
note the omission in your report. That is the documented fallback, not a silent
drop.

## Preferred: the adversarial-review runtime

Pass flags as **separate shell arguments** (don't cram them into one quoted
string — they'd be read as positional text and ignored). Run this in the **same
batch** as the Fable 5 reviewer dispatches so they all run together.

**Substitute `[PLAN_FILE_PATH]`, `[SPEC_FILE_PATH]`, and the `[one line]`
summary with real values before sending** — the runtime receives the prompt as
literal text, so an unsubstituted placeholder makes it review nothing. Keep
both full paths in the prompt so the runtime knows which files to read. If no
written spec exists (the clarify-pass branch), drop the "against the spec at
…" clause and fold the clarified requirements into the `[one line]` summary
instead.

```bash
node "$COMPANION" adversarial-review --wait "review the implementation plan at [PLAN_FILE_PATH] against the spec at [SPEC_FILE_PATH] for gaps that would derail a fresh implementer: spec requirements with no task, undefined interfaces or placeholder steps, tasks out of dependency order, and scope the spec never asked for: [one line on what the plan should deliver]"
```

Keep `--wait` — without it the call can return immediately with empty stdout,
which you'd then misread as "Codex absent" and drop the reviewer for the wrong
reason. The exact argument signature varies by companion version — if unsure,
confirm it on a tiny scope once before relying on it. The runtime prints a
structured result (a summary plus findings).

**Treat empty or missing stdout as a failed reviewer, not a pass.** If the call
returns nothing, note Codex as absent — do not fold a blank result into the
panel as if it approved.

When it does return, normalize its output to the same compact shape the Fable 5
reviewers use and fold it into the consolidated findings:

- **Verdict:** PASS | FAIL
- **Issues:** `[Movement N, Task N.M] · severity · one-line gap`

## Fallback: bare `codex exec`

If the plugin companion is absent but the `codex` CLI exists, run a headless
review with an inline adversarial prompt. **Replace `[PLAN_FILE_PATH]`,
`[SPEC_FILE_PATH]`, the `[PASTE one paragraph …]` block, and the repo dir
before sending** (the single-quoted heredoc does not expand variables). With
no written spec, set the spec line to `Spec file: none — requirements follow`
and put the clarified requirements in the paragraph block. Verify flags with
`codex exec --help` — they vary by version.

```bash
codex exec --cd "[repo dir]" "$(cat <<'PROMPT'
You are an adversarial, cross-model reviewer of an implementation plan. Break
confidence in it. Read the plan and its spec in full, then assume the plan is
flawed until it proves otherwise. It will be executed by fresh implementers who
each receive one task group and zero conversation context. Hunt for the
expensive failures: spec requirements no task implements, tasks that reference
types or interfaces defined nowhere, placeholder steps ("add appropriate error
handling", "similar to task N"), tests too vague to write, tasks out of
dependency order, and scope the spec never asked for. Report only material
gaps; one strong finding beats five weak ones.

Plan file: [PLAN_FILE_PATH]
Spec file: [SPEC_FILE_PATH]
This plan is meant to deliver:
[PASTE one paragraph: the goal and the chosen approach]

Output ONLY (same shape as the other reviewers):
- Verdict: PASS | FAIL
- Issues: [Movement N, Task N.M] · severity · one-line gap and why it matters
No rewrites, no restating the plan.
PROMPT
)"
```

Either way, the author folds only the compact verdict into the consolidated
findings — then revises the plan and commits.
