---
name: score
description: Turns an approved spec into a decision-complete implementation plan — movements of Maestro-ready task groups with exact paths, interfaces, and test expectations, but no function bodies — then hardens the plan with an adversarial review panel (2-3 diverse-lens Opus skeptics + a cross-model Codex reviewer) before a final user-approval gate. When one goal is too big for a single plan it scales to a plan set — a dated directory where a 00-overview authored by the controller carries the cross-plan contracts and parallel plan-writer subagents author one plan file each. The terminal artifact is an approved plan or plan set — never code, and never a spec.
when_to_use: Use when a spec or agreed design exists and the user wants the implementation plan — "write the implementation plan", "plan this out", "break the spec into tasks", "score this spec" — including goals that need several coordinated plan files. Executing a finished plan goes to maestro; writing the spec itself goes to libretto.
---

# Score

In music, the score is what the conductor conducts from — every part written
out, every entrance marked, nothing left to guesswork on stage. Here it's the
same: your job is to turn an approved spec into an implementation plan complete
enough that a fresh implementer with zero conversation context can build each
part without asking what you meant. A large work ships as a suite — several
plans under one overview — but the finish line is the same: the plan. You do
not write the code; you hand off a clear, approved document and stop.

Like Libretto's author and unlike Maestro's conductor, **you are hands-on with
the plan**: you decompose the work, lock in the file structure, and write every
task yourself. You delegate exactly two things — gathering context (so your own
context stays lean) and the adversarial review of the finished plan (because
the author is the worst reviewer of their own work). Everything in between is
your judgment, checked once with the user at the structural fork.

## Why work this way

A plan is where decomposition decisions get locked in. Get them wrong and every
downstream implementer inherits the mistake — tasks that can't be built in
isolation, interfaces that drift between groups, spec requirements that simply
fall through the cracks. The author can't see those gaps in their own plan, so
the finished document goes to fresh skeptics with distinct lenses, plus one
cross-model reviewer, before any implementer sees it. That scrutiny is cheap
now and expensive once three subagents have built on a broken decomposition.

## Core principles

- **Decision-complete, not code-complete.** Every decision is made in the plan
  — exact file paths, interfaces, signatures, data shapes, named tests with
  their key assertions, exact verify commands. But the implementers are fresh
  Opus agents: they write the function bodies. A plan full of complete code has
  quietly become the implementation, done in the wrong phase with no review
  panel behind it.
- **You plan; you delegate scrutiny.** You read the spec, decompose the work,
  and author the plan. You dispatch subagents only to (1) scout context,
  (2) write the individual plan files of a plan set — the partition and the
  overview stay yours — and (3) review the written plan. Editing prose you
  wrote, in response to review, is your job — not delegated.
- **One plan by default; a set when the goal demands it.** When one goal
  genuinely exceeds what a single plan — and the single Maestro run that
  conducts it — can carry, split it into a **plan set**: `00-overview.md` plus
  one plan file per part, numbered in execution order. You judge the count
  from the seams in the work, not from a page target, and the user confirms
  the partition at the structure checkpoint like every other decomposition
  decision.
- **Movements are the unit.** Group tasks the way Maestro will conduct them:
  tightly coupled work travels together, one implementer-sized unit per group,
  clean seams between groups. The plan's structure *is* the execution structure.
- **The plan is the terminal artifact.** Score ends at an approved plan. It does
  not implement, and it does not redesign — open design questions go back to
  `/repertoire:libretto`, not into the plan as guesses. Point to
  `/repertoire:maestro` as the next step — but never invoke it.
<!-- canonical: shared/invariants.md — keep this bullet in sync -->
- **Opus for skeptics, plus one cross-model voice.** Every review-lens subagent
  runs on Opus at xhigh effort; one more reviewer is Codex, cross-model by design. Context
  scouting uses the read-only Explore agent.

## When to use

Use Score when a design is settled — ideally an approved spec from Libretto,
but any written spec or solid requirements will do — and the work is big enough
to need more than one sitting, up to goals big enough to need several
coordinated plans. If the change is small enough that a single
implementer could just do it, skip the plan. If the design itself is still
open (approach unchosen, interfaces unsettled), the work belongs in
`/repertoire:libretto` first; planning an undesigned thing just launders
guesses into tasks.

Score auto-invokes on matching requests; the structure checkpoint gates all
heavy work behind user approval.

## The pipeline

At a glance — the phase prose below is authoritative.

0. Locate the spec & scope check — no written spec → a short clarify pass
   (not a design dialogue); decide the artifact's shape: one plan, or a
   plan set (you pick the count; the user confirms in Phase 2).
