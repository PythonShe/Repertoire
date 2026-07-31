# Guidance Doc Template

The scaffold for the target project's `docs/repertoire/animation.md` —
standing motion guidance that future sessions (Legato runs, Maestro
implementers, any agent touching UI) read before proposing or building
motion. Phase 7 creates it from this template on a first run and updates it
in place on every later one.

**The contract: this doc states what *is*, never the history of how it got
there.** A changed decision supersedes its old row; a rejected direction is
recorded once, in its own section, so no future session re-proposes it. If
the doc is growing run over run without decisions changing, something is
appending history — stop and prune.

```markdown
# Animation guidance — <project name>

Standing motion guidance for this project, maintained by Repertoire/Legato
runs. Decisions here are settled: agents extend them, they do not re-argue
them. A human editing this file wins over any run.

Last updated: <YYYY-MM-DD> · <`git rev-parse --short HEAD` when this doc was written>

## Motion language

- **Personality:** <crisp and fast | warm and playful | …> — one line on the
  product's character that motion must match.
- **Tokens:** <where easing/duration tokens live, e.g. `src/styles/tokens.css`>
  - `--ease-out: cubic-bezier(0.23, 1, 0.32, 1)` — UI entrances/exits
  - <each token · what it is for>
- **Library:** <the motion library in use, or "CSS only">, and when to reach
  for it vs. plain transitions.
- **Reduced motion:** <the project's pattern, e.g. "media query per
  component, keep opacity, drop travel">.

## Decisions by surface

One row per surface. Supersede in place; never append a second row for the
same surface.

| Surface | Motion | Values | Why | Decided |
| --- | --- | --- | --- | --- |
| <page/widget, e.g. Settings modal> | <what it does, e.g. scale+fade enter from trigger> | <exact values, e.g. `scale(0.97)→1`, 200ms `--ease-out`> | <one line> | <YYYY-MM-DD> |

## Rejected directions

Directions considered and turned down, so they are not proposed again. One
line each: surface · direction · why it was rejected.

- <e.g. Command palette · open/close animation · keyboard-triggered, 100+/day — never animate>

## Open seams

Known gaps deliberately left for later, one line each — or remove this
section when empty.
```

Notes for the conductor filling this in:

- Values in the table are exact — the same copied-never-approximated rule as
  everywhere else in Legato.
- The `Decided` column is a date, not a narrative; when a decision changes,
  the row's values and date change and nothing else is added.
- On a first creation, ask the user (one AskUserQuestion) whether to add a
  one-line pointer to this doc in the project's `CLAUDE.md`, its
  `AGENTS.md`, or neither — never edit those files without that consent. A
  suitable pointer: `Motion/animation decisions live in
  docs/repertoire/animation.md — read it before proposing or changing UI
  motion.`
