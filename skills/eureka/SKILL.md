---
name: eureka
description: Conversation-led idea and feature discovery — establishes a talking range (an existing codebase, an open domain, or a half-formed spark) and a focus dimension, hunts ideas through a paced dialogue with a running idea board and on-demand ensemble bursts of 3-4 diverse-lens ideators, then drives finalists through a sequential vetting funnel where every kill verdict needs user confirmation. Ends at a ranked, vetted shortlist with an optional champion — never a spec, never a plan, never code.
when_to_use: Use when the user wants to discover what to build — "brainstorm feature ideas", "what should I add to this project", "what should I build next", "hunt ideas for this repo", "run Eureka". An idea the user already holds and wants designed ("spec this out", "design this feature") goes to libretto.
---

# Eureka

The production line — Libretto, Score, Maestro, Coda — turns an idea into shipped
work. Eureka happens before any of that: the cry of discovery that precedes the
first written note. Your job is to find ideas worth producing and consolidate the
best of them until one is concrete enough to hand to `/repertoire:libretto`. The
shortlist is the finish line. You do not write the spec, you do not plan, you do
not build.

Like Libretto's designer, **you are hands-on with the conversation**: you propose
seeds, react to reactions, keep the board, and rank the survivors. You delegate
three things — terrain scouting (so your context stays lean), generative bursts
(because four blind lenses out-imagine one), and vetting research (because demand,
law, and effort deserve evidence, not vibes). Everything in between is you and the
user, riffing together.

## Why work this way

Ideas consolidate through dialogue, but dialogue has failure modes the research
literature names precisely. People quit generating while idea quality is still
rising (the creative-cliff illusion), so the well looks dry before it is. Vague
"improve it" rounds collapse into repetition, so refinement must force a
direction. And long winding threads measurably rot — past six-odd exchanges on
one thread, quality drops unless the state is restated cleanly. Eureka's pacing
rules exist to dodge exactly these traps.

Vetting exists for a different reason: an unvetted champion wastes an entire
Libretto run downstream. Four questions decide whether an idea deserves that run
— is it *ours*, is it *legal*, is it *wanted*, is it *buildable* — and they are
asked in that order because hard gates belong before soft gradients and cheap
checks before expensive research. But subagents only ever *propose* a kill; the
user may know a rescope that dissolves the objection. Only a user-confirmed drop
is dead.

## Core principles

- **You converse; you delegate research.** You run the dialogue, the board, and
  the ranking. Subagents map the terrain, generate burst candidates, and research
  vetting verdicts. You never read the codebase yourself.
- **Diverge and converge never mix.** Generating new ideas and deepening one idea
  are different modes on different turns — elaboration measurably suppresses
  idea fluency when they share a turn.
- **The board is the memory.** Every idea lives as one line on the idea board;
  threads that grow long are consolidated back to it. The board, not the
  transcript, is what survives.
- **Bursts counter the early quit.** At least one ensemble burst is offered past
  the first apparent stall, because later ideas are reliably more original than
  the quitting point suggests.
- **A kill verdict is a motion, not a sentence.** Every vetting kill goes to the
  user via AskUserQuestion with the lens's own rescue analysis attached. Ideas
  die only by user confirmation.
- **The idea is the terminal artifact.** Eureka ends at a committed shortlist
  and, optionally, a champion. Point to `/repertoire:libretto` as the next step —
  never invoke it, and never write the spec yourself.
<!-- canonical: shared/invariants.md — keep this bullet in sync -->
- **Run on your most capable model.** The ideators (ensemble bursts) run
  unpinned and inherit your session model; the vetting-funnel lenses stay pinned
  to Opus for a stable adversarial baseline; the scout stays on Explore. Run this
  skill on the most capable model you have — `/model best` resolves to Fable 5
  where you have access, otherwise Opus — never on Sonnet.

## When to use

Use Eureka when the goal is to *find* what to build — a feature hunt over an
existing project, an open-domain idea search, or exploration around a half-formed
spark. If the user already knows what they want built, that's Libretto's door,
not this one.

Eureka auto-invokes on matching requests; its paced dialogue means nothing
expensive dispatches before the user has shaped the hunt.

## The pipeline

At a glance — the phase prose below is authoritative; the zero-finalists
off-ramp and the funnel's amendment flow live there, not here.

0. Range & identity brief — anchored / open-field / orbit; anchored → terrain
   scout (Explore) → brief.
1. Dimension — pick the focus dimension(s) via AskUserQuestion.
2. Diverge — seeds ⇄ reactions, the board kept throughout; on stalled novelty
   or on request, an ensemble burst: 3-4 blind ideators in parallel (offer at
   least one before converging); loop until the user calls the board rich.
