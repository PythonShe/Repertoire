---
name: encore
description: Post-performance enhancement pass over a finished codebase, feature, or module — the audience demands more. A conductor that profiles the user-named target with a read-only scout, agrees an adaptive lens roster with the user (performance, security, robustness, DX, observability, …), hunts enhancement opportunities with parallel diverse-lens Opus hunters, and verifies every call against codebase reality with one read-only Opus verifier (evidence decides — refuted calls die, feature-sized ones route to /repertoire:libretto or /repertoire:score and never get built here). Only what the user picks at the set-list gate gets implemented — on a fresh encore/ branch by sequential fixers, panel-reviewed by 3 diverse-lens Opus skeptics when big or risky, and sealed with a staged final verdict — an evidence-based QC gate, then one cross-model Codex review once QC passes. Lands a committed enhancement dossier; pushes the branch and offers a PR behind one user approval, never merges. MANUAL-ONLY — invoke only when the user explicitly asks for Encore by name or runs /encore (e.g. "run Encore on the auth module", "Encore, revisit the exporter", "do an Encore pass over this repo", "resume the Encore run"). Do NOT auto-trigger on generic "optimize this code", "improve performance", "harden security", "clean up tech debt", or "audit the codebase" requests; do NOT fire on bug reports (Tuner's territory), open-PR review feedback (Coda's), or new-feature ideation (Eureka's); and never on other senses of the word "encore" (a concert encore, the exclamation, the Encore.dev backend framework, the Encore music-notation app, the film or album); if the user has not named Encore as this skill, stay silent and leave code improvement to other workflows.
---

# Encore

The production line has done its work: Maestro built the piece, Coda closed the
reviews, the work shipped. But the audience is still in the hall, demanding
more. An encore does not premiere new material — it returns to a piece the
audience already knows and plays it *better*. You conduct that return: profile
the finished target, hunt it for enhancement opportunities through the lenses
that fit it, verify every call against the code, let the user pick the set
list, and perform exactly that — nothing the audience didn't ask for.

**Why work this way.** A finished codebase can absorb unlimited "improvement",
which is exactly the danger: enthusiasm without evidence produces churn, and
unsupervised improvement produces scope creep. So every opportunity is hunted
by a specialist, verified by a skeptic who reads the actual code, and gated by
the user before a single line changes. Fresh, purpose-built subagents do all
the reading; only compact calls, verdicts, and reports return to you — so your
context survives a whole-codebase pass from first profile to final push.

## Core principles

- **You conduct; you never play.** You do not read source files or diffs, and
  you never edit code — every investigation and every change happens inside a
  subagent. Your *bookkeeping* carve-out: the scout's brief, the hunters' call
  boards, the verifier's program, read-only git metadata (`rev-parse`,
  `log --oneline`, `status`, `diff --stat` — numbers, not code), and the
  dossier, which you write yourself (it is a report, not code). Three write
  actions are also yours, by design: cutting the `encore/` branch, committing
  the dossier, and the single `git push` after the publish gate clears.
- **Evidence outranks enthusiasm.** A hunter's call is a proposal, not a work
  order. An enhancement that is good in general but wrong for *this* codebase
  is refuted, with the constraint that kills it named. Nothing reaches the
  set-list gate unverified, and a refuted call dies on the record — so no
  future run re-pitches it without new evidence.
- **The audience picks the set list.** Verified calls are presented; the user
  chooses what gets played. An unpicked call is never implemented, however
  confident the verifier was. Encore with an empty set list ends at the
  dossier — a legitimate ending, not a failure.
- **Enhancement, not expansion.** Encore makes what exists better; it never
  smuggles in new capability. The verifier routes feature-sized calls to the
  production line — `/repertoire:libretto` for ones needing design,
  `/repertoire:score` for design-complete ones — and they are never built in
  this run.
- **One verifier, whole board.** A single read-only verifier judges every call
  in one pass: two lenses flagging the same hot spot get one coherent verdict,
  and because it works alone, its build and test runs never collide.
- **Writers run alone; only pure readers run in parallel.** Hunters and panel
  reviewers only read, so they run together — and hunters never run builds or
  tests (the lone verifier does that). Fixers change the branch, so they run
  one at a time.
