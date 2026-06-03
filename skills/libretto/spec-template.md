# Spec Template

The structure Libretto writes specs in. Scale each section to its complexity — a
sentence where the answer is obvious, a few paragraphs where it isn't — and drop a
section outright if it genuinely doesn't apply. A small spec is *short*, not
padded with empty headings. The goal is a document someone could build from
without having to ask you what you meant.

```markdown
# <Title>

## Problem & context
What we're solving and why it matters now. The current situation and what's
wrong (or missing) about it.

## Goals / non-goals
- **Goals** — what success looks like, as concrete bullets.
- **Non-goals** — what we are deliberately *not* doing. This is the YAGNI
  guardrail; an empty non-goals list is a warning sign.

## Approach
The chosen design, and one or two sentences on why it beat the alternatives you
considered. The reader should understand not just *what* but *why this and not
that*.

## Components & interfaces
Each unit, kept small and well-bounded. For each: what it does, how it's used
(its interface), and what it depends on. Someone should be able to understand a
unit without reading its internals.

## Data flow
How data moves through the system, the key pieces of state, and where they live.

## Error handling & edge cases
The failure modes that matter and how each is handled. The edge cases worth
calling out before they surprise the implementer.

## Testing strategy
How we'll know it works — what to test, at what level, and what "done" proves.

## Open questions / assumptions
The assumptions you made while designing (stated plainly so reviewers can
challenge them) and anything still genuinely unresolved.

## Out of scope
Work deliberately deferred or excluded, each with a one-line reason.
```
