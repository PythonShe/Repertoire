# Authoring skill descriptions and prompts

How Repertoire writes the routing surface (frontmatter) and the subagent prompt
files. Read this before touching either. The evidence behind these rules comes
from the v2.0.0 auto-invoke flip (July 2026) and the mattpocock/skills study
that preceded it.

## The two-field routing surface

Since v2.0.0 every skill auto-invokes: Claude reads the skill listing and fires
the skill on matching requests. The listing is built from two frontmatter
fields:

- `description` — 1–2 sentences: what the skill does and to what object.
  Front-load the leading words (the nouns and verbs a matching request would
  contain). This is also what humans read in `/plugin` and the README.
- `when_to_use` — the trigger surface: one trigger per genuinely distinct
  branch, phrased as the requests users actually make. Collapse synonyms — two
  phrasings of the same intent are duplication, not coverage. End with the
  adjacency clause (see below).

**Hard cap: the combined `description` + `when_to_use` text is truncated at
1,536 characters in the skill listing.** Budget ≤ ~1,200 combined so edits
never silently fall off the end. This cap is not theoretical: the 1.x
MANUAL-ONLY descriptions of eureka (1,579) and encore (1,752) were silently
truncated for their entire lives, and nobody noticed — which is also evidence
that what got truncated was never load-bearing.

## Never write negative triggers

The 1.x descriptions guarded with negation: `Do NOT auto-trigger on generic
"execute this plan" requests`. Under auto-invoke this is worse than useless —
the guard literally embeds the attractor phrase in the routing surface, so the
matcher sees *execute this plan* inside the skill most likely to be confused
by it. Prompt the positive instead:

- Homonyms (score, coda, tuner, encore as ordinary words) are handled by
  concrete object nouns — "an approved spec", "an open GitHub pull request",
  "a reproducible bug" — not by listing the senses to avoid.
- Adjacent skills are handled by one positive territory clause that names the
  neighbor: "The code must already work: bugs go to tuner, open-PR feedback to
  coda." One clause per neighbor that real requests actually confuse.

## The eval loop

Every skill carries `skills/<name>/evals/evals.json`: flat
`{"query": …, "should_trigger": …}` cases. Three case families, all required:

1. **Positive triggers** — named invocations and the generic phrasings the
   skill now owns ("execute this plan" → maestro: true).
2. **Cross-skill confusion** — the neighbor's requests, expected false
   (score-vs-maestro, tuner-vs-coda-vs-encore, eureka-vs-libretto).
3. **Homonyms** — other senses of the word, expected false.

Run the skill-creator eval harness against these after ANY edit to
`description` or `when_to_use`. A description edit without an eval run is an
unreviewed routing change. Scratch output stays in the gitignored
`<skill>-workspace/`; the committed cases live in `evals/evals.json`.

## Cost gates

A skill whose pipeline dispatches subagents before any user gate must carry a
cost gate: if the user did not name the skill or run its slash command,
confirm scope and cost with one AskUserQuestion before the first dispatch.
Maestro, coda, and tuner carry this gate; eureka, libretto, score, and encore
do not need one because a dialogue or AskUserQuestion gate already precedes
their first expensive dispatch. A new skill that launches a fleet from Phase 0
needs the gate from day one.

## Writing prompt files (the leading-words rule)

For **new** prompt files, prefer a compact leading-word stance line over
multi-sentence posture prose: "adopt a red-team posture", "triage, not trace",
"evidence decides" each anchor a whole region of behavior in a few tokens.

Never compress these — they are protocol, not prose, and conductors parse
them as exact strings:

- report formats and status vocabularies (`FIXED | PARTIALLY_FIXED |
  COULD_NOT_FIX`, the `path:42 · severity · description` line grammar),
- command sequences and their flags (`gh api --paginate`, revert-conflict
  fallbacks),
- edge-case → verdict mappings and strike budgets.

Do not retro-compress the existing hardened prompt files: measurement (July
2026) put template text at ~1–2% of a run's token spend, the honest yield at
15–25% of that, and the odds of silently dropping a hardening clause well
above the value saved. Leading words recruit a distribution; conductors parse
exact strings.

## The no-op test and sediment

Before adding a line to a description, a SKILL.md, or a prompt: does it change
behavior versus the default? A line that restates what the model would do
anyway is sediment — it costs context on every load and buys nothing. When a
phase's behavior changes, hunt the copies: the frontmatter, the at-a-glance
checklist, the phase prose, and the prompt file are one contract and drift
between them is a bug (a graph/prose drift shipped in 1.x and was only caught
by audit).