1. Context scout (Explore) → brief.
2. Structure checkpoint — propose file structure + movements (for a set:
   the partition first, then per-plan structure); rework until the user
   approves.
3. Write the plan — single: author it yourself from the template. Set:
   author 00-overview.md, dispatch parallel plan writers (one per plan),
   reconcile their manifests. Self-check, commit.
4. Adversarial review panel, parallel — 2-3 Opus lenses + 1 Codex over the
   whole artifact (for a set: overview + every plan) → consolidate
   findings, revise, commit.
5. User approval (terminal) — structure changed → back to Phase 2; small
   edits → back to Phase 3; approved → point to /maestro (never invoke).

Create a TodoWrite list with one item per phase so a resumed session knows where
it left off; the committed plan is the durable trail. Whether a revision re-runs
the panel is the judgment call described in Phase 4 — it is not automatic.

### Phase 0 — Locate the spec & scope check

1. **Find and read the spec.** Use the path the user gave, or look in
   `docs/repertoire/specs/` for the obvious candidate; confirm with the user if
   it's ambiguous. Read it in full — the spec is your primary input, and reading
   it is your job, not a scout's.
2. **No written spec?** Run a **short clarify pass**: pin down what's being
   built, the constraints, and the success criteria in a handful of questions.
   This is not a design dialogue. If real design decisions are still open — the
   approach isn't chosen, interfaces aren't settled — stop and point the user to
   `/repertoire:libretto` rather than designing inside a planning skill.
3. **Scope check (plan-count decision).** Decide the artifact's shape: one
   plan, or a **plan set**. A single plan is the default. Reach for a set when
   one goal spans parts too big or too separable for a single plan — several
   subsystems, parallel feature tracks, work that will take multiple Maestro
   runs. The count is your judgment call, made from the seams in the work:
   each plan must still produce working, testable software on its own. The
   user confirms the partition at the structure checkpoint. Fully unrelated
   goals are not a set — they are separate Score runs; help the user pick the
   first.

### Phase 1 — Context scout

Dispatch one Explore subagent with `context-scout-prompt.md` to map the ground
the plan will sit on: likely touch points, conventions, prior art, constraints,
the exact **build and test commands**, and — unlike a design scout — a check of
the spec's claims about the codebase (files it names, interfaces it assumes),
flagging any drift. You read the brief, not the codebase, so your context stays
lean for the writing ahead. Dispatch it **once**; skip it only for a greenfield
project with no existing code to fit into.

### Phase 2 — Structure checkpoint

This is where decomposition decisions get locked in — the one user checkpoint
between spec and finished plan.

1. **Map the file structure.** Every file the plan will create or modify, one
   clear responsibility each. Prefer small, focused units with clear boundaries;
   files that change together belong in the same task. In an existing codebase,
   follow its patterns rather than restructuring on principle.
2. **Group tasks into movements** using the same criteria Maestro uses to group:
   tasks travel together when they touch the same files or module, when one
   depends on another's output, or when splitting them would force a second
   implementer to re-learn the first's context. Keep movements independent of
   each other where you can — a clean seam is a clean handoff and a clean
   commit boundary. A movement must be small enough for one agent to hold in
   context, large enough to stand on its own.
   **For a plan set, partition first, then structure each plan.** The same
   grouping criteria apply one level up: work travels into the same plan when
   it shares files, interfaces, or context; seams between plans must be at
   least as clean as seams between movements, because each plan is conducted
   by a separate Maestro run that never reads its siblings. Name each plan's
   deliverable, its dependencies on other plans, and the cross-plan interfaces
   that will live in the overview.
3. **Present the structure compactly** — the file map, the movements, and the
   one-line intent of each; for a set, lead with the partition (each plan, its
   one-line deliverable, the dependency order) and keep the per-plan detail to
   a movement sketch — and get the user's approval before writing the full
   plan. Use AskUserQuestion for discrete choices, prose for open ones. A wrong
   decomposition caught here costs a paragraph; caught after the panel, it costs
   the whole plan.

### Phase 3 — Write the plan