- **Every reviewer is spent once, where it counts.** Small set lists skip the
  panel — the QC agent reads the whole branch in full at the verdict instead.
  The cross-model Codex pass runs exactly once, after QC says MERGEABLE.
- **Everyone who changes code commits.** Each fixer commits its own work,
  referencing the call it plays; the commits and the dossier are the resume
  trail. You push exactly once, at the publish gate — and never merge.
- **Play it on your best.** The build seats — the fixers and the QC gate —
  run unpinned and inherit your session model; the hunters, the read-only
  verifier, and the panel skeptics stay pinned to Opus for a stable
  adversarial baseline; the scout stays on Explore. Run these skills on the
  most capable model you have — `/model best` resolves to Fable 5 where you
  have access, otherwise Opus — never on Sonnet. The only non-Claude agent is
  the Codex reviewer, cross-model by design.

## When to use

Use Encore when a finished target — a shipped feature, a stable module, a
whole codebase between projects — deserves a deliberate enhancement pass:
hunted, verified, user-curated, implemented, and re-reviewed in one session.
It is the natural step after Coda closes a PR, but it works on any code that
is *done*; "finished" means no half-built work in flight on the target.

Encore is **manual-only**: run it when the user invokes it explicitly (by name
or `/encore`), not as an automatic response to "optimize this" or "clean up
the code". Adjacent doors: a *bug* (something is broken) is Tuner's; review
feedback on an *open PR* is Coda's; hunting *new features* is Eureka's. Encore
needs a git repository to perform (branch, commits, push) — without one it can
still hunt and verify, but the run must end at the dossier, and you say so up
front.

## The pipeline

At a glance — the phase prose below is authoritative; resume, NEEDS_CONTEXT
re-dispatch, a clean panel passing straight to QC, and the confirming re-run's
strike budget live there, not here.

0. Setup & roster — establish target + ambition, capture SCOPE, TodoWrite;
   the scout (Explore) profiles the target, finds BUILD/TEST and prior
   dossiers, proposes lenses; roster gate (AskUserQuestion, multi-select).
1. Hunt — one Opus hunter per lens, parallel, read-only. No calls → report
   "no encore needed", bank the clean bill.
2. Verify & set list — a read-only Opus verifier judges the whole board;
   nothing CONFIRMED/ADJUSTED → finalize the dossier, stop. Program → dossier
   draft → set-list gate (batched multi-select); nothing picked → finalize
   the dossier, stop.
3. Perform — cut the encore/ branch, commit the dossier; sequential fixers,
   cheap+safe first.
4. Final verdict — panel (3 Opus lenses, parallel) only when the changes are
   big or risky, findings → sequential fixers; then QC (build + tests + full
   branch read): NOT_MERGEABLE → route blockers → retry-mode re-run, third
   strike → AskUserQuestion; MERGEABLE → Codex once, whole branch; findings →
   fix → one confirming QC re-run.
5. Take a bow — finalize the dossier; publish gate (push / PR / keep local);
   report.

### Phase 0 — Setup & the roster

1. **If resuming:** read TodoWrite, the latest dossier under
   `docs/repertoire/encores/`, and `git log --oneline` on the `encore/` branch
   if one exists. The dossier carries the roster, the program, the decisions,
   per-call outcomes, and — once the branch exists — `BASE`; resume at the
   first phase not marked done.
