---
name: presto
description: Fast-lane implementation session for one scoped change — 1-2 read-only scouts investigate the codebase and weigh approaches, the user picks at a single AskUserQuestion gate, then the work is built (by the controller itself when it is small, by one implementer subagent when it is not), reviewed by one Opus subagent that is never skipped, fixed in a single pass, and sealed by a short build-and-test QC gate. Runs on a named or fresh branch, commits as it goes, pushes only on request, never merges.
when_to_use: Use when the user wants one concrete change built right now, with no spec or plan written first — "add a --json flag to the export command", "wire this endpoint to the new service", "make the sidebar collapsible", "just build it", "run Presto on this". A written plan already exists → maestro; a bug to diagnose → tuner; improving working code nobody asked to change → encore; a change that needs designing or planning before anyone builds it → libretto, then score.
---

# Presto

Not every piece needs the full orchestra. A scoped change — one the user can
state in a sentence and you can finish in a sitting — does not want a spec, a
plan, and a ten-seat pipeline in front of it. It wants recon, a decision, the
work, and a check. You run that: fast, but never careless.

**Why work this way.** Presto's speed comes from **fewer seats, not shallower
thinking**. Every agent it does seat reasons at full effort; there are simply
three to six of them instead of a dozen, and the controller is allowed to pick
up an instrument when the handoff would cost more than the work. What survives
the compression is the part that catches mistakes: nothing is built before the
user approves an approach, and nothing ships without a fresh reviewer and a
gate that actually runs the tests.

## Core principles

- **The conductor may play — inside the size gate.** This is Presto's one
  deliberate break with the rest of the line. When the change is small and the
  approach is settled, you implement it yourself: a handoff to a subagent that
  must first reconstruct context you already hold is pure latency. Outside the
  size gate (below), you dispatch — with one narrow extension at the repair
  step, where you may fix `minor` findings in place whoever wrote the code.
  Discovering mid-build that the work outgrew the gate is a
  **stop-and-hand-off**, not a reason to keep playing.
- **You never review your own code.** The reviewer is always a fresh subagent —
  most of all when you were the one who built it. An author reviewing their own
  work is precisely the failure this seat exists to catch, and it is the one
  thing Presto never shortcuts.
- **One gate, before anything is written.** Recon reports, the user picks, then
  the work starts. A Presto run that has already edited a file before the user
  approved an approach has skipped its only safety rail.
- **One writer at a time.** One branch, one seat holding the pen — never two
  implementers, never a fixer alongside an implementer, never you editing
  alongside a subagent.
- **Everyone who changes code commits** — implementers, fixers, and you when
  you play. Commits are the unit of progress and the same-session resume trail.
- **Know when this is the wrong room.** Presto is a fast lane, not a shortcut
  past thinking. Work that needs a design decision the user hasn't made goes to
  `/repertoire:libretto`; work that needs a written plan goes to
  `/repertoire:score` and then `/repertoire:maestro`. Escalating at the gate is
  a successful Presto run, not a failed one.
- **No cross-model seat, by design.** Presto is the only skill in the line
  without a Codex reviewer. The cross-model pass is worth its wall-clock on a
  whole branch built by a fleet; on one scoped change it is the slowest thing
  in a pipeline whose entire premise is speed. When a change deserves that
  scrutiny, it deserved Maestro.
<!-- canonical: shared/invariants.md — keep this bullet in sync -->
- **Play it on your best.** The build seats — the implementer, the fixer, the
  QC gate, and you when you play — run unpinned and inherit your session model;
  the recon scouts and the reviewer stay pinned to Opus, the scouts because
  their approach recommendation is the only investigation the run gets, the
  reviewer for a stable adversarial baseline. Every seat runs at xhigh effort:
  the ensemble is small, so no seat in it is cheap. Run this skill on the most
  capable model you have — `/model best` resolves to Fable 5 where you have
  access, otherwise Opus — never on Sonnet.

## When to use