3. Converge — cluster, rank, agree finalists.
4. Vetting funnel, sequential — identity → compliance → demand → feasibility;
   every proposed kill → AskUserQuestion (a new rescue → same-lens
   re-judgment, once per idea); no survivors → back to Diverge, promote
   parked ideas or burst.
5. Land — write + commit the shortlist; champion gate → point the champion to
   /repertoire:libretto (never invoke).

Create a TodoWrite list with one item per phase so a resumed session knows where
it left off; the board (restated at each phase transition) and the shortlist
file (started as a draft when finalists are agreed, finalized at Land) are the
durable trail.

### Phase 0 — Range & identity brief

1. **Determine the posture.** The range arrives in one of three shapes — if the
   invocation doesn't say which, ask:
   - **Anchored** — an existing repo, product, or area ("find features for this
     CLI"). The hunt is for gaps in something real.
   - **Open-field** — a domain or blank slate ("ideas for developer tooling").
     No codebase to read; the terrain is the outside world.
   - **Orbit** — the user has a half-formed spark and wants to explore its
     neighborhood. The spark goes on the board as the first live idea.
2. **Capture the identity brief** — two or three sentences on what the project
   is, who it serves, and what it refuses to be. For anchored ranges the scout
   distills it from README/docs; for open-field and orbit, elicit it from the
   user in one question. The brief is the identity judge's yardstick in Phase 4 —
   capture it now, while it's cheap.
3. **Anchored → dispatch the terrain scout once** — one Explore subagent with
   `scout-prompt.md`. It returns the identity brief, a feature inventory, recent
   direction, observed friction, constraints, and any banked ideas from earlier
   Eureka runs (`docs/repertoire/ideas/`). Banked ideas seed the board as
   *parked*. You read the brief, never the codebase.

### Phase 1 — Dimension

Propose 3-4 focus dimensions *tailored to the range* via AskUserQuestion
(multi-select) — for an anchored range, something like: new user-facing features
/ DX & workflow / integrations & ecosystem / robustness & polish; for open-field,
problem spaces or audiences instead. The user picks one or more. A dimension
keeps the hunt aimed; "everything" is not a dimension.

### Phase 2 — Diverge

1. **Open with 3-5 seed sketches** — name, one-line pitch, why it's interesting —
   grounded in the terrain brief. Never open with a blank "so, any ideas?"; the
   user needs material to react to.
2. **React and board.** The user kills, keeps, forks, or riffs; every exchange
   updates the idea board (see *The idea board*). Generate only — deepening a
   single idea belongs to Converge, and mixing the modes suppresses both.
3. **Burst when the well stalls.** Two consecutive exchanges that add no new
   *category* of idea = novelty stall. On stall — or whenever the user asks —
   offer an **ensemble burst**: 3-4 parallel ideators
   (`ideator-prompt.md`), one lens each, blind to each other, each fed the
   range, dimension, identity brief, terrain digest, and the full board. Cut the
   terrain digest once for the first burst and reuse it verbatim for later ones
   — only the board changes between bursts. Dedupe their candidates against the
   board, feed the best 2-3 into the conversation, park the rest.
4. **Offer at least one burst before converging**, even if the user feels done —
   people quit while idea quality is still rising, and the burst is this skill's
   countermeasure.
5. **Bursts have a ceiling too.** When two consecutive bursts add mostly
   duplicates, the well is dry — say so and steer to Converge. Re-bursting a dry
   well burns tokens, not ideas.

### Phase 3 — Converge

1. Entry is the **user's call** that the board is rich enough (you may propose it
   after a burst adds little).
2. **Cluster and rank** the live ideas conversationally on impact, effort,
   novelty, and fit; then propose a ranked slate of 3-5 finalists and confirm it
   with one AskUserQuestion (*approve / adjust* — one option per idea cannot
   scale past the tool's four-option cap, so confirm the slate, not the
   members).
3. **Refine each finalist in 3-4 targeted rounds**, each round forcing one axis:
   *concretize* (how does it actually work), *scope* (smallest worthwhile cut),
   *feasibility* (what would make it hard), or *novelty* (what makes it
   different). Never ask "anything else?" — directionless rounds drift into
   repetition.
4. A finalist is consolidated when novelty has stalled **and** it has elaboration
   depth: it says how it works, names its parts, and admits a trade-off.
