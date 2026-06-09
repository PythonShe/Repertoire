# Fixer Prompt Template

Dispatch a fresh **Fable 5** subagent per picked call — or one for a few
interrelated calls. Fixers write to the branch, so they run **sequentially**,
never in parallel. The fixer implements the *verifier's directive*, which is
authoritative even where it deliberately differs from the hunter's original
sketch (ADJUSTED verdicts).

```
Agent tool (model: fable, effort: high):
  description: "Play [E-id]: [short gist]"
  prompt: |
    You are implementing an already-verified enhancement on an encore
    branch. The investigation is done: a read-only verifier confirmed the
    opportunity and wrote the directive below, and the user explicitly
    picked this call for implementation. The directive is authoritative —
    where it differs from the hunter's original sketch, follow the
    directive.

    ## Directive

    [Paste the verifier's directive: what to change, where, and the
    acceptance check. For multiple calls, paste each with its E-id.]

    ## Original call (context)

    [The board entry/entries: E-id, lens, title, anchors, now,
    enhancement, payoff.]

    Work from: [directory]   Branch: [encore branch name]
    Test command: [TEST]

    Before you commit, confirm you are on the named branch.

    ## Your job

    1. Implement exactly the directive — no refactors beyond it, no bonus
       improvements, nothing outside the stated scope. An encore plays the
       piece better; it does not smuggle in a new movement. If the
       directive turns out to be wrong once you are in the code, say so in
       your report rather than improvising a different change.
    2. This is finished, working code: preserve observable behavior except
       where the directive explicitly changes it. Keep or add the test the
       acceptance check names. Run [TEST] and confirm it passes — including
       the surrounding tests, so you break nothing the audience already
       relies on.
    3. **Commit your work**: one clear commit per call, in the repo's
       commit convention, referencing the call it plays (e.g. "refactor:
       dedupe export writers — encore E3"). Stage only the files your
       directive touched — an updated dossier file may sit uncommitted in
       the worktree; leave it out of your commit.

    ## Report format (compact — the controller stays lean; under ~10 lines)

    - **Status:** DONE | PARTIALLY_DONE | COULD_NOT_DO
    - Per call: the commit SHA + one line on what changed, phrased for the
      dossier's outcome column.
    - Test result (the summary line).
    - Anything not done, and why — that feeds the re-review brief and an
      honest dossier entry.
```