Use Presto when the user names a change they want built now and the path to it
is short enough that writing a plan would cost more than the work. It is the
door Maestro's own guidance points at for a single scoped change with no plan
behind it — except that "no plan" still deserves recon, a decision, a reviewer,
and a gate.

The floor and the ceiling both matter. Below the floor — a rename, a typo, a
one-line config edit with an obvious answer — just make the change and say so;
seating three agents around it is theatre. Above the ceiling — several modules,
a new subsystem, an unmade design decision — hand it to the production line at
the gate.

Presto auto-invokes on matching requests. **Cost gate:** if the user did not
name Presto or run `/presto`, confirm before the scouts dispatch — one
AskUserQuestion carrying the restated brief, the branch you intend to use, and
the fact that recon is about to run. Fold the branch decision into that same
question rather than asking twice; a misfire then costs one question, and the
repository has not been touched yet. A user who named Presto has already
consented to recon — ask only if the branch is ambiguous. Either way the
Phase 2 approval gate still follows: the cost gate is about spending, the
Phase 2 gate is about what gets built, and no run skips the second one.

## The pipeline

At a glance — the phase prose below is authoritative; the size gate's terms,
the stop-and-hand-off rule, who-fixes routing, and the strike budget live
there, not here.

0. Downbeat — restate the brief, scope check, decide BRANCH, find BUILD/TEST,
   cost gate if Presto wasn't named, *then* cut the branch and capture BASE,
   TodoWrite.
1. Recon — 1 read-only Opus scout, a 2nd only for a genuinely separate
   surface. Landing zone, approaches, size call.
2. The gate — report and one AskUserQuestion. Nothing is written before it
   clears; too big → escalate here.
3. Build — the size gate picks the seat: you, or one implementer subagent.
   Never both, never two.
4. Review — one Opus reviewer on BASE..HEAD, always a subagent, never skipped.
5. Fix — one pass. You fix what you built or what is trivial; a fixer subagent
   takes the rest. Clean PASS → skip.
6. QC — build + tests + acceptance: SHIPPABLE → report (push only on request,
   never merge); NOT_SHIPPABLE → route blockers → retry-mode re-run; second
   strike → stop + AskUserQuestion offering the escalation.

### Phase 0 — Downbeat

1. **If resuming:** read TodoWrite and `git log --oneline` first. There is no
   state file — the todos carry what the commits cannot: `BASE` in the
   `Review` todo, the build seat in the `Build` todo, and the strike count in
   the `QC` todo. Re-derive the rest from the commits and resume at the first
   phase not marked done. If `BASE` is genuinely lost, do not guess a range —
   ask the user which commit the branch started from.
2. **Restate the request as the brief** — one or two lines of what the user
   wants, plus **acceptance**: how anyone would know it works. Every agent in
   the run receives this verbatim, so it is worth getting exact. If the request
   is ambiguous in a way that changes what gets built, ask now.
3. **Scope check.** Decide, before spending anything, whether this room is the
   right one:
   - *Below the floor* — genuinely trivial with one obvious answer → just do
     it, tell the user you skipped the pipeline and why, and stop.
   - *Above the ceiling* — spans several modules, needs a new subsystem, or
     rests on a design decision the user has not made → say so now and point
     at `/repertoire:libretto` (needs design) or `/repertoire:score` (needs a
     plan). Do not run Presto on it.
   - *In the room* — proceed. If you are unsure, proceed to recon: the scout's
     size call settles it at the gate, which is a cheaper place to be wrong.
4. **Decide `BRANCH` — but do not cut it yet.** The user may name an existing
   branch or ask for a new one; absent an instruction, use the current branch
   when it is already a feature branch, otherwise plan to create
   `presto/<slug>` from a short kebab-case handle for the request. **Never
   build on main or master** without the user's explicit consent. Check
   `git status` now — uncommitted changes are the user's work; surface them and
   resolve before Phase 3, never touch them.
