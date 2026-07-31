---
name: jam
description: Fully autonomous improvement session that finds its own worklist and ships it — three parallel read-only Opus scouts study the repo from its end users' perspective (new non-breaking features, quick fixes, UI/UX polish), a selector distills their candidates into a 3-5 job docket sized to one session, fresh implementers build each job with per-job Opus review at the conductor's discretion, and the whole branch is sealed by a two-reviewer final gate (pinned Opus beside the session's best model) plus an evidence-based QC. Announces the docket rather than gating on it, runs on a fresh jam/ branch, commits as it goes, pushes only on request, never merges.
when_to_use: Use when the user wants the project improved without naming what — "find some quick wins and ship them", "make this repo better, you pick what to do", "spend a session improving whatever users need most", "run Jam". The user naming the one concrete change goes to presto; a written plan to execute goes to maestro; improving one named target with user-picked lenses goes to encore; discovering ideas that should end in a shortlist, not code, goes to eureka; a reported bug goes to tuner; motion polish on a named surface goes to legato.
---

# Jam

No brief, no plan, no named target — the user hands you the keys for one
session. You find what is worth doing, decide what fits, build it, and prove
it shipped clean. Three scouts listen to the codebase from the user's seat, a
selector writes the set list, and you conduct it to a sealed branch without
stopping to ask.

**Why work this way.** A session with no user at the wheel has to replace the
user's judgment with structure. Discovery is three diverse lenses instead of
one perspective's blind spots; a selector — not the scouts' enthusiasm, and
not yours — decides what fits the clock; review is discretionary per job but
the final gate runs at full strength; and the verdict is evidence — a build
and a test run — never a nod. The docket is announced, not gated: the user
reads what you chose while you build it, and vetoes by interrupting, not by
being asked.

## Core principles

- **You conduct; you never play.** Jam is not Presto: you do not read source
  files or diffs, write code, or apply fixes — every change happens inside a
  subagent, and what returns to you is a compact report. The bookkeeping
  carve-out is the same as Maestro's: reading subagent *reports* and running
  read-only git metadata commands (`merge-base`, `rev-parse`,
  `log --oneline`, `status`) is conducting; reading code is playing.
- **Sit in the user's seat.** Every scout hunts for what this project's
  actual users — CLI users, library consumers, site visitors, plugin
  installers — would notice and thank you for. The maintainer's backlog is
  not the assignment.
- **The docket is a bet, sized to the session.** Three to five jobs, each
  non-breaking and small enough that the whole set finishes here. The
  selector composes it; you may strike a job that violates scope, but you do
  not add one.
- **Announce, don't ask.** The run is fully automated by design. The only
  stops are the cost gate (auto-invoke only), Phase 0 blockers only the user
  can resolve (a dirty tree, an undiscoverable test command), and the QC
  two-strike escalation. Everything else — the docket, review decisions,
  dropped jobs — is announced in passing and proceeds.
- **Review is discretionary per job, never at the end.** You choose whether
  each job gets its own reviewer, guided by the selector's flag. The finale —
  two whole-branch reviewers plus QC — is never skipped, never thinned, and
  never waved through because the per-job reviews came back clean.
- **Prefer shrinking the docket to stalling the run.** A job that blocks
  twice is dropped and reported, not fought. An unattended run that stalls on
  one stubborn job delivers nothing; a four-job branch that ships is a
  success with a footnote.
- **One writer at a time; everyone who writes commits.** Never two
  implementers, never a fixer beside an implementer. Commits are the unit of
  progress and the same-session resume trail.
- **No cross-model seat, by design.** Jam seats no Codex agent — a variance
  it shares only with Presto, for its own reason: an unattended session
  cannot babysit a cross-model call that hangs, and a hung seat here stalls
  the whole run with nobody watching. The diversity Codex buys elsewhere is
  bought cross-tier instead: the finale pairs a reviewer pinned to Opus with
  a second reviewer on the session model.
<!-- canonical: shared/invariants.md — keep this bullet in sync -->
- **Play it on your best.** The build seats — implementers, fixers, the QC
  gate, and the finale's second reviewer — run unpinned and inherit your
  session model; the three scouts, the selector, the per-job reviewers, and
  the finale's first reviewer stay pinned to Opus — the judgment seats,
  because in an unattended run they stand in for the user's judgment. Every
  seat runs at xhigh effort. Run this skill on the most capable model you
  have — `/model best` resolves to Fable 5 where you have access, otherwise
  Opus — never on Sonnet.

## When to use

Use Jam when the user wants the project improved but names no particular
change: the mandate is a session of your judgment, not a task. If the user
names the one change, that is `/repertoire:presto`; a written plan is
`/repertoire:maestro`; one named target with user-picked lenses is
`/repertoire:encore`; ideas without code is `/repertoire:eureka`; a bug is
`/repertoire:tuner`.

Jam auto-invokes on matching requests. **Cost gate:** if the user did not
name Jam or run `/jam`, confirm before the scouts dispatch — one
AskUserQuestion stating that this is an unattended session on a fresh `jam/`
branch, that it will pick and build its own 3-5 jobs, and the rough seat
count (ten to nineteen subagents). A user who named Jam has already
consented. The Phase 0 blockers — a dirty tree, an undiscoverable test
command — are resolved *before* this gate, so once it clears (or was never
needed) the run proceeds to its report with no further question except the
QC escalation.

## The pipeline

At a glance — the phase prose below is authoritative; the review-decision
rule, the drop rule, the amputation rule, and the strike budget live there,
not here.

0. Warm-up — dirty-tree check, BUILD/TEST, cost gate if Jam wasn't named,
   *then* cut `jam/<date>` branch, capture BASE, TodoWrite.
1. Scouting — 3 read-only Opus scouts in parallel, one lens each: features ·
   fixes · polish. Candidates from the user's perspective.
2. The docket — one Opus selector merges, scores, and sizes: 3-5 jobs with
   review flags and build order. Announce it and go.
3. Build loop — per job: fresh implementer → per-job Opus review at your
   discretion → fixer when findings. Blocked twice → drop and move on.
4. Finale — two whole-branch reviewers in parallel (pinned Opus + session
   model) → sequential fixers → QC (build + tests): SHIPPABLE → report (push
   only on request, never merge); NOT_SHIPPABLE → route blockers, retry;
   second strike → stop + AskUserQuestion.

### Phase 0 — Warm-up

1. **If resuming:** read TodoWrite and `git log --oneline` first. There is no
   state file — the todos carry what commits cannot: each job's full docket
   block with its review flag, `BASE` and the carried findings in the finale
   todo, and the QC strike count. Resume at the first todo not marked done.
   If `BASE` is genuinely lost, recompute it — jam's branch is always cut
   fresh, so `git merge-base HEAD <the branch it was cut from>` is the
   branch point; ask the user only if even that is ambiguous.
2. **Check the tree.** Uncommitted changes are the user's work — if
   `git status` is dirty, stop and ask before anything else. This is a
   Phase 0 blocker, not a mid-run question.
3. **Determine `BUILD` and `TEST`** from the README or package manifest. If
   you cannot find them, ask now — never mid-run, and never let QC run on a
   guess. If the project genuinely has none, record `none (user-confirmed)`
   and pass that token wherever `BUILD` or `TEST` is handed to an agent.
4. **Cost gate** — if the user did not name Jam, the one AskUserQuestion
   described above. Nothing has been spent and no branch exists yet; that is
   the point of asking here.
5. **Cut the branch**: `jam/<YYYY-MM-DD>` from the current HEAD (suffix `-2`
   on collision). **Never build on main or master.** Capture
   `BASE` = `git rev-parse HEAD`; every whole-branch review is `BASE..HEAD`.
6. Create a TodoWrite list: `Scouts`, `Docket`, `Finale: review pair
   (BASE: <sha>)`, `Finale: QC (strikes 0/2)`, `Report`. Job items are
   inserted after Phase 2, when they exist. `BASE` lives in the finale
   todo's text as the cheap copy; because the branch is always cut fresh, a
   resume that lost the todo can still recover it from the merge-base with
   the branch it was cut from.

### Phase 1 — Scouting

1. Dispatch **three scouts in parallel** (`scout-prompt.md`, Opus,
   read-only), one lens each:
   - **features** — new capabilities this project's users would ask for
     next: non-breaking, no schema or dependency or public-interface
     upheaval, each buildable in a fraction of one session.
   - **fixes** — quick fixes users are silently paying for: real defects,
     papercuts, misleading error messages, stale docs and broken examples —
     each verified against the code, not assumed.
   - **polish** — the surfaces users actually touch: UI and UX, CLI output,
     onboarding and docs readability, naming, formatting, accessibility.
     Where an installed skill covers a surface (design, animation,
     performance), the scout names it as leverage in the candidate.
2. Scouts **read only** — no edits, no builds, no tests. Parallel dispatch is
   safe precisely because nobody writes.
3. Each returns at most six candidates — title, user-visible benefit,
   `file:line` evidence, size (S/M), risk, and a breaking? flag that must be
   `no` — plus the local patterns and constraints an implementer of its
   candidates must respect, naming an installed skill as leverage where one
   applies. Breaking ideas are listed out-of-scope for the report, never as
   candidates.
4. **One thin lens does not stall the run.** A scout returning `NONE` or
   `BLOCKED` is noted for the report and the other lenses proceed. If all
   three return nothing real, report that honestly and end — an empty docket
   on a polished repo is a finding, not a failure.

### Phase 2 — The docket

1. Dispatch **one selector** (`selector-prompt.md`, Opus) with all three
   scout reports pasted verbatim. It merges duplicates, scores user value
   against effort and risk, rejects anything breaking or oversized or
   speculative, and returns the **docket**: 3-5 jobs in build order, each
   with a brief, acceptance, anchors, the patterns and skill leverage
   carried from the scouts, size, and a review flag —
   `review: required` for anything touching behavior, logic, public surface,
   security, or data handling; `review: optional` only for pure docs, copy,
   or cosmetic changes. Plus the notable rejects, each with its one-line
   reason.
2. **Sanity-check scope only.** If a docket job violates the non-breaking
   rule or plainly cannot fit the session, strike it — striking is your
   call, adding is not. Fewer than three survivors is acceptable when the
   candidates were thin; say so rather than padding with rejects.
3. **Announce the docket** — the jobs in order, one line each with size and
   review flag, and the top rejects with reasons. Do not wait for a reply.
   Insert one TodoWrite item per job carrying its **full docket block
   verbatim** — brief, acceptance, anchors, patterns, size, review flag. The
   todos are the docket's only durable copy: implementer briefs, finale
   briefs, and QC all quote from it, and a resumed run has nowhere else to
   read it.

### Phase 3 — Build loop

For each docket job, in order, one at a time:

1. **Implementer** — dispatch a fresh subagent (`implementer-prompt.md`,
   unpinned) with the job's brief, acceptance, anchors, and patterns pasted
   in, plus the branch and `TEST`. Note the current `HEAD` first; after it
   returns, the job's range is `<noted-HEAD>..HEAD`.
2. **The review decision** — dispatch one Opus reviewer
   (`reviewer-prompt.md`, job mode, the job's range) when **any** of these
   holds: the flag says `required`; the implementer returned
   DONE_WITH_CONCERNS; or its report strayed past the brief. Skip the
   reviewer only when the flag says `optional` **and** the report is a clean
   DONE — the finale still reads that code. You may also **batch**: two
   adjacent jobs sharing a surface may take one reviewer over their combined
   range after both land. Batching is a scheduling choice; skipping a
   `required` review is not available — and if the second job of an intended
   batch is dropped, review the first alone right away: a deferred
   `required` review never dissolves with its batchmate.
3. **Fixer** — only when the review found something (`fixer-prompt.md`, the
   findings, one pass). A clean PASS skips straight on. Unfixed or disputed
   findings are appended to the finale todo's text the moment they exist —
   the todos are the only state that survives an interrupt — and carried
   into the finale briefs, never dropped.
4. **The drop rule.** NEEDS_CONTEXT or BLOCKED → re-dispatch once with the
   missing context supplied; a second failure drops the job: mark its todo
   `dropped: <reason>`, announce it in one line, and move on. If partial
   commits landed before the block, dispatch a fixer to revert them — the
   same clean-revert duty as a Phase 4 amputation — so a dropped job leaves
   no trace on the branch. Never re-dispatch unchanged, and never let one
   job eat the session.
5. Mark the job's todo done and move to the next.

### Phase 4 — Finale

1. **The review pair, in parallel**, over `BASE..HEAD`
   (`reviewer-prompt.md`, finale mode) — both briefed with the docket, the
   carried-forward findings, and any dropped-job reverts to double-check:
   - one reviewer **pinned to Opus**, lens *integrated correctness &
     cohesion* — do the jobs compose into one sound branch;
   - one reviewer **unpinned on the session model**, lens *user impact &
     regression risk* — did this session actually make things better for
     this project's users, and does anything regress.
   Neither seat is skippable, and clean per-job reviews do not thin this
   gate.
2. **Consolidate from finding text only** — match by `file:line` and
   description, drop duplicates. Route to fixer(s), sequentially, each
   committing. If two findings cannot be merged from their text, keep both.
3. **QC gate** (`qc-prompt.md`, unpinned) with `BASE..HEAD`, `BUILD`,
   `TEST`, and the docket annotated per job — shipped, dropped, or
   amputated — with each job's acceptance. It builds, tests, and verifies
   each shipped job's acceptance is satisfied by the code, returning
   `SHIPPABLE` or `NOT_SHIPPABLE` with typed blockers.
4. **NOT_SHIPPABLE** → route each blocker: `[defect]` to a fixer;
   `[implementation]` back to that job's implementer — or, when a job cannot
   be made shippable, **amputate**: a fixer reverts that job's commits
   cleanly, the todo flips to `dropped: not shippable`, and the docket
   shrinks. The branch ships what is sound. Then re-run QC in retry mode
   with the prior blockers and the fix SHAs since.
5. **Two strikes, then the user.** A round is one QC dispatch returning
   `NOT_SHIPPABLE`; update `Finale: QC (strikes N/2)` as each accrues. On
   the second, stop and escalate with AskUserQuestion — offer at least:
   keep iterating, amputate to what is sound and ship that, or hand the
   failing work to `/repertoire:maestro` (or `/repertoire:tuner` if what is
   failing is a bug). Escalate whether the run is interactive or autonomous;
   this is one of the two questions the automation never skips.

### The report

- **The docket, settled** — per job: shipped (with commit SHAs), dropped
  (with reason), or amputated. One table, no prose padding.
- **What the scouts saw** — candidates per lens, the top rejects with the
  selector's reasons, and the breaking-but-interesting ideas the scouts
  ruled out of scope; the user should see the road not taken.
- **Seats spent** — scouts, selector, implementers, which jobs got per-job
  review and which skipped it (and why), fixers, the finale pair, QC rounds.
- **QC evidence** — the build result and test summary, real output lines.
- **The user's call** — the merge is never yours; push only if asked, and
  say what is on the branch either way.

## The ensemble budget

Ten seats on the leanest real path — three scouts, the selector, three
implementers, the finale pair, and QC. A full five-job docket with per-job
reviews and a couple of fixers runs to about **nineteen**. QC retry rounds
spend past that by design; the two-strike budget is what bounds them.

What the budget forbids is growing the ensemble by design: a second
scouting round, a fourth lens, a finale panel of three, a cross-model seat.
A session that wants those wanted `/repertoire:encore` or
`/repertoire:maestro`, and the honest move is to say so in the report.

## Handling subagent status

**Scout:** `FOUND` with candidates → into the selector's brief verbatim.
`NONE` / `BLOCKED` → note for the report; the other lenses proceed.

**Selector:** `DOCKET` → announce and build. `EMPTY` → report honestly and
end the run; never invent jobs the scouts did not ground.

**Implementer:** `DONE` → the review decision. `DONE_WITH_CONCERNS` → a
reviewer, always, with the concerns pasted verbatim into its brief.
`NEEDS_CONTEXT` / `BLOCKED` → the drop rule (one enriched re-dispatch, then
drop).

**Fixer:** `FIXED` → proceed. `PARTIALLY_FIXED` / `COULD_NOT_FIX` → carry
the remainder into the finale briefs (from Phase 3) or the next QC round
(from Phase 4); never re-loop a fix pass.

**Reviewer** reports `PASS` / `FAIL` with severities; **QC** reports
`SHIPPABLE` / `NOT_SHIPPABLE` with typed blockers. Handle both as the phase
steps describe.

## Red flags

- Reading source files or editing code yourself — Jam's conductor never
  plays; that carve-out is Presto's, not yours.
- Pausing the run to ask a question the pipeline answers — the docket is
  announced, not approved.
- Adding a job the selector did not pick, or padding a thin docket.
- A breaking change, schema migration, or dependency upheaval in the docket.
- Two writers on the branch at once, in any combination.
- Skipping a `required` per-job review, or skipping either finale reviewer
  because the per-job reviews were clean.
- Fighting a blocked job past the drop rule instead of shrinking the docket.
- Guessing `BUILD` or `TEST`, or letting QC run on an invented command.
- Building on main or master; pushing without being asked; merging ever.
- Looping QC past two strikes instead of escalating.

## Bundled files

- `scout-prompt.md` — read-only Opus discovery, parameterized by lens
  (features · fixes · polish), from the end user's perspective.
- `selector-prompt.md` — merge the scout reports into the 3-5 job docket
  with build order, review flags, and the rejects on the record.
- `implementer-prompt.md` — build one docket job in one pass and commit.
- `reviewer-prompt.md` — skeptical reviewer, two modes: job mode over one
  job's range, finale mode over `BASE..HEAD` with a named lens.
- `fixer-prompt.md` — repair findings (or revert an amputated job) and
  commit.
- `qc-prompt.md` — evidence-based shippability verdict over the whole
  branch, with retry mode.
