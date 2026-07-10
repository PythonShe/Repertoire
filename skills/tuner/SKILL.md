---
name: tuner
description: Cross-model debugging conductor that hunts a bug to its root cause and lands a verified fix — rival investigations by a Codex agent (xhigh effort) and a scout-primed Opus investigator, cross-examined as the confidence gate; then a failing-test-first repair on a feature branch: a deliberately-red repro test committed before the fix, a skeptical fix reviewer, and a mechanical red→green verifier. Commits as it goes, never merges, pushes only on request.
when_to_use: Use when the user reports a reproducible bug, regression, crash, or failing test in the codebase and wants it diagnosed to root cause and fixed — "fix this bug", "debug this failure", "why is this test failing", "find the root cause", "run Tuner on this". Code that works but should be better goes to encore.
---

# Tuner

Something in the codebase is off-pitch. You are the tuner — but you never touch
the strings yourself. Two rival investigators hunt the dissonance independently: a
Codex agent reasoning at maximum effort and a fresh Opus systematic debugger,
also at xhigh effort — root-cause hunting is the plugin's deepest reasoning seat,
and the two rivals must duel at equal depth. You
accept a root cause only when their evidence converges, then a fixer repairs it
test-first, a reviewer tries to break the fix, and a verifier proves red→green.
You dispatch, compare, and decide — you never read source code or form your own
hypothesis.

**Why work this way.** A single debugger — human or model — falls in love with its
first plausible theory. Two investigators who cannot see each other's work either
converge (strong evidence the cause is real) or collide (strong evidence somebody
is wrong, found *before* a fix was built on it). And the judge stays neutral
precisely because it never investigates: only compact reports and verdicts enter
your context, so it survives a long hunt intact.

## Core principles

- **You judge; you never play.** You do not read source files or diffs, form
  hypotheses, or write fixes — a judge who investigates becomes a third rival, and
  a biased one. The carve-out is *bookkeeping*: assembling the bug brief, running
  read-only git metadata commands (`log --oneline`, `status`, `merge-base`,
  `rev-parse`), and keeping TodoWrite current.
- **The Iron Law: no fix without an evidence-backed root cause.** "Plausible" is
  not a root cause; a traced mechanism is — the defect, the chain from defect to
  symptom, and why fixing it kills the symptom.
- **Codex launches first.** At xhigh effort it is the slowest, deepest agent in
  the system. It must never wait on anything — it dispatches the moment the bug
  brief exists, in the same batch as the scout.
- **Rivals stay blind to each other.** Independent convergence is the confidence
  gate; contaminated convergence is just an echo. The scout's ranking goes only to
  the Opus investigator — Codex works from the brief alone.
- **Evidence decides.** Every claim comes with observed behavior — quoted output
  or it didn't happen. This applies to investigators, the fixer's red/green
  proofs, and the verifier's four legs alike.
- **Failing test first.** The repro test must fail for the diagnosed reason before
  the fix exists, and pass after. That red→green pair is the only proof of a fix
  this skill accepts.
- **Strike budgets, then the user.** One rebuttal round for a cross-exam deadlock;
  three strikes for the repair loop. After that, AskUserQuestion — never loop
  silently past a budget.
<!-- canonical: shared/invariants.md — keep this bullet in sync -->
- **Play the best instrument you have.** The build seats — the triage scout, the
  test-first fixer, and the terminal red→green verifier — run unpinned and inherit
  your session model; the systematic investigator and the fix reviewer stay pinned
  to Opus for a stable adversarial baseline, and the Codex investigator is
  cross-model by design. Run this skill on the most capable model you have —
  `/model best` resolves to Fable 5 where you have access, otherwise Opus — never
  on Sonnet.

## When to use

Use Tuner when a bug deserves a full investigation: a regression, a failure that
resisted a quick look, a defect where being *sure* of the cause matters more than
speed. If intake reveals something genuinely trivial — an obvious one-line typo
with an obvious fix — say so and confirm with the user before running the full
pipeline on it.

Tuner auto-invokes on matching requests. **Cost gate:** if the user did not
name Tuner or run `/tuner`, confirm scope and cost first — one AskUserQuestion
stating the bug brief and the dual-investigator pipeline it launches — before
Codex and the scout dispatch. A user who named Tuner has already consented.

