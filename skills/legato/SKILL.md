---
name: legato
description: Animation and motion polish session for a named UI surface — a read-only Opus scout audits the named pages or widgets against a bundled motion-craft bar (Emil Kowalski's MIT-licensed standards), the user locks direction at one AskUserQuestion gate (their goal grilled, or at most three proposed directions when they have none), one implementer builds the approved motion spec, a fresh Opus reviewer holds it to the ten standards, and a build-and-test QC gate seals the branch. Decisions land in the target project's docs/repertoire/animation.md — standing guidance, not a changelog. Commits as it goes, pushes only on request, never merges.
when_to_use: Use when the user wants UI animation or motion improved, redesigned, or polished on a surface they name, or asks to scan named pages or widgets for it — "polish the modal open animation", "the dropdown feels sluggish", "scan the checkout flow for motion polish", "run Legato on this". Medium-to-small scope only — an app-wide motion overhaul goes to libretto, score, maestro. A non-motion change goes to presto; broader improvement of working code goes to encore; an animation that is broken rather than wrong-feeling goes to tuner; "improve things, you pick" with nothing named goes to jam.
---

# Legato

Legato is playing without gaps — each note handed to the next, nothing
snapping, nothing teleporting. That is what good UI motion does, and this
skill runs the session that gets a named surface there: a scout that knows
what right feels like, a user who locks the direction, one implementer, one
skeptical reviewer, one gate, and a written record so the next session starts
where this one ended.

**Why work this way.** Motion is the highest-taste, lowest-diff work in a
codebase: twenty changed lines can make an interface feel exactly right or
faintly broken, and nothing in a test suite knows which. Legato compensates
with two things. First, a borrowed bar — Emil Kowalski's animation standards,
bundled in `references/` under his MIT license — so every value an agent
proposes is a citable number, not a vibe. Second, the user's own eye at one
deliberate gate: nothing is built until they have picked the direction, and
what they pick is recorded in the target project so no future session
re-argues it.

## Core principles

- **The conductor never plays.** Legato follows Maestro's doctrine, not
  Presto's carve-out — the diff may be small, but the judgment per line is
  dense, and it lives in the references and the approved spec, which is
  exactly what a fresh implementer receives. You dispatch, read reports, and
  run bookkeeping git commands; reading the guidance doc and the plan-level
  material is yours, reading source code is not.
- **Exact values or nothing.** Any value that appears in the bundled
  references — a curve, a duration band, a spring config, a stagger step — is
  copied, never approximated. Agents cite `references/STANDARDS.md` and
  `references/AUDIT.md` by path; a proposal without concrete values does not
  reach the gate.
- **One gate, before anything is written.** When the user arrives with a
  concrete goal, evaluate it against the bar — agree with evidence or push
  back with a cited standard — then grill the genuinely open choices through
  AskUserQuestion. When they don't know what they want, propose **at most
  three** coherent directions, recommended first, each carrying its exact
  values. Either way the user picks; a Legato run that edited a file before
  the gate cleared has skipped its only approval.
- **The record outlives the run.** `docs/repertoire/animation.md` in the
  target project is standing guidance — the motion language, the per-surface
  decisions, the directions considered and rejected. It is read in Phase 0,
  honored throughout (a documented decision is not re-litigated), and updated
  in Phase 7 by superseding rows in place. It is not a changelog and must not
  grow without bound.
- **Feel needs eyes when eyes are available.** If the app is reachable in a
  browser (a dev server the user is running, or a URL they give), the scout
  may look at the real motion and the reviewer may verify the result live.
  When no browser or server is available, both fall back to code-only without
  ceremony — and say plainly when a feel judgment could not be made from code.
  No agent starts a dev server; an already-running one is used, never owned.
- **One writer at a time.** One branch, one seat holding the pen. Everyone
  who changes code commits — commits are the unit of progress and the resume
  trail.
- **No cross-model seat, by design.** Legato seats no Codex reviewer, sharing
  Presto's sanctioned variance for the same reason (`shared/invariants.md`):
  the cross-model pass earns its wall-clock over a whole branch, not over one
  scoped polish. Work that deserves that scrutiny deserved the production
  line.
<!-- canonical: shared/invariants.md — keep this bullet in sync -->
- **Play it on your best.** The build seats — the implementer, the fixer, and
  the QC gate — run unpinned and inherit your session model; the scout and
  the reviewer stay pinned to Opus, the scout because its audit is the only
  investigation the run gets, the reviewer for a stable adversarial baseline.
  Every seat runs at xhigh effort: the ensemble is small, so no seat in it is
  cheap. Run this skill on the most capable model you have — `/model best`
  resolves to Fable 5 where you have access, otherwise Opus — never on
  Sonnet.

## When to use

Use Legato when the user names the surface — a page, a widget, a flow — and
wants its motion improved, redesigned, or polished, or asks you to scan that
named surface for animation work. The two entries share one pipeline; the
only difference is whether the scout verifies a stated target or sweeps a
stated scope.

The floor and the ceiling both matter. Below the floor — one obvious property
on one element, with a right answer the references settle (`ease-in` →
`ease-out`, an unbounded `transition: all`) — just make the change, cite the
standard, tell the user you skipped the pipeline, and stop. Above the
ceiling — an app-wide motion overhaul, a motion design system, a scan of
"everything" — this is not the room: point at `/repertoire:libretto`, then
`/repertoire:score`, then `/repertoire:maestro`, and offer to run Legato on
the one surface that hurts most today.

Legato auto-invokes on matching requests. **Cost gate:** if the user did not
name Legato or run `/legato`, confirm before the scout dispatches — one
AskUserQuestion carrying the restated brief, the branch you intend to use,
and the fact that a scout is about to audit the named surface. Fold the
branch decision into that question rather than asking twice; a misfire costs
one question and the repository is untouched. A user who named Legato has
already consented to the scout — ask only if the branch is ambiguous. The
Phase 2 gate still follows on every run: the cost gate is about spending, the
direction gate is about what gets built.

## The pipeline

At a glance — the phase prose below is authoritative.

0. Downbeat — restate the brief and mode (POLISH a named target / SCAN a
   named scope), read the guidance doc if one exists, scope check, decide
   BRANCH, find BUILD/TEST, note VIEW if the app is reachable, cost gate if
   Legato wasn't named, *then* cut the branch, capture BASE, TodoWrite.
1. Scout — one read-only Opus scout audits the named surface against the
   references; optional live look via VIEW. Inventory, proposals with exact
   values, size call.
2. The gate — evaluate the user's goal or propose ≤3 directions; one
   AskUserQuestion locks the motion spec. Too big → escalate here.
3. Build — one implementer subagent builds the approved spec and commits.
4. Review — one Opus reviewer on BASE..HEAD against the ten standards,
   always dispatched, never skipped.
5. Fix — clean PASS → skip; otherwise one fixer pass.
6. QC — build + tests + acceptance + reduced-motion: SHIPPABLE → Phase 7;
   NOT_SHIPPABLE → route blockers, retry; second strike → stop and escalate.
7. The record — create or update `docs/repertoire/animation.md` in the
   target project, first-run pointer question, commit, report.

### Phase 0 — Downbeat

1. **If resuming:** read TodoWrite and `git log --oneline` first. The todos
   carry `BASE` (in the `Review` todo), the mode, and the QC strike count;
   the commits carry the rest. Resume at the first phase not marked done. If
   `BASE` is genuinely lost, ask the user which commit the branch started
   from — never guess a range.
2. **Restate the request as the brief** — the mode (`POLISH: <target>` or
   `SCAN: <pages/widgets>`), what the user wants in one or two lines, and
   **acceptance**: the feel goal in words plus anything mechanical (builds,
   tests pass, reduced motion honored). Every agent receives this verbatim.
3. **Read the record.** If `docs/repertoire/animation.md` exists in the
   target project, read it now — it is guidance for this run, and its
   documented decisions are honored, not re-litigated. Note any decision that
   touches the named surface; it goes into every brief.
4. **Scope check.** Below the floor → fix it directly, cite the standard,
   stop. Above the ceiling (app-wide overhaul, motion system, unscoped
   "everything") → name the spec-to-maestro door and offer the one-surface
   alternative. Otherwise proceed; the scout's size call settles doubt at the
   gate.
5. **Decide `BRANCH` — but do not cut it yet.** Absent instruction: the
   current branch when it is already a feature branch, otherwise plan
   `legato/<slug>`. **Never build on main or master** without explicit
   consent. Surface uncommitted changes now — they are the user's work;
   resolve before Phase 3, never touch them.
6. **Determine `BUILD` and `TEST`** from the README or package manifest. If
   you cannot find them, ask — never let QC run on an invented command. A
   project with none records `none (user-confirmed)`.
7. **Note `VIEW`** — a URL for the running app, if the user gave one or the
   project's docs make it obvious and a browser tool is available in this
   session. `VIEW` is optional; absent, the run is code-only. Do not start a
   server to manufacture it.
8. **Cost gate** — if the user did not name Legato: one AskUserQuestion with
   the brief, the branch, and the coming scout dispatch. Nothing has been
   touched yet; that is the point of asking here.
9. **Now cut the branch** and **capture `BASE`** = `git rev-parse HEAD`.
   Create todos: `Scout`, `Gate`, `Build`, `Review`, `Fix`,
   `QC (strikes 0/2)`, `Record` — write `BASE` and the mode into the
   `Review` todo's text; it is the only place they survive a resume.

### Phase 1 — Scout

1. **One scout** (`scout-prompt.md`, Opus, read-only) audits the named
   surface. It receives the brief, the mode, the relevant decisions from the
   record, `VIEW` when it exists, and the absolute paths to
   `references/AUDIT.md` and `references/STANDARDS.md` — it reads those files
   first and judges against them, not against taste it improvises.
2. It returns an **inventory** of the surface's motion (and missing motion)
   with `file:line` anchors and current values; **proposals** with exact
   target values, each passing the frequency-and-purpose gate in AUDIT.md;
   a **size call** (S / M / L); and open questions only the user can answer.
   High-conviction shortlist, not a wishlist — rejected candidates are named
   with the reason.
3. **A second scout in parallel — only for a genuinely external surface** a
   codebase read cannot answer: a motion library's API or version behavior,
   a framework's animation constraint. It may search the web and read docs.
   Two is the ceiling.
4. Scouts read; they never edit, never run builds or tests, and never start
   servers. `BLOCKED` or an empty inventory → take what it found to the user
   at the gate and ask for the missing pointer.

### Phase 2 — The gate

1. **Report compactly** — the inventory in a few lines, the proposals with
   their values, the size call, which decisions from the record constrain the
   work, and the branch.
2. **When the user brought a concrete goal:** evaluate it against the
   references — agree with evidence, or push back citing the exact standard
   (a keyboard-triggered animation, an `ease-in`, a 500ms dropdown). Then
   **grill the open choices** in one AskUserQuestion — up to four real
   questions: values with trade-offs, scope edges, personality (crisp vs
   playful), reduced-motion treatment. Confirmed choices become the motion
   spec.
3. **When the user doesn't know what they want:** propose **at most three**
   coherent directions, recommended first, each with its exact values —
   option previews showing the before/after CSS or config are worth the
   space. One AskUserQuestion; their pick becomes the motion spec.
4. **When the size call is L** — or the work rests on an unmade design
   decision — make escalation an option here, naming the door
   (`/repertoire:libretto` → `/repertoire:score` → `/repertoire:maestro`).
   The user choosing it ends the run successfully.
5. **Nothing is written before this gate clears.** Fold any adjustment back
   into the spec and restate it; build on the answer, never on half of it.

### Phase 3 — Build

1. **One implementer subagent** (`implementer-prompt.md`, unpinned), fresh,
   with the brief, the approved motion spec verbatim, the scout's inventory
   for the touched surface, the record's constraints, the references paths,
   `BRANCH`, and `TEST`. It builds exactly the spec, extends the project's
   existing motion tokens rather than inventing parallel ones, honors
   reduced motion, and commits its own work.
2. **Never two writers.** One implementer, dispatched alone; you do not edit
   while it runs.

### Phase 4 — Review

1. Dispatch **one Opus reviewer** (`reviewer-prompt.md`), scope `BASE..HEAD`,
   with the brief, the motion spec, the references paths, and `VIEW` when it
   exists. It holds the diff to the ten non-negotiable standards and reports
   findings as a Before/After/Why table with severities, `PASS` or `FAIL`.
2. **Always a subagent, always dispatched** — there is exactly one review in
   this pipeline; skipping it leaves none.

### Phase 5 — Fix

1. **Clean `PASS` → skip.** A fixer with an empty finding list is a wasted
   seat.
2. Otherwise **one fixer subagent** (`fixer-prompt.md`, unpinned), one pass,
   all findings pasted with their exact target values. Anything it disputes
   or cannot fix goes into the QC brief verbatim; do not loop Phase 5.
3. The fixer commits.

### Phase 6 — QC gate

1. Dispatch the **QC agent** (`qc-prompt.md`, unpinned) with `BASE..HEAD`,
   `BUILD`, `TEST`, the brief and motion spec, and any unfixed or disputed
   findings. It runs the build and tests, confirms the spec is satisfied *by
   the code*, checks reduced-motion handling exists where movement was
   added, and scans for ship-blockers.
2. **`SHIPPABLE`** → Phase 7. **`NOT_SHIPPABLE`** → route each blocker —
   `[defect]` to a fixer, `[implementation]` back to an implementer — then
   re-run QC in retry mode (prior blockers + fix SHAs pasted; full build and
   tests, close-read only the fixes).
3. **Two strikes, then the user.** On the second `NOT_SHIPPABLE`, stop and
   escalate with AskUserQuestion — offering `/repertoire:maestro` (work was
   mis-sized) or `/repertoire:tuner` (what is failing is a bug) among the
   options. Update the `QC (strikes N/2)` todo as strikes accrue.

### Phase 7 — The record

1. **Create or update `docs/repertoire/animation.md`** in the target project
   from `guidance-template.md`. New decisions land as rows; changed decisions
   **supersede their old rows in place**; directions the user rejected at the
   gate are recorded so no future session re-proposes them. Stamp the doc
   with the date and commit. It states what *is*, never the history of how it
   got there.
2. **First creation only:** ask via AskUserQuestion whether to add a one-line
   pointer to the doc in the project's `CLAUDE.md`, its `AGENTS.md`, or
   neither. Never edit those files without this consent.
3. **Commit the doc on the branch**, then report.

### The report

- **What changed** and why, in a few lines — in motion terms, not diff terms.
- **Files and commit SHAs.**
- **Seats used.**
- **QC evidence** — build result and test summary, real output.
- **Feel checks for the user** — the specific interactions to try by hand
  (slow-motion, spam-the-toggle, reduced-motion on), because the last judge
  of feel is a human eye. Name anything no agent could verify from code.
- **The record** — what was written to `docs/repertoire/animation.md`.
- **Left open**, and **the user's call** — push only if asked, never merge.

## The ensemble budget

Four agents on the floor — scout, implementer, reviewer, QC. **Six on the
nominal path** — a second scout for an external library surface, and a fixer.
QC retry rounds spend past six; that is what the two-strike budget bounds.
What the budget forbids is growing the ensemble **by design** — a review
panel, a cross-model seat, a third scout, an app-wide sweep. That ensemble
belongs to the production line; say so and name the door.

## Handling subagent status

**Scout:** inventory + proposals + size call → the gate. A thin result is
still a result; `BLOCKED` → surface it at the gate with what it did find.

**Implementer:** `DONE` → review. `DONE_WITH_CONCERNS` → concerns go
verbatim into the reviewer's brief. `NEEDS_CONTEXT` → supply what was
missing and re-dispatch; never re-dispatch unchanged. `BLOCKED` → more
context, or back to the user; if the approved spec itself is wrong, that is
a gate decision.

**Fixer:** `FIXED` → QC. `PARTIALLY_FIXED` / `COULD_NOT_FIX` → remainder
into the QC brief verbatim.

**Reviewer** reports `PASS` / `FAIL`; **QC** reports `SHIPPABLE` /
`NOT_SHIPPABLE` with typed blockers.

## Red flags

- Editing a file before the Phase 2 gate cleared, or implementing anything
  above the floor yourself — the conductor never plays here.
- Approximating a value the references state exactly, or letting a proposal
  reach the gate without concrete values.
- Re-proposing a direction the record already rejects, or re-litigating a
  documented decision.
- Appending run-history to the guidance doc instead of superseding rows —
  it is standing guidance, not a changelog.
- Sweeping surfaces the user never named, or growing a scan into an
  app-wide audit instead of naming the spec-to-maestro door.
- Skipping the reviewer, a second review round, a panel, or a cross-model
  seat.
- An agent starting a dev server, or a scout editing, building, or testing.
- Editing `CLAUDE.md` or `AGENTS.md` without the Phase 7 consent question.
- Guessing `BUILD` or `TEST`, building on main without consent, looping QC
  past two strikes, pushing unasked, or merging — ever.

## Bundled files

- `scout-prompt.md` — read-only Opus audit of the named surface: inventory,
  gated proposals with exact values, size call.
- `implementer-prompt.md` — build the approved motion spec in one pass and
  commit.
- `reviewer-prompt.md` — the single adversarial review against the ten
  standards, Before/After/Why findings.
- `fixer-prompt.md` — repair review or QC findings and commit.
- `qc-prompt.md` — evidence-based shippability verdict: build, tests,
  acceptance, reduced motion, ship-blockers.
- `guidance-template.md` — the scaffold for the target project's
  `docs/repertoire/animation.md`.
- `references/` — the bundled motion-craft bar: `STANDARDS.md`, `AUDIT.md`,
  and `VOCABULARY.md`, copied from Emil Kowalski's MIT-licensed
  [skills repository](https://github.com/emilkowalski/skills) with his
  license and provenance in `references/LICENSE`.