5. **Determine `BUILD` and `TEST`** from the README or package manifest. If you
   cannot find them, ask the user — never let an agent guess, and never let QC
   run on an invented command. If the project genuinely has none, record
   `none (user-confirmed)` and pass that token to QC, which then judges by its
   read and says so in its evidence.
6. **Cost gate** — if the user did not name Presto, one AskUserQuestion now:
   the brief, the branch you intend to use, and the fact that recon is about to
   run. Nothing in the repository has changed yet, and that is the point of
   asking here rather than after the checkout.
7. **Now cut the branch** — check out or create what step 4 settled and the
   gate confirmed — and **capture `BASE`** = `git rev-parse HEAD`. Every review
   in this run is `BASE..HEAD`.
8. Create a TodoWrite list: `Recon`, `Gate`, `Build`, `Review`, `Fix`,
   `QC (strikes 0/2)` — and **write `BASE` into the `Review` todo's text**.
   `BASE` is a plain `HEAD` snapshot, not a merge-base: on a branch that
   already carried commits nothing can recompute it later, so that todo is the
   only place it survives a resume.

### Phase 1 — Recon

1. **One scout** (`scout-prompt.md`, Opus, read-only) by default. It gets the
   brief and returns the landing zone with `file:line` anchors, the patterns
   and constraints the change must respect, **1-3 viable approaches with a
   recommendation** and their trade-offs, a **size call** (S / M / L with a
   rough file and line estimate), and any open question only the user can
   answer.
