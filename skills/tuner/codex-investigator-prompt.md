# Codex Investigator (the cross-model rival, xhigh effort)

The opening dispatch of every Tuner run. A different model hunts differently — it
catches what a Claude will happily walk past, and at xhigh reasoning effort it
digs deepest and runs longest. That is why it launches **first**, in the same
batch as the scout, before any Claude-side analysis exists: it must never wait on
anything, and it must never be contaminated by the scout's ranking or the Opus
investigator's theory.

## Why codex-rescue IS the right tool here (unlike reviews)

Maestro and Coda must avoid the `codex-rescue` subagent for reviews — it is a
write-capable forwarder to Codex's `task` runner that refuses review work. An
investigation is the opposite case: *diagnosis* is exactly what the `task`
runtime is for, `--effort xhigh` is honored as an explicit request, and asking
for diagnosis-without-edits is what keeps the run read-only (the forwarder only
adds `--write` when the request wants changes).

## Preferred: the `codex:codex-rescue` subagent

If your environment lists the `codex:codex-rescue` agent type, dispatch it **in
the background** (so the scout shares its batch and you keep conducting). The
flag tokens at the top are routing controls: the forwarder strips them from the
task text and honors them as execution control. `--effort xhigh` pins reasoning
effort. `--wait` matters because the forwarder otherwise prefers `--background`
for an open-ended investigation like this and would hand back only a job ID —
`--wait` forces a foreground companion call so the rescue agent's final message
contains Codex's full report. The parallelism comes from the Agent tool's
background dispatch, never from these flags.

```
Agent tool (subagent_type: codex:codex-rescue, run in background):
  description: "Codex investigates [bug one-liner]"
  prompt: |
    --effort xhigh --wait
    Diagnosis only — read-only, do not add --write. Do NOT modify any files and
    do NOT apply fixes; this is an investigation without edits.

    Find the root cause of this bug: the mechanism, not the vicinity. A rival
    investigation is running in parallel and your report will be cross-examined
    against it, so back every claim with evidence from the code or from running
    the reproduction.

    ## Bug brief
    [PASTE the full bug brief — symptom, expected, repro, history, environment]

    Work from: [directory]   Branch: [branch]
    Build: [BUILD]   Test: [TEST]

    Reproduce first if a repro exists; construct one if it does not. Trace
    backward from the symptom to the first place reality diverges from
    expectation. Stop when you can state the defect, the chain from defect to
    symptom, and why fixing it kills the symptom.

    Report back exactly this shape:
    - ROOT CAUSE: file:line + the mechanism in 2-4 sentences
    - EVIDENCE CHAIN: the observations that prove it, one line each
    - REPRO: exact command + current failing output, one line
    - PROPOSED MINIMAL FIX: described in a line or two, not applied
    - CONFIDENCE: HIGH | MEDIUM | LOW, and the one thing that would change
      your mind
    If you cannot establish a mechanism, say NO_ROOT_CAUSE instead, and report
    what you ruled out with the evidence.
```

## Rebuttal round (Phase 2, on disagreement)

Run the rebuttal as a **fresh dispatch** with Codex's own earlier report pasted
back alongside the rival's. Do **not** use `--resume`: the runtime resumes the
*newest* task in this Claude session by recency, so any other Codex work run in
between would silently land the rebuttal on the wrong session. Pasting the
context is deterministic; losing the session memory is the cheaper risk.

```
Agent tool (subagent_type: codex:codex-rescue, run in background):
  description: "Codex rebuttal — [bug one-liner]"
  prompt: |
    --effort xhigh --wait
    Diagnosis only — read-only, do not add --write, no edits.

    You investigated this bug earlier and concluded:

    [PASTE Codex's original report in full]

    A rival investigation of the same bug disagrees. Its conclusion:

    [PASTE the rival's root cause + evidence chain]

    Refute or concede. Check the rival's evidence against the code, re-test
    your own mechanism, and return the same report shape as before with your
    final position. Conceding to better evidence is a win, not a loss.
```

## Fallback: bare `codex exec`

If the rescue subagent is unavailable, check for the CLI:

```bash
command -v codex >/dev/null && echo "cli: $(command -v codex)"
```

If it exists, run the investigation headless, in a background Bash call.
Verify flags with `codex exec --help` first — they vary by version; the
reasoning-effort override is commonly the config form shown here.

```bash
codex exec --cd "[repo dir]" --sandbox read-only -c model_reasoning_effort="xhigh" "$(cat <<'PROMPT'
You are a root-cause investigator. Find the mechanism of this bug, not the
vicinity. A rival investigation runs in parallel and your report will be
cross-examined, so back every claim with evidence. Do not modify any files.

## Bug brief
[PASTE the full bug brief]

Reproduce first; trace backward from the symptom to the first divergence from
expectation. Report ONLY:
- ROOT CAUSE: file:line + mechanism in 2-4 sentences
- EVIDENCE CHAIN: one observation per line
- REPRO: exact command + failing output
- PROPOSED MINIMAL FIX: described, not applied
- CONFIDENCE: HIGH | MEDIUM | LOW + what would change your mind
PROMPT
)"
```

The rebuttal works the same way on this route — a second `codex exec` with
Codex's original report and the rival's both pasted in.

## The absence rule

If neither route exists, or the call errors, or stdout comes back **empty**: the
rival is absent. Absent is never agreement and never a pass. Proceed
single-investigator, skip the cross-examination (there is nothing to examine
against), record `single-investigator` in the final report's concordance line,
and let the reviewer + verifier gates carry the confidence burden alone.