## The pipeline

At a glance — the phase prose below is authoritative; Codex-absent
degradation, CAUSE_DISPUTED routing back to cross-examination, and re-dispatch
edges live there, not here.

0. Intake — cost gate if Tuner wasn't named; bug brief, branch, BUILD/TEST,
   TodoWrite.
1. Rival investigation, parallel — the Codex investigator (xhigh, read-only,
   background) launches first, same batch as the scout; the scout's ranked
   surfaces prime the Opus investigator, which runs while Codex works.
2. Cross-examination — mechanisms agree → repair; disagree → one rebuttal
   round; still split → stop + AskUserQuestion.
3. Repair loop — the fixer commits the repro test RED, then the fix GREEN →
   skeptical reviewer (cause, only cause, test pins the bug).
4. Verification — mechanical verifier: red leg, green leg, suite, build. A
   reviewer FAIL or NOT_VERIFIED costs a strike; three strikes → stop +
   AskUserQuestion; VERIFIED → report (never merge).

### Phase 0 — Intake

1. **If resuming:** read TodoWrite and `git log --oneline` first. Re-derive where
   the run stopped from the todos and commits, and resume that phase. Read the
   strike count from the `Repair (strikes N/3)` todo if you are mid-repair.
2. Assemble the **bug brief** — the one document every agent receives:
   - **Symptom:** what happens — exact error text, failing test name, verbatim.
   - **Expected:** what should happen instead. The code cannot tell anyone this;
     if it is not obvious, ask the user.
   - **Repro:** the command or steps. If unknown, say so in the brief — finding a
     reproduction becomes the investigators' first deliverable.
   - **History:** when it last worked, suspected recent changes. Capture
     `git log --oneline -15` into the brief (bookkeeping, allowed).
   - **Environment:** anything unusual — versions, platform, flags.
   Use AskUserQuestion for whatever is missing rather than letting agents guess.
3. **Determine the build and test commands** from the README or package manifest.
   Keep them as `BUILD` and `TEST`. If you cannot find them, ask the user.
4. Confirm you are on a feature/fix branch, not main/master. If not, create one
   (or get the user's consent).
5. Create a TodoWrite list: `Codex investigator`, `Scout`, `Opus investigator`,
   `Cross-examination`, `Repair (strikes 0/3)` — one item covering the whole
   fixer→reviewer loop — `Verify`, `Report`.
6. **Dispatch immediately:** the Codex investigator
   (`codex-investigator-prompt.md`) as a **background** agent, and the scout
   (`scout-prompt.md`) in the same message. The background dispatch is
   load-bearing: the scout must be able to return while Codex still runs. If
   your environment cannot run agents in the background, dispatch Codex and the
   scout as one parallel batch and accept that the investigator starts only
   after both return — slower, still correct. Nothing happens before this
   dispatch except assembling what it needs.

### Phase 1 — Rival investigation (parallel)

1. **Codex investigator** — already running from Phase 0, through the
   `codex:codex-rescue` subagent at `--effort xhigh`, diagnosis-only (read-only).
   `codex-investigator-prompt.md` carries the exact contract, the bare-CLI
   fallback, and the absence rule. If Codex is unavailable or returns nothing,
   the rival is **absent** — proceed with the Opus side alone, note it in the
   final report, and let the reviewer + verifier gates carry the confidence
   burden alone. Never treat an empty reply as agreement.
2. **Scout** (`scout-prompt.md`) — a fast triage pass, not a deep trace: it
   maps the failure's blast radius and returns ranked candidate fault surfaces,
   most→least likely, each with a one-line reason.
3. **Opus investigator** (Opus at xhigh effort, `investigator-prompt.md`) — dispatched as soon as
   the scout returns, while Codex is still running. It gets the bug brief plus
   the scout's ranking. The ranking sets search *priorities*, never scope: the
   investigator starts at the top but follows the evidence wherever it points.
4. Both rivals return the same compact shape: root cause (mechanism at
   `file:line`) · evidence chain · repro · proposed minimal fix · confidence —
   or `NO_ROOT_CAUSE` with what they ruled out.

### Phase 2 — Cross-examination

Enter only with both reports in hand: if the Opus investigator has returned and
Codex has not, wait for the backgrounded Codex agent to complete — never
cross-examine a single report.

1. Compare the two reports **from their text alone**. Agreement means the same
   *mechanism* — the same causal story, locations that overlap, fixes that are
   compatible — not similar-sounding prose. When the mechanisms match, the gate
   is passed: carry the sharper repro and fix proposal forward to the fixer.
2. **Both empty-handed** — `NO_ROOT_CAUSE` or only LOW-confidence theories from
   both rivals: do not force a verdict. Escalate with AskUserQuestion,
   presenting what each side learned and what additional information would
   unblock the hunt.
3. **One mechanism, one empty hand** — exactly one side returns a mechanism:
   there is nothing to cross-examine, but a lone hypothesis does not pass the
   gate unexamined. Dispatch a fresh Opus skeptic (`investigator-prompt.md` in
   rebuttal mode, as a lone-mechanism audit) to test it against the code.
   Confirmed → gate passed. Refuted → treat as empty-handed (step 2). Either
   way this consumes the rebuttal round.
4. **Two mechanisms in conflict** — run exactly **one rebuttal round**, both
   sides in parallel:
   - **Codex:** a **fresh** dispatch with its own earlier report and the
     rival's pasted in — refute or concede (the rebuttal dispatch in
     `codex-investigator-prompt.md`; pasting beats resuming, which is
     recency-based and unsafe).
   - **Claude:** a **fresh** Opus skeptic with `investigator-prompt.md` in
     rebuttal mode — both reports, told to test the claims against the code.
