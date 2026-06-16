# Fixer Prompt Template

Dispatch a fresh **Opus** subagent to repair a set of review findings. Used in
Phase 1 (general-review findings), Phase 2 (consolidated panel findings — one fixer
for everything, or one per issue run sequentially), and Phase 3 (QC findings).

```
Agent tool (model: opus, effort: xhigh):
  description: "Fix findings on [group/branch]"
  prompt: |
    You are fixing specific, already-identified defects. The diagnosis is done —
    your job is the repair.

    ## Findings to fix

    [Paste the findings: each with file:line, severity, and the one-line
    description. For a per-issue fixer, paste only that one issue.]

    ## Context

    [Where this lives and anything you need to understand the surrounding code.]

    Work from: [directory]   Branch: [branch name]
    Test command: [TEST — how to run the tests]

    Before you commit, confirm you are on the named branch.

    ## Your job

    1. Fix exactly the findings listed — and only those. Do not refactor beyond them
       or add features (YAGNI). If a finding looks wrong or you disagree with it, say
       so in your report rather than fixing it incorrectly.
    2. Keep or add tests that prove each fix works. Run `[TEST]` and confirm it
       passes — including the surrounding tests, so you do not break anything else.
    3. **Commit your work.** You have full authority to commit, and you should: one
       clear commit per fix (or per logical group of fixes), referencing the finding
       it resolves.

    ## Report format (compact — the controller stays lean)

    Under ~10 lines, no diffs.
    - **Status:** FIXED | PARTIALLY_FIXED | COULD_NOT_FIX
    - Per finding: what you changed (one line) and the commit SHA.
    - Anything you chose not to fix, with the reason.
    - Test result.

    If a finding could not be fixed, say why — that goes back into the next round.
```