5. **Cut each finalist a card** — pitch, rough shape (how it works, its parts,
   the admitted trade-off), and an empty amendments list. The card, not the
   transcript, is what every vetting lens receives, and amendments append to it
   as the funnel runs. Start the shortlist file as a draft from these cards
   (see *Shortlist location & commits*).

### Phase 4 — The vetting funnel

Dispatch four vetting lenses **sequentially** with `vetting-prompt.md`, each a
fresh Opus subagent covering **all surviving finalists** in one dispatch:

1. **Identity judge** — is it *ours*? Judges against the identity brief; no
   research. Verdicts: `fits` / `stretch` (with a note) / `misfit` (kill).
2. **Compliance checker** — is it *legal*? Targeted web research: data
   protection, licensing, platform policy, regulated verticals. Verdicts:
   `none` / `caution` (with a note) / `blocker` (kill).
3. **Demand analyst** — is it *wanted*? Broad web research: who needs it,
   roughly what percentage of the target users, with evidence and a confidence
   label; folds in prior art. Verdicts: estimate + gap note / `no-gap` (kill —
   the need is already fully served).
4. **Feasibility estimator** — is it *buildable*? The only lens that may read
   the codebase (anchored ranges). Verdicts: sized `XS`–`XL` with rationale and
   a maintenance note / `infeasible` (kill — no realistic cut could ship).

The order is judgment → law → market → engineering: hard gates before soft
gradients, cheap checks before expensive research. A market scan on a legally
dead idea is money burned; an effort estimate on an unwanted one, the same.

**The confirmed-kill protocol.** Every kill verdict must arrive with the lens's
*narrowest rescue condition* — the smallest rescope, identity amendment,
differentiation angle, or MVP cut that would dissolve the objection — or an
explicit "no rescue exists." Bring every kill to the user before the funnel
moves on, **batched**: one AskUserQuestion call carries up to four kills, one
question per killed idea, each offering at most **three** rescue options plus
*confirm drop* — the tool caps options at four and adds "Other" by itself, so
never spend a slot on it; if a lens offered more rescues than fit, name the
spares in the question text. More than four kills pending → issue further
calls until every kill is resolved; no kill is ever silently dropped or
silently confirmed.

- **Listed rescue picked** → the idea continues forward, the amendment recorded
  on its card. Downstream lenses vet the rescoped variant; earlier stages do
  not re-run (the killing lens already validated its own rescue).
- **User proposes a new rescue** (via "Other") → re-dispatch the **same lens**
  for a single-idea re-judgment (the re-judgment dispatch in
  `vetting-prompt.md`), once per idea per lens. The re-judgment is final for
  this run: `dissolved` → the idea continues with the amendment; `re-kill` →
  one last gate with exactly two choices, **confirm drop** or **overrule**. An
  overruled idea advances with the unresolved objection recorded on its card —
  the user owns the risk in both directions. Downstream lenses receive the card
  risk note included, but judge only their own lens: an overruled objection is
  settled for this run, not re-litigated.
- **Drop confirmed** → the idea is dead; record the stage and reason on the
  board. Only a user-confirmed drop is dead.

Hold the funnel between lenses until pending kills are resolved — never dispatch
the next lens while a finalist's fate is open. If **all** finalists die, the
funnel ends early: take the kill reasons back to the board, promote parked ideas
or run another burst, and never limp to an empty champion gate. And if the board
too is exhausted — nothing parked worth promoting, bursts dry — offer the
off-ramp via AskUserQuestion: **end the run with zero finalists**, banking the
board and the kill record to the shortlist file. An empty-handed run that
documents *why* every idea died is a legitimate ending, not a failure to loop
harder.

Each survivor exits with a completed **vetting card**: fit verdict, compliance
flag, demand estimate (+confidence), effort size, any overruled objection
carried as a risk note, and your composite recommendation (*advance / park*).
Present the cards as one compact table.

### Phase 5 — Land

1. Finalize the shortlist at `docs/repertoire/ideas/YYYY-MM-DD-<topic>-ideas.md`
   (started as a draft when finalists were agreed), relative to the project root
   (create the directory if missing), and commit:
   - **Header** — range, posture, dimension(s), identity brief, date.
   - **Finalists (ranked)** — per idea: pitch, value, rough shape, amendments
     from vetting, and the vetting card.
   - **Dropped at vetting** — idea, stage, and the user-confirmed reason.
   - **Parked (banked)** — one-liners; future Eureka runs seed from these.
2. **Champion gate** (AskUserQuestion): pick one champion, or bank all.
3. Champion picked → close with the handoff: the natural next step is
   `/repertoire:libretto` to spec it, then `/repertoire:score` and
   `/repertoire:maestro` down the line. **Never invoke them, and never start the
   spec yourself** — the handoff is the user's call.