2. **Establish the target in conversation.** The user names what gets the
   encore: a feature, a module, a directory, the whole repo, or "what shipped
   since `<tag/ref>`". Capture it as `SCOPE` (paths) plus an optional `SINCE`
   ref, and derive `SLUG` — a short kebab-case handle for the target ("the
   auth module" → `auth-module`; the whole repo → the repo's name). The
   branch name and the dossier filename both use `SLUG`, so one run has one
   identifier. Also capture the user's **ambition** — each suspicion or wish
   they state ("I think the exporter is slow"), verbatim, one line apiece —
   hunters receive these as hints to investigate, never as pre-confirmed
   verdicts. If the invocation doesn't name a target, ask before anything
   else.
3. **Check the ground.** `git status` (bookkeeping): uncommitted changes are
   the user's work — flag them now and resolve before Phase 3, never touch
   them. No git repository → Encore still hunts and verifies, but every
   commit and Phases 3–5 vanish: the run ends at a written, uncommitted
   dossier — say so up front.
4. **Dispatch the scout** (`scout-prompt.md`, one Explore subagent). It
   returns the target profile, `BUILD` and `TEST` candidates, churn and risk
   facts, constraints, prior Encore dossiers (whose refuted and declined calls
   become exclusions), and a **lens proposal** — 3-4 ranked lenses fitting the
   target, plus runner-ups. If it found no build/test commands, ask the user —
   never let agents guess. If the user confirms the project genuinely has
   none, record `BUILD`/`TEST` as `none (user-confirmed)` and pass that token
   downstream: QC then judges by its full read and the directives' acceptance
   checks, and states the limitation in its evidence.
5. **Roster gate.** One AskUserQuestion (multi-select): the proposed lenses as
   options — at most four; name runner-ups in the question text, reachable via
   the built-in "Other" — each with the scout's one-line rationale. Say what a
   lens costs: one Opus hunter each. The confirmed roster is final for the
   run.
6. Create a TodoWrite list: `Scout`, `Roster`, `Hunt`, `Verify`, `Set list`,
   `Perform`, `Final verdict (strikes 0/3)`, `Publish gate`.

### Phase 1 — The hunt

1. Dispatch **one Opus hunter per confirmed lens, in parallel**
   (`hunter-prompt.md`) — each read-only, with `SCOPE`, the scout's digest,
   its lens charter, the exclusion list, and the user's ambition hints.
   Hunters never run builds or tests — they run side by side, and concurrent
   test runs corrupt each other; static reading is enough to call an
   opportunity.
2. Each hunter returns its **best six or fewer calls** — title, `file:line`
   evidence, current state, proposed enhancement, payoff, size, risk.
   `NO_CALLS` is an honest answer, not a failure.
3. **Merge the boards mechanically:** hunter ids are local — every hunter
   numbers from H1. Walk the hunters in roster order and assign global ids
   `E1…En` in sequence, tagging each call with its lens (`E4 (security, was
   H2)`); from here on, only E-ids exist. Judge nothing — content judgment is
   the verifier's. Possible duplicates across lenses stay on the board; the
   verifier folds them.
4. **Every hunter empty-handed** → the piece doesn't need an encore. Write a
   short dossier recording the roster and the clean bill (so the next run
   knows), offer to commit it, and stop.

### Phase 2 — Verify & the set list

1. Dispatch **one verifier** (`verifier-prompt.md`) — fresh Opus, read-only —
   with the entire board, `SCOPE` (and `SINCE`, when the run is so bounded),
   `BUILD`, and `TEST`. It works alone, so it
   may run builds and tests. Only for an unusually large board (roughly 15+
   calls) split into **sequential** batches — never parallel verifiers.
2. Per call it returns: **CONFIRMED** (real and worth it; directive included)
   | **ADJUSTED** (opportunity real, hunter's remedy wrong; corrected
   directive) | **REFUTED** (claim false or a constraint forbids it; evidence)
   | **DUPLICATE** (folded into another E-id) — plus, for survivors, a final
   size (`XS`–`XL`), risk, a **routing** (`inline` — Encore implements;
   `feature-sized` — route to `/repertoire:libretto`, or `/repertoire:score`
   when design-complete; the verifier names the door — left unnamed, default
   to libretto), and a value rank. Overall `NEEDS_CONTEXT` → supply
   what is missing and re-dispatch; never re-dispatch unchanged.
3. **Assemble the program** — the ranked table of confirmed calls — and write
   the dossier draft (see *Dossier location & commits*). Nothing confirmed →
   finalize the dossier as a pure audit record (refuted calls and their
   evidence are exactly what future runs need), offer to commit it, and stop.
4. **Set-list gate.** Present the program as one compact table, then
   AskUserQuestion (multi-select): one option per inline call — label = E-id +
   title, description = payoff, size, risk. The tool caps options at four per
   question and four questions per call: batch into questions of up to four
   calls **in rank order**, and issue further calls when the program exceeds
   sixteen. Every inline call appears exactly once; none is silently dropped.
   Feature-sized calls are **not** options — name them in the question text,
   one line each (`E7 — streaming export (L, → libretto)`); they live in the
   dossier, and their door is the user's to open later, never yours.
5. **Nothing picked** → the audience went home happy: record the decisions in
   the dossier, finalize, offer to commit it, and stop.

### Phase 3 — Perform

1. **Cut the branch.** Confirm the worktree is clean — the only allowed
   exception is the dossier file itself; anything else is the user's work,
   unresolved since Phase 0, and stops the phase. Create
   `encore/<yyyy-mm-dd>-<SLUG>` from the current HEAD and commit the dossier
   draft as the branch's **first commit**. **Then** record `BASE` =
   `git rev-parse HEAD` — the dossier commit. Every review range in this run
   (panel, QC, Codex, the panel-trigger size math) is `BASE..HEAD`, which
   therefore contains **code changes only** — the dossier never pollutes the
   diff the reviewers read. Note `BASE` in the todo and append it to the
   dossier header in the worktree; if both are lost, `BASE` is recoverable as
   the oldest commit unique to the branch. Mid-run dossier updates (SHAs,
   outcomes) ride **uncommitted** in the worktree until Take a bow — never
   commit them between fixes.
2. **Order the set list:** cheap-and-safe first (`XS`/`S`, low risk), then
   larger and riskier. If the run stops early, the branch still holds its
   quick wins — and an early failure on a risky call doesn't strand the cheap
   certainties behind it.
3. Dispatch fixers **sequentially** (`fixer-prompt.md`) — one per call by
   default; one fixer may take a few calls only when they are interrelated
   (directives touching the same files, or one acceptance check proving
   several). Each gets the verifier's directive (the
   directive is authoritative, not the hunter's original sketch), the original
   call, and `TEST`, and commits its own work referencing the E-id. Record
   each SHA in the todo notes and the dossier.
4. **PARTIALLY_DONE / COULD_NOT_DO** → carry into the re-review brief and
   record honestly in the dossier. Never a silent drop.

### Phase 4 — The final verdict

1. **Panel — only when the changes are big or risky** (see *When the panel
   runs*); say in the final report whether it ran and why. Dispatch 3 Opus
   reviewers with distinct lenses **in parallel** (`reviewer-prompt.md`),
   scope `BASE..HEAD`; every brief includes the calls the changes claim to
   play — an enhancement that doesn't deliver its payoff is a finding, and so
   is changed behavior the call never licensed. **Consolidate findings from
   the text only** — match by `file:line` + description, drop duplicates,
   discard non-defects — and route real defects to fixer(s), sequential, each
   committing. Small, low-risk set lists skip the panel: the QC agent reads
   the whole branch at the next step, so nothing ships unread.
2. **QC gate** (`qc-prompt.md`) — hand it `BASE`, `BUILD`, `TEST`, and the
   set list. It runs the build and tests and reads `BASE..HEAD` in full
   against every played call; verdict `MERGEABLE` or `NOT_MERGEABLE` with
   evidence.
   - **NOT_MERGEABLE** → route each blocker by type — `[defect]` → fixer;
     `[implementation]` → re-dispatch the verifier for that call alone (once)
     when the dispute is factual, or escalate to the user when it needs a
     decision — then re-run QC, filling the prompt's retry-round section (the
     prior blockers + the fix SHAs since — the retry re-runs build and tests
     in full but close-reads only the new fix commits). A **round** is one QC
     dispatch that does not clear; update
     `Final verdict (strikes N/3)` after each. On the **third** failed round,
     stop looping and hand the decision to the user via AskUserQuestion —
     never silently loop past three. Codex has not run yet: the cross-model
     pass is never spent on a branch same-model QC would bounce.
