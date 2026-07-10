---
name: score
description: Turn an approved spec into a decision-complete implementation plan — movements of Maestro-ready task groups with exact paths, interfaces, and test expectations, but no function bodies — then harden that plan with an adversarial subagent review panel (2-3 diverse-lens Opus skeptics + a cross-model Codex reviewer) before a final user-approval gate. Score authors the plan itself; it delegates only context-scouting and the adversarial review. The terminal artifact is an approved plan — never code, and never a spec. MANUAL-ONLY — invoke only when the user explicitly asks for Score by name or runs /score (e.g. "score this spec", "run Score on the spec", "turn this spec into a plan with Score", "resume the Score run"). Do NOT auto-trigger on generic "write an implementation plan / plan this out / break this into tasks" requests, and never on other senses of the word "score" (test/coverage/quality scores, "score this PR 1-10", ratings); if the user has not named Score as this skill, stay silent and leave the request to other workflows.
---

# Score

In music, the score is what the conductor conducts from — every part written
out, every entrance marked, nothing left to guesswork on stage. Here it's the
same: your job is to turn an approved spec into an implementation plan complete
enough that a fresh implementer with zero conversation context can build each
part without asking what you meant. The plan is the finish line. You do not
write the code; you hand off a clear, approved document and stop.

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
  and author the plan. You dispatch subagents only to (1) scout context and
  (2) review the written plan. Editing prose you wrote, in response to review,
  is your job — not delegated.
- **Movements are the unit.** Group tasks the way Maestro will conduct them:
  tightly coupled work travels together, one implementer-sized unit per group,
  clean seams between groups. The plan's structure *is* the execution structure.
- **The plan is the terminal artifact.** Score ends at an approved plan. It does
  not implement, and it does not redesign — open design questions go back to
  `/repertoire:libretto`, not into the plan as guesses. Point to
  `/repertoire:maestro` as the next step — but never invoke it.
- **Opus for skeptics, plus one cross-model voice.** Every review-lens subagent
  runs on Opus at xhigh effort; one more reviewer is Codex, cross-model by design. Context
  scouting uses the read-only Explore agent.

## When to use

Use Score when a design is settled — ideally an approved spec from Libretto,
but any written spec or solid requirements will do — and the work is big enough
to need more than one sitting. If the change is small enough that a single
implementer could just do it, skip the plan. If the design itself is still
open (approach unchosen, interfaces unsettled), the work belongs in
`/repertoire:libretto` first; planning an undesigned thing just launders
guesses into tasks.

Score is **manual-only**: run it when the user invokes it explicitly (by name
or `/score`), not as an automatic response to any plan-shaped request.

## The pipeline

At a glance — the phase prose below is authoritative.

0. Locate the spec & scope check — no written spec → a short clarify pass
   (not a design dialogue); a multi-subsystem spec → split, one plan per
   subsystem, pick the first.
1. Context scout (Explore) → brief.
2. Structure checkpoint — propose file structure + movements; rework until
   the user approves.
3. Write the plan — from the template, self-check, commit.
4. Adversarial review panel, parallel — 2-3 Opus lenses + 1 Codex →
   consolidate findings, revise the plan, commit.
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
3. **Scope check (decomposition gate).** If the spec covers multiple independent
   subsystems, plan one at a time — each plan must produce working, testable
   software on its own. Help the user pick the first; the rest get their own
   plans later.

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
3. **Present the structure compactly** — the file map, the movements, and the
   one-line intent of each — and get the user's approval before writing the full
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
clarified requirements) with fresh eyes and walk it section by section — can you point at the task that implements each
requirement? Scan the plan for the failure patterns above. Check that
signatures and names used in later movements match where they were defined.
Fix inline, then commit. This self-check catches the cheap mistakes so the
panel can spend its attention on the expensive ones.

### Phase 4 — Adversarial review panel

The headline gate. Dispatch the panel **in parallel** against the committed
plan, giving every reviewer both the plan path and the spec path. If there is
no written spec (the clarify-pass branch), paste the clarified requirements
into each reviewer's spec slot instead and say so — never send an
unsubstituted placeholder:

- **2-3 Opus reviewers**, each with one lens from *Choosing review lenses*,
  using `plan-reviewer-prompt.md`. Use 3 by default; 2 is fine for a genuinely
  small plan.
- **+1 Codex reviewer** for an independent cross-model pass
  (`codex-reviewer-prompt.md`). If Codex is unavailable, run the Opus lenses
  only and say so in your report — never silently drop a reviewer.

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
- **Reviewers** return `PASS`/`FAIL` with findings. Consolidate from the text
  only; you don't open code files to dedupe. A finding you can't tie to a
  specific movement or task isn't actionable — drop it.

## Plan location & commits

- Plans live in the current project's shared Repertoire docs namespace:
  `docs/repertoire/plans/YYYY-MM-DD-<topic>-plan.md`, beside Libretto's
  `docs/repertoire/specs/`. The path is relative to the project root; create
  the directory if it's missing. (User preference for plan location overrides
  this.)
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
- Folding a blank/missing Codex result into the panel as a pass → that's a
  dropped reviewer, not an approval.
- Writing code, scaffolding, or invoking Maestro after approval → the plan is
  the finish line; hand off and stop.

## Bundled files

- `plan-template.md` — the document structure for the plan (header, file
  structure, movements, tasks).
- `context-scout-prompt.md` — read-only Explore subagent that maps touch
  points, conventions, build/test commands, and spec drift into a compact brief.
- `plan-reviewer-prompt.md` — skeptical plan reviewer, parameterized by lens
  (one lens each for the panel).
- `codex-reviewer-prompt.md` — cross-model general plan review via Codex.