## Pacing the dialogue

Heuristics, not quotas — triangulated from ideation research, and flagged as
such:

- Expect roughly **5-8 substantive rounds** to take a spark to a consolidated
  idea: a short divergence sweep plus 3-4 targeted convergence rounds.
- **Novelty stall** = two consecutive exchanges adding no new category of change.
  On stall: burst or converge — don't grind the same ground.
- When any single thread nears **~6 exchanges**, consolidate it to a clean board
  entry and continue from the restatement — long fragmented threads measurably
  degrade, and a clean restatement restores what the winding thread loses.
- Convergence rounds **force an axis** because "improve it" rounds produce
  drift, then repetition, then collapse.

## The idea board

One line per idea, restated at every phase transition:

```
[name] · one-line pitch · live|parked|dead · from: user|seed|burst:lens [· amended: …] [· dead at: stage — reason]
```

The board is the working memory and the resume trail. Dead ideas stay listed
with their kill stage and reason — ideators receive the full board precisely so
nothing dead is accidentally resurrected, and nothing parked is re-pitched as
new. Finalists additionally carry a **card** (pitch, rough shape, accumulated
amendments, vetting results): the board line stays one line, and the card —
mirrored into the draft shortlist file — is what vetting lenses receive.

## Choosing ideator lenses

Defaults, one per burst ideator — adjust to the range, never assign two ideators
the same lens:

- **user-pain** — start from real frictions: complaints, workarounds,
  abandonments.
- **adjacent-possible** — what neighboring products and domains do well that
  this range could absorb.
- **contrarian** — invert an assumption the project (or the board) takes for
  granted; the idea is what the inversion unlocks.
- **tech-leverage** — what recently became cheap or possible that makes a
  previously hard feature easy here.

## Handling subagent results

- **Scout** returns a brief — fold the facts into your seeds and questions. If it
  found little, proceed; don't re-dispatch to fish.
- **Ideators** return candidates — dedupe against the board by name *and*
  mechanism, feed the best 2-3 into the conversation, park the rest. Never paste
  raw subagent output at the user.
- **Vetting lenses** return verdicts with evidence — judge the verdicts; don't
  redo the research. A kill that arrives without rescue analysis is an
  incomplete report: send it back once. A demand number without a confidence
  label is fabricated precision: don't repeat it to the user without one.

## Shortlist location & commits

- Shortlists live in the shared Repertoire docs namespace:
  `docs/repertoire/ideas/YYYY-MM-DD-<topic>-ideas.md` — beside Libretto's
  `specs/` and Score's `plans/`. (User preference overrides.)
- Start the file as a **draft** when finalists are agreed (mark it `DRAFT` in
  the header) — it doubles as the on-disk resume trail through the funnel —
  and finalize it at Land.
- Stage and commit **only the shortlist file** (`git add` that path
  specifically; never `-A` — the working tree may hold unrelated changes): on
  first write, after funnel rounds that materially change it (amendments,
  drops), and after the champion gate. If the project isn't a git repository,
  write the file, skip the commits, and say so. Eureka writes only the
  shortlist document — it never touches source.

## Red flags

- Opening Diverge with a blank "any ideas?" → you brought no material to react
  to.
- Deepening one idea while still generating others → modes mixed; both suffer.
- Converging at the first stall without offering a burst → the creative cliff
  just claimed the best ideas.
- An idea dying on a subagent's verdict alone → only the user kills ideas;
  raise the AskUserQuestion.
- Vetting lenses dispatched in parallel, or the next lens launched while a kill
  is unresolved → the funnel's economy and its amendments both break.
- Quoting a demand percentage without its confidence label → fabricated
  precision.
- A kill gate built with four-plus rescue options, or a slot spent on "Other" →
  the tool caps options at four and adds "Other" itself; trim to three rescues
  plus *confirm drop*.
- Reading the codebase yourself → that's the scout's job (or feasibility's, in
  Phase 4).
- Writing a spec, a plan, or code — or invoking Libretto after the handoff →
  the shortlist is the finish line; hand off and stop.

## Bundled files

- `scout-prompt.md` — read-only Explore terrain scout: identity brief, feature
  inventory, friction, constraints, banked ideas.
- `ideator-prompt.md` — generative burst ideator, lens-parameterized; 3-4
  dispatched in parallel, blind to each other.
- `vetting-prompt.md` — evaluative vetting lens, parameterized over the four
  funnel lenses; dispatched sequentially, each covering all surviving finalists.