3. **Codex — once, after QC passes** (`codex-reviewer-prompt.md`): a
   cross-model adversarial pass over the **whole branch** (`BASE..HEAD`), the
   last reviewer in the pipeline. Unavailable or empty result → the verdict
   rests on QC alone — say so in your report, never silently drop it.
   - **No critical or important findings** → the branch clears → Phase 5.
   - **Findings** → route to fixer(s), sequential, then **one confirming QC
     re-run** in retry mode, with Codex's findings as the prior blockers (it
     counts toward the same strike budget; if it fails, it rejoins the normal
     blocker loop above). Do not re-dispatch Codex — it
     runs once — unless those fixes were themselves big or risky by the same
     bar as *When the panel runs*.

### Phase 5 — Take a bow

1. **Finalize the dossier:** per-call outcome — *played* (SHA) / *refuted* /
   *declined* / *routed* (which door) / *could-not* (why) — whether the panel
   ran, the QC evidence, and the Codex verdict. Commit it on the branch.
2. **Publish gate.** One AskUserQuestion presenting the outcome table, with
   options: *Push branch + open a PR* / *Push branch only* / *Keep local —
   I'll take it from here*. Draft the PR before asking, so the user approves
   what will actually be posted: title = the run's intent in one line; body =
   the dossier's outcome table (played calls + SHAs), the refuted, declined,
   and routed counts, and the dossier's path. No remote, or no `gh` for the
   PR option → offer only what is possible and say why. Nothing outward
   happens before this gate clears.
