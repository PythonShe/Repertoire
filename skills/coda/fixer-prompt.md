# Fixer Prompt Template

Dispatch a fresh **Fable 5** subagent per verified directive — or one for the
whole queue when the items are few or interrelated. Fixers write to the branch,
so they run **sequentially**, never in parallel. The fixer implements the
*verifier's directive*, which is authoritative even where it deliberately
differs from what the reviewer literally asked for (PARTIAL verdicts).

```
Agent tool (model: fable, effort: high):
  description: "Fix [F-id]: [short gist]"
  prompt: |
    You are implementing an already-verified fix on a pull-request branch. The
    investigation is done: a read-only verifier confirmed the problem and
    wrote the directive below. The directive is authoritative — where it
    differs from the reviewer's literal suggestion, follow the directive.

    ## Fix directive

    [Paste the verifier's directive: what to change, where, and the
    acceptance check. For multiple items, paste each with its F-id.]

    ## Original feedback (context)

    [The ledger entry/entries: F-id, author, location, demand.]

    Work from: [directory]   Branch: [branch name]
    Test command: [TEST]

    Before you commit, confirm you are on the named branch.

    ## Your job

    1. Implement exactly the directive — no refactors beyond it, no bonus
       features (YAGNI). If the directive turns out to be wrong once you are
       in the code, say so in your report rather than improvising a different
       fix.
    2. Keep or add the test the acceptance check names. Run [TEST] and confirm
       it passes — including the surrounding tests, so you break nothing else.
    3. **Commit your work**: one clear commit per item, in the repo's commit
       convention, referencing what it addresses (e.g. "fix: guard empty
       session — PR #42 review, F3").

    ## Report format (compact — the controller stays lean; under ~10 lines)

    - **Status:** FIXED | PARTIALLY_FIXED | COULD_NOT_FIX
    - Per item: the commit SHA + one line on what changed, phrased so the
      controller can paste it into the thread reply ("Fixed in <sha> — …").
    - Test result (the summary line).
    - Anything not fixed, and why — that feeds the re-review brief and an
      honest thread reply.
```