Write the approved structure out as a full plan at
`docs/repertoire/plans/YYYY-MM-DD-<topic>-plan.md`, relative to the project
root (create the directory if it doesn't exist), following `plan-template.md`.
Decision-complete throughout: exact paths, spelled-out interfaces and data
shapes, named tests with key assertions, exact verify commands, and the
build/test commands in the header where Maestro's Phase 0 looks for them.

**For a plan set**, the artifact is a directory —
`docs/repertoire/plans/YYYY-MM-DD-<topic>/` holding `00-overview.md` plus
`plan-NN-<slug>.md` files numbered in execution order — and the writing is
shared:

1. **Author `00-overview.md` yourself**, following `overview-template.md`. It
   carries what no single plan can: the goal, the plan inventory with its
   dependency order, the shared interfaces and data shapes that cross plan
   boundaries, project-wide conventions, and the build/test commands. You hold
   the spec and the approved structure, so the global document is yours —
   and writing it *first* pins the contracts the writers build against.
2. **Dispatch one plan writer per plan file, in parallel**, using
   `plan-writer-prompt.md`. Each gets its plan's approved structure slice, the
   spec (or the clarified requirements), the overview path, and the scout
   findings that touch its scope. Writers are build seats — dispatch them
   unpinned so they inherit the session model. Each writes exactly one file
   and returns a compact manifest: interfaces defined, interfaces consumed
   with the shape it relied on, and open assumptions.
3. **Reconcile the manifests.** Every consumed interface must match its
   definition — in the overview or the defining plan — exactly; every spec
   requirement must land in exactly one plan. Spot-read plan files only where
   manifests disagree. Fix small drift yourself; a "missing contract" in a
   manifest means the *overview* needs the edit. Re-dispatch a writer only
   when its plan needs structural rework.

**Plan failures — never write them:**

- "TBD", "TODO", "decide later", "fill in details".
- "Add appropriate error handling / validation" — name each failure mode and
  what handles it.
- "Write tests for the above" — name each test and the behavior it pins down.
- "Similar to Task N" — repeat the interface; movements are read in isolation
  by implementers who never see each other's briefs.
- A task that references types, functions, or signatures defined nowhere in the
  plan or the existing codebase.
- Full code blocks for routine work — that's implementation in disguise.
  Interfaces, signatures, schemas, and data shapes are decisions and belong in
  the plan; function bodies are the implementer's job.

**Self-check, then commit.** Re-read the spec (or, with no written spec, the
clarified requirements) with fresh eyes and walk it section by section — can
you point at the task (and, in a set, the plan) that implements each
requirement? Scan the plan for the failure patterns above. Check that
signatures and names used in later movements match where they were defined.
Fix inline, then commit — a set commits as one directory, overview and all.
This self-check catches the cheap mistakes so the panel can spend its
attention on the expensive ones.

### Phase 4 — Adversarial review panel

The headline gate. Dispatch the panel **in parallel** against the committed
plan, giving every reviewer both the plan path and the spec path — for a plan
set, the plan path is the set directory, and reviewers read the overview plus
every plan file. If there is
no written spec (the clarify-pass branch), paste the clarified requirements
into each reviewer's spec slot instead and say so — never send an
unsubstituted placeholder:

- **2-3 Opus reviewers**, each with one lens from *Choosing review lenses*,
  using `plan-reviewer-prompt.md`. Use 3 by default; 2 is fine for a genuinely
  small plan.
- **+1 Codex reviewer** for an independent cross-model pass
  (`codex-reviewer-prompt.md` — read
  `${CLAUDE_PLUGIN_ROOT}/shared/codex-reviewer-core.md` first; the stub
  composes with that shared contract). If Codex is unavailable, run the Opus
  lenses only and say so in your report — never silently drop a reviewer.

When the verdicts return, **consolidate from the finding text** — match by
movement/task + description, drop duplicates, discard anything that isn't a
real gap. Then **revise the plan yourself** (you wrote it; editing it is your
job), and commit. One review round is the gate; re-running the panel is a
judgment call — do it only if your revisions restructured movements or changed
interfaces, not as a counted loop, and never for small post-approval edits.

### Phase 5 — User approval (terminal)

Ask the user to read the committed plan:

> "Plan written, reviewed, and committed to `<path>`. Please read it and tell
> me if you want changes before we call it done."

- **Changes requested** → revise (re-open the structure checkpoint if the
  change is structural; otherwise just edit), re-commit, and re-review if
  warranted.
- **Approved** → you're done. Tell the user the plan is ready and that the
  natural next step is `/repertoire:maestro` to conduct it. **Do not invoke
  Maestro, start implementing, or touch any source file** — the handoff is the
  user's call.

## Choosing review lenses

Pick lenses that fit *this* plan so the skeptics cover different failure modes
instead of repeating each other. Good defaults for an implementation plan:

- **Spec coverage & traceability** — spec requirements with no implementing
  task, tasks with no spec backing (scope creep), a plan that quietly
  contradicts a decision the spec already made.
- **Buildability & self-containment** — whether a fresh implementer with zero
  conversation context could execute each task: undefined interfaces,
  placeholder steps, missing or unrunnable verify commands, tests too vague to
  write.
- **Sequencing & decomposition** — tasks that reference work built later,
  movement seams that split coupled work or weld independent work, a movement
  too big for one implementer to hold in context.

For a **plan set**, one lens of the panel must be:

- **Cross-plan coherence** — shared contracts in the overview that no plan
  implements, interfaces that drift between the defining and consuming plans,
  spec requirements that fall between plans or land in two, and dependency
  order between plans that contradicts the overview.

Adjust to the work (a migration-heavy plan might swap in a *rollback &
ordering* lens). Hand each reviewer exactly one lens. The Codex reviewer does a
general pass, so it complements the lens reviewers rather than duplicating them.

## The Codex reviewer

A cross-model reviewer catches what a room full of Claudes will agree to
overlook. Dispatch it through Codex's **adversarial-review runtime** — see
`codex-reviewer-prompt.md` for the invocation and the availability check. Do
**not** use the `codex-rescue` subagent: it is a write-capable forwarder that
refuses to run reviews. If neither the Codex plugin nor the `codex` CLI is
available, treat the reviewer as absent, proceed with the Opus lenses, and say
so.

## Handling subagent results

- **Context scout** returns a brief — read it, fold the touch points, commands,
  and any spec-drift warnings into the structure proposal. If it found nothing
  useful, proceed; don't re-dispatch to fish. If it found **spec drift** (the
  spec names files or interfaces that don't exist as described), surface that to
  the user at the structure checkpoint — it may need a spec fix, not a plan
  workaround.
- **Plan writers** return the path they wrote plus a manifest — interfaces
  defined, interfaces consumed with the shapes they relied on, open
  assumptions. You reconcile manifests against the overview; open a plan file
  only where manifests conflict. A writer that returns questions instead of a
  finished plan got an underspecified brief — fix the brief (or the overview)
  and re-dispatch; don't answer piecemeal.
- **Reviewers** return `PASS`/`FAIL` with findings. Consolidate from the text
  only; you don't open code files to dedupe. A finding you can't tie to a
  specific movement or task isn't actionable — drop it.

## Plan location & commits

- Plans live in the current project's shared Repertoire docs namespace:
  `docs/repertoire/plans/YYYY-MM-DD-<topic>-plan.md`, beside Libretto's
  `docs/repertoire/specs/`. The path is relative to the project root; create
  the directory if it's missing. (User preference for plan location overrides
  this.)