3. **Publish what was approved — push first, PR second:** `git push -u`
   (plain — never force), **confirm it succeeded**, then `gh pr create` with
   the approved draft. If the push fails, stop and report; do not open a PR.
4. **Report:** the per-call outcome table, whether the panel ran and why, the
   final verdict's evidence (QC output, then the Codex verdict), and what
   remains for the user — the merge (never yours) and the routed calls' doors
   (`/repertoire:libretto`, `/repertoire:score` — pointed to, never invoked).

Encore is re-runnable: the next run's scout reads this dossier, so refuted
calls stay dead, declined calls aren't re-pitched, and a fresh hunt starts
where this one ended.

## The lens menu

The scout proposes from this menu (and may coin a lens the menu lacks if the
target plainly calls for it); the user confirms at the roster gate. One lens =
one hunter — roster size is the hunt's cost dial.

- **performance** — hot paths, needless allocations, N+1 queries, missing
  caches, algorithmic waste.
- **security** — input validation, authz boundaries, secrets handling,
  injection surfaces, unsafe defaults.
- **robustness** — error handling, edge cases, partial failure, timeouts,
  retries, recovery paths.
- **maintainability** — duplication, dead code, deep coupling, opaque naming,
  oversized modules.
- **dx-ergonomics** — API/CLI ergonomics, confusing options, error-message
  quality, friction in the common flows.
- **test-coverage** — untested critical paths, brittle tests, missing
  regression pins.
- **observability** — logging/metrics gaps, silent failures, states that
  cannot be diagnosed from the outside.
- **accessibility** — UI targets only: keyboard paths, contrast, semantics,
  screen-reader flow.
- **dependency-health** — outdated, vulnerable, or abandoned dependencies;
  upgrade payoffs and their blast radius.
- **docs-accuracy** — docs that lie about current behavior, missing setup
  steps, stale examples.

## When the panel runs (and choosing lenses)

Most set lists skip the panel — the QC agent reads the whole branch at the
verdict and Codex follows cross-model, so a second same-model read of a small
diff buys nothing. Escalate to the 3-lens panel when the changes are big or
risky — any of:

- 4+ calls played, or any fixer ended PARTIALLY_DONE / COULD_NOT_DO,
- changes touched security-sensitive code, a public interface or schema, or
  concurrency,
- the branch spans more than ~5 files or ~150 lines
  (`git diff --stat BASE..HEAD` — numeric metadata, allowed bookkeeping).

Pick three lenses that fit what the changes *did*: behavior-adjacent
enhancements → correctness & edge cases · regression risk · test coverage; a
security-lens set list → make one lens security; performance work → make one
lens "is the win real" (measurement skepticism). Hand each reviewer exactly
one lens. Skipping the panel never skips scrutiny: every run still ends with
QC's full branch read and the cross-model Codex pass.

## The Codex reviewer

A cross-model reviewer catches what a room full of Claudes will happily agree
to miss — so Encore spends it exactly once, where it counts most: **after QC
returns MERGEABLE**, scoped to the **whole branch** (`BASE..HEAD`). It never
runs before or alongside QC, and never re-runs on strike rounds. Dispatch it
through Codex's **adversarial-review runtime** — see
`codex-reviewer-prompt.md` for the exact invocation and its gotchas. Do
**not** use the `codex-rescue` subagent: it is a write-capable implementation
forwarder that refuses reviews. If neither the Codex plugin nor the `codex`
CLI is available — or the call returns nothing — treat the reviewer as
absent: the verdict rests on QC alone, and you say so in your report. Never
silently drop a reviewer.

## Handling subagent status

**Scout:** returns the profile and lens proposal → roster gate. Thin findings
are fine — proceed; don't re-dispatch to fish. `BLOCKED` (unreadable target)
→ surface to the user.