2. **A second scout in parallel — only for a genuinely separate surface**: an
   external one the codebase cannot answer (a library's API, a protocol, a
   version's behavior — this one may search the web and read docs), or a second
   unrelated part of the codebase the change must meet. Two is the ceiling. If
   the work needs a third, it needed Maestro; say so at the gate.
3. Scouts **read only** — they never edit, and never run builds or tests.
   Parallel test runs corrupt each other, and nothing in recon needs one.
4. If a scout returns `BLOCKED` or an empty landing zone, do not guess: take
   what it did find to the user at the gate and ask for the missing pointer.

### Phase 2 — The gate

1. **Report compactly** — what recon found, the approaches with your
   recommendation and its reasoning, the size call, what will change in 3-6
   bullets, which seat will build it, and the branch. Keep it short enough to
   read at a glance; the detail lives in the agents' briefs, not the user's
   screen.
2. **One AskUserQuestion.** The approaches are the options, recommended first.
   If recon surfaced questions only the user can answer, batch them into the
   same call (the tool takes up to four questions) rather than asking twice.
3. **When recon says this is bigger than Presto** — size L, or an unmade design
   decision — make the escalation an option here, naming the door
   (`/repertoire:libretto`, `/repertoire:score`). The user choosing it ends the
   run successfully.
4. **Nothing is written before this gate clears.** If the user adjusts the
   approach, fold their adjustment in and re-state it — start building on the
   answer, never on half of it.

### Phase 3 — Build

**The size gate — you play only when all of these hold:**

- recon returned a settled approach and left no open questions,
- the change is confined to roughly **≤3 files and ≤150 lines**,
- it lives in **one module**, and
- it introduces no new subsystem, dependency, schema, or public interface.

Fail any one of them and you dispatch. Record the seat in the `Build` todo's
text as `seat: controller` or `seat: implementer` — Phase 5's routing reads it,
and a resumed run has no other way to recover it.

1. **You play** — implement the approved approach yourself, follow the patterns
   recon named, write tests that verify real behavior, run `TEST` scoped to
   what you touched, and commit. You are building, not designing: what the user
   approved is what gets built.
2. **Or one implementer subagent** (`implementer-prompt.md`) — fresh, with the
   brief, the approved approach, recon's landing zone and patterns, `BRANCH`,
   and `TEST` all pasted in. It never reads recon's raw report and never hunts
   for the request. It commits its own work.
3. **Never two writers.** One implementer, dispatched alone; you do not edit
   while it runs.
4. **Stop-and-hand-off.** If you are playing and the work reveals itself to be
   past the size gate — a fourth file, a schema change, an interface you did
   not expect — stop, commit what is coherent, and hand the remainder to an
   implementer, filling that prompt's **Work already on the branch** section
   with your commit SHAs, what they cover, and what you learned that changed
   the picture. Without that section the implementer would rebuild what you
   already committed. Flip the `Build` todo to `seat: implementer` (Phase 5
   then routes the review findings to a fixer, not to you — the branch is no
   longer only your code). Sunk effort is not a reason to keep playing: the
   size gate is a promise about *this* change, not about how far you have
   already gotten.

### Phase 4 — Review

1. Dispatch **one Opus reviewer** (`reviewer-prompt.md`), scope `BASE..HEAD`.
   It receives the brief, the acceptance, and the approved approach — so it can
   catch the two failures a diff alone hides: code that solves the wrong
   problem, and code that built more than was asked.
2. **Always a subagent, always dispatched.** Not when you built it, not when
   the change is small, not when the implementer's report sounded confident.
   There is exactly one review in this pipeline; skipping it leaves none.
3. It returns `PASS` (no real issues) or `FAIL` with findings, each anchored at
   `file:line`.

### Phase 5 — Fix

1. **Clean `PASS` with no findings → skip this phase.** A fixer with an empty
   finding list is a wasted seat and a wasted minute.
2. **Who fixes** — the size gate governs who *builds*; repair has its own,
   narrower rule, and it routes on the severities the reviewer already
   assigned rather than on your own sense of convenience:
   - **You**, when the `Build` todo says `seat: controller` — you already hold
     the context of code you wrote, so briefing a subagent on it costs more
     than the repair — or, whoever built it, when **every finding is `minor`**:
     localized and mechanical, needing no grasp of the surrounding design (a
     missing null check, a rename, an unhandled case).
   - **A fixer subagent** (`fixer-prompt.md`) otherwise — that is, any
     `critical` or `important` finding against code an implementer wrote. A
     subagent's code is context you do not have, and reconstructing it in your
     own window is how a fast lane turns slow *and* wrong.
3. **One pass.** Do not review the fix, and do not loop. Anything the fixer
   disputes or could not fix goes into the QC brief verbatim — QC is the round
   that catches it, and it is one dispatch away.
4. Whoever fixes, commits.

### Phase 6 — QC gate

1. Dispatch the **QC agent** (`qc-prompt.md`) with `BASE..HEAD`, `BUILD`,
   `TEST`, the brief with its acceptance, and any unfixed or disputed findings
   from Phase 5. It runs the build and the tests, checks the acceptance is
   actually satisfied by the code rather than claimed, and scans for
   ship-blockers. Short means narrow, not lenient: it reads the diff against
   the brief and trusts build and tests for the rest — it does not re-derive
   the codebase.
2. **`SHIPPABLE`** → report. Push only if the user asks. **Never merge** — that
   decision is theirs.
3. **`NOT_SHIPPABLE`** → route each blocker by type — `[defect]` to a fixer
   (or to you, by the Phase 5 rule); `[implementation]`, which a fixer cannot
   localize, back to an implementer, or to the user when the approved approach
   itself is what is wrong. Then re-run QC, filling the prompt's retry-round
   section with the prior blockers and the fix SHAs since: the retry re-runs
   build and tests in full but close-reads only those fixes.
4. **Two strikes, then the user.** A round is one QC dispatch returning
   `NOT_SHIPPABLE`; update `QC (strikes N/2)` the moment one accrues. On the
   **second**, stop and escalate with AskUserQuestion — and make handing the
   work to `/repertoire:maestro` (or `/repertoire:tuner`, if what is failing is
   a bug rather than the build) one of the options. Two failed rounds in a fast
   lane is evidence the work was mis-sized; a third swing is Presto pretending
   to be Maestro. Escalate whether the run is interactive or autonomous.

### The report

- **What changed** and why, in a few lines.
- **Files and commit SHAs.**
- **Seats used** — how many agents the run actually spent, and whether you
  played or dispatched.
- **QC evidence** — the build result and the test summary, real output.
- **Left open** — anything unfixed, disputed, or deliberately out of scope.
- **The user's call** — the merge is never yours; say whether you pushed (only
  if asked) and what remains.