- A **plan set** gets a dated directory instead:
  `docs/repertoire/plans/YYYY-MM-DD-<topic>/` holding `00-overview.md` plus
  `plan-NN-<slug>.md` files numbered in execution order — the overview is
  `00` so it sorts first. (Same user-preference override.)
- Commit the plan when you first write it and again after each revision — the
  commits are the resume trail.
- Work on a branch if the repo convention calls for it; Score writes only the
  plan document, so it never touches source.

## Red flags

- Choosing an approach or settling an interface inside Score → that's design
  work; it belongs in Libretto. Stop and say so.
- Full code blocks for routine work in the plan → you're implementing in the
  planning phase, with no review panel behind the code.
- Any placeholder pattern from Phase 3's failure list → the implementer will
  guess, and guesses compound.
- Skipping the structure checkpoint because the decomposition "seems obvious" →
  it's the cheapest moment to be wrong in the whole pipeline.
- Reading the whole codebase yourself instead of dispatching the context scout
  → you'll burn the context you need for writing.
- Splitting into a plan set when one plan would do → every extra plan buys
  coordination cost; split at real seams, not for symmetry.
- Writing an approved set's plan files yourself, serially → that burns the
  author context the overview and reconciliation need; the parallel writers
  exist for exactly this. (Conversely, delegating `00-overview.md` → the
  global contracts are the author's job, never a writer's.)
- An overview that summarizes the plans instead of carrying contracts → if a
  line wouldn't change how someone writes or executes a plan, cut it.
- Folding a blank/missing Codex result into the panel as a pass → that's a
  dropped reviewer, not an approval.
- Writing code, scaffolding, or invoking Maestro after approval → the plan is
  the finish line; hand off and stop.

## Bundled files

- `plan-template.md` — the document structure for the plan (header, file
  structure, movements, tasks).
- `overview-template.md` — the document structure for a plan set's
  `00-overview.md` (plan inventory, execution order, shared contracts).
- `context-scout-prompt.md` — read-only Explore subagent that maps touch
  points, conventions, build/test commands, and spec drift into a compact brief.
- `plan-writer-prompt.md` — plan-set writer subagent (one per plan file,
  dispatched in parallel, unpinned); writes one plan, returns an interface
  manifest.
- `plan-reviewer-prompt.md` — skeptical plan reviewer, parameterized by lens
  (one lens each for the panel).
- `codex-reviewer-prompt.md` — what the Codex reviewer reviews; a thin stub
  composing with `shared/codex-reviewer-core.md` (the invocation mechanics).
