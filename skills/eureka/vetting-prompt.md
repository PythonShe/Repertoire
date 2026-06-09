# Vetting Lens Prompt (evaluative, lens-parameterized, sequential)

One template for the four funnel lenses. Unlike the ideator burst, these run
**sequentially** — judgment → law → market → engineering — each a fresh **Fable 5**
subagent covering **all surviving finalists** in one dispatch. One lens across
all finalists yields coherent relative judgments; four agents per idea would
not. Dispatch the next lens only after the user has resolved every kill the
current lens proposed — downstream lenses must see the rescoped variants, and
research spent on an idea whose fate is open is research wasted.

Fixed order, and why:

1. `identity` — cheapest: judges against the identity brief, no research.
2. `compliance` — targeted web research; produces hard, non-negotiable kills.
3. `demand` — the broadest, most expensive research; produces gradients.
4. `feasibility` — the only lens that may read the codebase (anchored ranges).

A kill verdict is a **motion, not a sentence**: the user — not you, not the
lens — decides whether an idea dies. That is why every kill must carry rescue
analysis (see below), and why every kill is confirmed with the user — batched
per SKILL.md Phase 4 — before the funnel moves on. If the user proposes a
rescue the lens never considered, use the **re-judgment dispatch** at the
bottom of this file: once per idea per lens, and its verdict is final for the
run.

```
Agent tool (model: fable, effort: high):
  description: "Vet finalists — lens: [LENS]"
  prompt: |
    You are a vetting lens in an idea-discovery funnel. Your lens for this
    pass is:

    ## Lens: [LENS]

    Judge every finalist below through this one angle only — the other lenses
    run separately.

    ## Identity brief

    [2-3 sentences — what the project is, who it serves, what it refuses
    to be, including any user-approved amendments from earlier in the funnel]

    ## Range & dimension

    [the talking range and chosen focus dimension(s)]

    ## Finalists (with amendments from earlier funnel stages)

    [paste each finalist's card: name — pitch — rough shape — accumulated
    amendments such as "EU excluded from target range" or "descoped to MVP
    cut X"]

    ## Lens contracts — find yours, apply only it

    - **identity** — Does each finalist belong in THIS project, per the
      identity brief? Not "is it good" — "is it ours". Verdicts: `fits` /
      `stretch` (fits, with a note on the tension) / `misfit` (kill).
    - **compliance** — Does each finalist trip legal or policy wires: data
      protection (GDPR/CCPA), licensing of dependencies or content, platform
      policies (app stores, API terms), regulated verticals (health, finance,
      minors), security exposure? Research what you are unsure of; cite
      sources. Verdicts: `none` / `caution` (note what to watch) / `blocker`
      (kill).
    - **demand** — Who actually needs each finalist, and roughly what
      percentage of the target user base? Ground the estimate in evidence —
      forums, issue trackers, competitor adoption, surveys — cite sources, and
      label confidence (low/med/high). A grounded range beats fake precision;
      never invent a number. Fold in prior art: what already serves this need
      and what gap remains. Verdicts: demand estimate + gap note / `no-gap`
      (kill — the need is already fully served).
    - **feasibility** — How hard is each finalist to actually build? A sizing
      guess is enough. On an anchored range, read the relevant code first.
      Verdicts: t-shirt size `XS`-`XL` + one-paragraph rationale + a
      maintenance-burden note / `infeasible` (kill — only when no realistic
      cut could ship).

    ## Every kill carries rescue analysis

    For each kill you propose, also state the *narrowest* change that would
    dissolve your objection — a scope cut (exclude a region, drop a data
    flow), an identity-brief amendment, a differentiation angle, an MVP
    descope — or state plainly: "no rescue exists." The user decides; your
    verdict is a motion, not a sentence. A kill without rescue analysis is an
    incomplete report and will be sent back.

    ## Report format (compact, per finalist)

    One block per finalist, no prose padding:

    - **[Idea name]** · verdict · decisive evidence in one or two lines
      [· rescue: the narrowest dissolving change, or "no rescue exists"]

    Cite URLs for demand and compliance claims. Cap evidence at what actually
    decides the verdict — the conductor stays lean.
```

## Re-judgment dispatch (single idea, same lens, once per run)

When the user proposes a rescue the lens never considered, re-dispatch the same
lens for that one idea. The re-dispatched agent is fresh and stateless — it
remembers nothing of its first pass — so paste the history it must argue
against. Its verdict is final for this run: `dissolved` → the idea continues
with the amendment; `re-kill` → one last user gate, *confirm drop* or
*overrule* (see SKILL.md Phase 4).

```
Agent tool (model: fable, effort: high):
  description: "Re-judge [IDEA NAME] — lens: [LENS]"
  prompt: |
    You are the [LENS] vetting lens in an idea-discovery funnel, re-judging ONE
    idea that a prior dispatch of your lens proposed to kill. Decide whether
    the user's proposed rescue dissolves the objection.

    ## Identity brief

    [2-3 sentences, with any user-approved amendments]

    ## The idea (card, with amendments so far)

    [name — pitch — rough shape — accumulated amendments]

    ## Your prior verdict

    [the kill verdict and its evidence, as reported by the first dispatch]

    ## The user's proposed rescue

    [what the user proposed, verbatim]

    ## Lens contract

    [paste the matching lens contract from the main template above]

    Judge only whether the rescue dissolves YOUR lens's objection — not whether
    the idea is good; the other lenses run separately. This re-judgment is
    final for the run, with no second rebuttal — so be fair in both directions:
    if the rescue genuinely cures the objection, say `dissolved` without
    face-saving; if it doesn't, say `re-kill` and state plainly why not.

    ## Report format

    - **Verdict:** `dissolved` — the amendment to record, in one line | `re-kill`
      — why the rescue does not cure it, in one or two lines
```
