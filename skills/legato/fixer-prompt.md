# Fixer Prompt Template

Dispatch a fixer when the reviewer returned findings, or when QC returns a
`[defect]` blocker. One fixer at a time, never alongside another writer. It
fixes everything in one pass; Legato does not re-review a fix — anything
disputed goes to the QC brief.

```
Agent tool (effort: xhigh):
  description: "Fix motion findings: [short surface handle]"
  prompt: |
    You are fixing specific, already-identified defects in a motion change.
    The diagnosis is done — your job is the repair, not a fresh review.

    ## Findings to fix

    [Paste the findings table verbatim: location, severity, Before, After,
    Why. The After column is the exact target — implement it as written,
    with those values, not an approximation of them. Paste all findings —
    this is the only fix pass.]

    ## What the change was supposed to do

    [Brief + acceptance + the approved motion spec, so you can tell a defect
    from a deliberate decision.]

    ## Context

    [The inventory rows and conventions for the touched surface, and any
    record decisions that constrain the fix.]

    ## The reference bar

    - [absolute path to skills/legato/references/STANDARDS.md]

    When a fix needs a value the findings table does not state, take it from
    there — never approximate a value that file states exactly.

    Work from: [directory]   Branch: [BRANCH]
    Test command: [TEST — how to run the tests]

    Before you commit, confirm you are on the named branch.

    ## Your job

    1. Fix exactly the findings listed — and only those. No refactors beyond
       them, no extra polish (YAGNI). If a finding looks wrong — the Before
       is not what the code says, or the After would violate the spec — say
       so in your report rather than fixing it incorrectly; a disputed
       finding goes to the QC gate with your reasoning attached, and that is
       a real outcome, not a failure.
    2. Keep the craft rules intact while you repair: GPU-only properties,
       tokens extended not duplicated, reduced motion and hover gating
       preserved.
    3. Run `[TEST]` scoped to what you touched, including the surrounding
       tests, so you do not break anything else. The full suite is QC's job.
    4. **Commit your work** — one clear commit per fix or logical group,
       referencing the finding it resolves.

    ## Report format (compact — the controller stays lean)

    Under ~10 lines, no diffs.
    - **Status:** FIXED | PARTIALLY_FIXED | COULD_NOT_FIX
    - Per finding: what you changed (one line) and the commit SHA.
    - Anything you disputed or chose not to fix, with the reason.
    - Test result.
```
