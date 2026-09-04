# Two-tier model policy — canonical source

This file is the canonical statement of the two-tier model policy; each
SKILL.md carries a marked copy (seat names vary by skill) that must not drift
from it. Sanctioned variance: libretto and score, whose only build seat is the
conductor itself, state the policy without the session-model guidance; presto
pins its **recon scouts** to Opus alongside its reviewer, because its scouts
weigh approaches rather than merely profiling a target and their
recommendation is the only investigation that run gets — and legato pins its
**scout** to Opus for the same reason: its audit is the only investigation
its run gets. Three skills seat no Codex agent: presto and legato because the
cross-model pass earns its wall-clock over a whole branch rather than over
one scoped change (presto's one scoped build, legato's one scoped motion
polish), and jam because its run is unattended — a cross-model call that
hangs has no user at the wheel to notice, so jam buys the diversity
cross-tier instead, pairing a finale reviewer pinned to Opus with a second
finale reviewer unpinned on the session model. Jam also pins its three scouts
and its selector to Opus alongside its reviewers: in a run with no user
gates, those seats stand in for the user's judgment.

- **Play it on your best.** The build seats — the fixers and the QC gate —
  run unpinned and inherit your session model; the hunters, the read-only
  verifier, and the panel skeptics stay pinned to Opus for a stable
  adversarial baseline; the scout stays on Explore. Run these skills on the
  most capable model you have — `/model best` resolves to Fable 5 where you
  have access, otherwise Opus — never on Sonnet. The only non-Claude agent is
  the Codex reviewer, cross-model by design.

# Codex fallback hygiene — canonical source

`shared/codex-reviewer-core.md` ("Fallback: bare `codex exec`") is the one
shape every bare-CLI Codex call takes, reviewer or investigator: prompt
written to a scratchpad file, `-o` verdict file, `-s read-only --ephemeral`,
**`< /dev/null` on the call**, run under the Bash tool's ~10-minute
`timeout`, foreground inside a background subagent — never in the
conductor's own Bash call, because a hung `codex exec` blocks whoever ran it
until a timeout or a human intervenes, and never relaunched: the shape must
be right on the first try. The redirect exists
because `codex exec` drains stdin to EOF whenever stdin is not a TTY — prompt
argument or not — and a subagent or background shell hands it a pipe nobody
closes (openai/codex #20919, open as of 2026-09). An empty output file is a
failed seat, never a pass; `Reading additional input from stdin` in the run
log means the redirect was lost, and the seat is recorded absent. Tuner's `codex-investigator-prompt.md`
carries a marked copy of the same shape and must not drift from it.