## The ensemble budget

Three agents on the floor — one scout, one reviewer, one QC. **Six on the
nominal path** — two scouts, an implementer, a reviewer, a fixer, and QC.

A QC retry round spends past six (a fixer, then another QC), and that is not a
budget violation: it is what the two-strike budget exists to bound. The six
counts the run as designed, not the run as rescued.

What the budget forbids is growing the ensemble **by design** — a third scout,
a second review round, a panel of lenses, a cross-model seat. Those are
Maestro's ensemble, and a run that wants one has outgrown the fast lane. The
honest move is to say so and name the door, not to quietly assemble an
orchestra.

## Handling subagent status

**Scout:** returns the landing zone, approaches, and size call → the gate. A
thin result is still a result — take it to the gate rather than re-dispatching
to fish. `BLOCKED` → surface it to the user at the gate with what it did find.

**Implementer:**
- **DONE** — proceed to the reviewer.
- **DONE_WITH_CONCERNS** — pass the concerns verbatim into the reviewer's
  brief; never let them evaporate.
- **NEEDS_CONTEXT** — you left something out. Supply it and re-dispatch; never
  re-dispatch unchanged.
- **BLOCKED** — supply more context and re-dispatch, or take it back to the
  user. If the approved approach is what is wrong, that is a gate decision, not
  an implementer's.

**Fixer:** `FIXED` → proceed. `PARTIALLY_FIXED` / `COULD_NOT_FIX` → carry the
remainder into the QC brief; do not re-loop Phase 5.

**Reviewer** reports `PASS` / `FAIL`; **QC** reports `SHIPPABLE` /
`NOT_SHIPPABLE` with typed blockers. Handle both as the phase steps describe.

## Red flags

- Editing a file before the Phase 2 gate cleared — the gate is the only
  approval in this pipeline.
- Building outside the size gate, or playing on past it once the work grew —
  the honest move is the hand-off. (Repairing a `minor` finding in place is
  the one sanctioned extension, and it is the *only* one.)
- Reviewing your own work, or skipping the reviewer because the change "is
  obviously right". There is one review here; skipping it leaves zero.
- Two writers on the branch at once, in any combination.
- Dispatching a fixer with an empty finding list, or dispatching one for a
  `minor` finding you could fix in place.
- Fixing a `critical` or `important` finding yourself on code an implementer
  wrote — that is the fixer's seat, and the severity is not yours to relabel.
- A third scout, a second review round, or a panel — that ensemble is
  Maestro's. Escalate instead of growing.
- Running Presto on work that needs a spec or a plan, instead of naming the
  door at the gate.
- Guessing `BUILD` or `TEST`, or letting QC run on an invented command.
- Building on main or master without explicit consent.
- Looping QC past two strikes instead of escalating.
- Merging the branch, or pushing without being asked.

## Bundled files

- `scout-prompt.md` — read-only Opus recon: landing zone, constraints,
  approaches with a recommendation, size call.
- `implementer-prompt.md` — build the approved approach in one pass and commit;
  used when the size gate sends the work to a subagent, and for the two mid-run
  routes into that seat (a stop-and-hand-off, a QC `[implementation]` blocker).
- `reviewer-prompt.md` — the single broad adversarial review over `BASE..HEAD`,
  briefed with the request and the approved approach.
- `fixer-prompt.md` — repair review or QC findings and commit.
- `qc-prompt.md` — short evidence-based shippability verdict: build, tests,
  acceptance, ship-blockers.