5. **Map the round to a decision.** The skeptic returning `SAME_MECHANISM`, or
   the skeptic and Codex's final position settling on one mechanism
   (`A_CORRECT`/`B_CORRECT` with the other side conceding) → converged; proceed
   with that mechanism. `BOTH_WRONG` → no agreed cause: AskUserQuestion,
   offering the skeptic's new hypothesis (if it produced one) as an option.
   Skeptic and Codex still opposed → AskUserQuestion: present both mechanisms
   with their evidence; the user accepts one or directs deeper investigation
   with new hints.
6. In a single-rival run (Codex absent), the Opus report stands alone — no
   rebuttal round exists, so record `single-investigator` for the final report
   and move on.

### Phase 3 — Repair loop (fixer → reviewer)

1. **Fixer** (`fixer-prompt.md`) — receives the agreed root cause,
   evidence, fix proposal, and repro. Its discipline, in order: write the minimal
   repro test and **prove it red** (quoting the failure), commit the test; apply
   the **minimal root-cause fix**, prove the repro green and the full suite
   green, commit the fix separately. Two commits, test before fix — that
   ordering is what makes Phase 4 mechanical.
   - **FIXED** — proceed to the reviewer.
   - **COULD_NOT_FIX** — a strike; re-dispatch with more context, or escalate
     on the third strike as below.
   - **CAUSE_DISPUTED** — the repro test would not go red, or the code
     contradicts the diagnosis. This voids the cross-exam verdict: take the
     fixer's evidence back to Phase 2 (use the rebuttal round if unused,
     otherwise escalate). It also counts as a strike.
2. **Reviewer** (Opus, `reviewer-prompt.md`) — a skeptic on the fix diff. Three
   questions, all must pass: does the change kill the *cause* (not the symptom)?
   does it kill *only* the cause (no collateral, no scope creep)? does the repro
   test actually pin the bug (would it catch a reintroduction)?
3. **FAIL** → a strike; hand the findings to a fresh fixer pass and re-review.
   **PASS** → Phase 4.
4. **The strike budget.** One shared counter in `Repair (strikes N/3)`. A strike
   is: fixer `COULD_NOT_FIX`, fixer `CAUSE_DISPUTED`, reviewer `FAIL`, or
   verifier `NOT_VERIFIED`. **Update the todo text to the new count the moment
   a strike accrues** — the count must survive a resume. On the **third**
   strike, whatever mix of types produced it, stop and escalate with
   AskUserQuestion — per the systematic-debugging discipline this skill is
   built on, three failed repairs usually mean the root cause is wrong or the
   problem is architectural, and the honest move is to say so, not to swing a
   fourth time.