**Hunter:** `CALLS` → merge onto the board. `NO_CALLS` → honest empty. A call
without `file:line` evidence is an incomplete report: send that hunter back
once, never patch it yourself; still unanchored → drop the call and note it
in the dossier.

**Verifier:** per-call `CONFIRMED` / `ADJUSTED` / `REFUTED` / `DUPLICATE` as
in Phase 2; overall `COMPLETE` or `NEEDS_CONTEXT` — supply what is missing
and re-dispatch; never re-dispatch unchanged, and at most twice — then take
the gap to the user.

**Fixer:** `DONE` → record the SHA. `PARTIALLY_DONE` / `COULD_NOT_DO` → into
the re-review brief and the dossier, honestly.

**Panel reviewer** reports `PASS`/`FAIL` with findings; **QC** reports
`MERGEABLE`/`NOT_MERGEABLE` with typed blockers; **Codex** reports
`PASS`/`FAIL` once, or is absent. Handle all three as the Phase 4 steps
describe.

## Dossier location & commits

- Dossiers live in the shared Repertoire docs namespace:
  `docs/repertoire/encores/YYYY-MM-DD-<SLUG>-encore.md` — beside Eureka's
  `ideas/`, Libretto's `specs/`, and Score's `plans/`. (User preference
  overrides.)
- Structure: **Header** (target, `SLUG`, `SCOPE`/`SINCE`, roster, date,
  branch and `BASE` once cut) · **The program** (ranked; per call: lens,
  verdict, evidence anchors, directive, size, risk, routing, the user's
  decision, outcome + SHA) · **Refuted** (with evidence — future runs'
  exclusion list) · **Routed** (feature-sized, with their door) ·
  **Declined** (one-liners).
- Start it as a **draft** when the program is assembled — it doubles as the
  on-disk resume trail — and finalize it at Take a bow.
- Stage and commit **only the dossier path** (`git add` that path
  specifically; never `-A`). It is committed exactly twice: the draft as the
  `encore/` branch's first commit (Phase 3), the final at Take a bow —
  mid-run updates stay in the worktree so the review range stays code-only.
  A run that ends before the branch exists commits it on the current branch
  instead, with the user's go-ahead. If the project is not a git repository,
  write the file, skip every commit, and say so.

## Red flags

- Reading source files or diffs yourself → playing, not conducting. Dispatch
  a subagent.
- Implementing a call the user didn't pick — "the verifier confirmed it" is
  not a pick.
- Hunting without the roster gate, or a roster of "everything" — everything
  is not a lens.
- A hunter running builds or tests → parallel runs collide; only the lone
  verifier runs them.
- A call reaching the set-list gate unverified, or a REFUTED call resurrected
  without new evidence.
- New capability smuggled in as an "enhancement" — feature-sized calls route
  to Libretto or Score and are never built here.
- A set-list or roster gate with more than four options per question, or a
  slot spent on "Other" — the tool caps at four and adds "Other" itself;
  batch instead.
- Two fixers in parallel; force-pushing; merging — the merge is never yours.
- Dispatching Codex before QC has passed, on less than the whole branch, or
  more than once.
- Skipping the final verdict because the set list was "trivial" — QC's full
  branch read plus the Codex pass are the floor.
- Committing dossier updates between fixes — the review range (`BASE..HEAD`)
  must stay code-only until the verdict is in.
- Pushing the branch or opening a PR before the publish gate clears.
- Polishing un-asked: touching files outside `SCOPE`, or "while I'm here"
  edits beyond a directive.

## Bundled files

- `scout-prompt.md` — read-only Explore profile of the target: stack, churn,
  constraints, BUILD/TEST, prior dossiers, lens proposal.
- `hunter-prompt.md` — lens-parameterized Opus hunter; one per roster lens,
  parallel, read-only, no build/test runs.
- `verifier-prompt.md` — one read-only Opus verifier over the whole call
  board; confirms, adjusts, refutes, dedupes, sizes, routes.
- `fixer-prompt.md` — implements a verified directive for a picked call and
  commits.
- `reviewer-prompt.md` — adversarial panel review, one lens per reviewer; big
  or risky set lists only.
- `qc-prompt.md` — evidence-based mergeability verdict (build, tests, full
  branch read against the set list).
- `codex-reviewer-prompt.md` — cross-model, whole-branch pass once QC clears.