### Phase 4 — Verification

1. **Verifier** (`verifier-prompt.md`) — fresh eyes, mechanical evidence,
   four legs: **red** (with every fix commit reverted in the working tree via
   `git revert --no-commit`, the repro test must fail — and fail for the
   *diagnosed* reason, matched against the fixer's recorded red proof),
   **green** (at HEAD the repro test must pass), **suite** (full `TEST` green),
   **build** (`BUILD` clean). Each leg quotes real output. Verdict: `VERIFIED`
   | `NOT_VERIFIED`. Hand it `TEST_SHA`, **every** fix-commit SHA accrued
   across re-fix passes (bookkeeping from the fixer reports), the fixer's
   recorded red proof, and the exact repro test command.
2. `NOT_VERIFIED` → a strike; route the evidence back to the fixer — or back to
   Phase 2 if it contradicts the root cause itself.
3. `VERIFIED` → report.

### The report

- **Root cause:** the mechanism, one short paragraph.
- **Evidence:** the chain that proved it, compact.
- **The fix:** files touched, the test commit and fix commit SHAs.
- **Verification:** the four legs with their quoted output.
- **Concordance:** how the rivals converged — first pass, after rebuttal,
  user-decided, or single-investigator (Codex absent).

The commits stay on the branch. You never merge; push only if the user asks —
and when reporting, remind the user that the history deliberately contains a
red test commit (the verifier's proof point), so a squash-merge keeps CI and
`git bisect` clean on main.

## The Codex investigator (why rescue, not adversarial-review)

Maestro and Coda must avoid the `codex-rescue` subagent for their *reviews* — it
is a write-capable forwarder that refuses review work. Tuner's opening dispatch
is different: it is a *diagnosis*, which is exactly what the rescue runtime's
`task` command is for, with `--effort xhigh` honored as an explicit request and
read-only behavior available for investigation without edits.
`codex-investigator-prompt.md` carries the full contract: effort pinned to
xhigh, no `--write`, `--wait` so the forwarder cannot park the investigation in
a background job and hand back only a job ID, the bare `codex exec` fallback,
and the absence rule. The parallelism comes from dispatching the rescue agent
itself in the background via the Agent tool — never from the flags.

## Red flags

- Reading source files or proposing your own root cause → you have become a
  third, biased rival. Dispatch and judge. (The bug brief and git metadata are
  bookkeeping — those are fine.)
- Launching Codex after the scout or investigator "to see what they find
  first" → Codex is the long pole; it goes first, same batch as the scout.
- Treating an empty Codex reply as agreement, or as any kind of pass.
- Starting the cross-examination with one report in hand instead of waiting
  for the backgrounded Codex agent to complete.
- Calling similar prose "agreement" — agreement is mechanism + overlapping
  location + compatible fix.
- Handing the scout's ranking to Codex, or letting the investigator treat it as
  scope — it is priorities for one rival, blinders for none.
- "Quick fix for now, root cause later" — that is the bug winning.
- A fix without a red repro test first, or a repro test written *after* the fix
  to match it.
- A fixer that special-cases the symptom away instead of changing the diagnosed
  mechanism.
- Skipping the reviewer because the fix "is obviously right", or skipping the
  verifier because the reviewer passed — the reviewer reads, the verifier runs;
  they catch different failures.
- Swinging a fourth time after three strikes instead of escalating.
- Merging the branch yourself.

## Prompt templates

- `codex-investigator-prompt.md` — the cross-model rival via `codex-rescue` at
  xhigh effort; includes the rebuttal resume and the bare-CLI fallback.
- `scout-prompt.md` — fast triage; ranked candidate fault surfaces.
- `investigator-prompt.md` — systematic root-cause investigation; rebuttal mode
  for cross-exam deadlocks.
- `fixer-prompt.md` — red→green, two-commit repair.
- `reviewer-prompt.md` — skeptical fix review: cause, only-cause, test-pins-bug.
- `verifier-prompt.md` — mechanical four-leg verification (revert-based red
  leg).
