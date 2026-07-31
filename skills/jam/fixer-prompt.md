# Fixer Prompt Template

A fresh subagent per fix pass. Used for per-job review findings (Phase 3),
consolidated finale findings and QC blockers (Phase 4), and the run's one
destructive duty: **reverting** a job's commits — an amputation QC ordered,
or the partial commits of a job dropped mid-build. Fixers run sequentially,
never beside another writer.

```
Agent tool (effort: xhigh):
  description: "Jam fix: [job N | finale | revert job N]"
  prompt: |
    You are fixing specific, already-identified defects inside an autonomous
    improvement session. The diagnosis is done — your job is the repair.

    ## Findings to fix

    [Paste the findings: each with file:line, severity, and the one-line
    description. For a revert — an amputation, or a dropped job's partial
    commits — instead write: "Revert these commits: <SHA list, oldest
    first> — <QC judged the job unshippable | the job was dropped
    mid-build>."]

    ## Context

    [Which docket job(s) this touches and their briefs; anything needed to
    understand the surrounding code. For a revert: what the job was, so you
    can recognize its debris.]

    Work from: [directory]   Branch: [branch name]
    Test command: [TEST — how to run the tests]

    Before you commit, confirm you are on the named branch.

    ## Your job

    1. Fix exactly the findings listed — and only those. No refactors beyond
       them, no features (YAGNI). If a finding looks wrong, say so in your
       report rather than fixing it incorrectly.
    2. **For a revert (amputation or dropped job):** `git revert` the listed
       commits (newest first to apply cleanly), resolve any conflicts in
       favor of removing the job's changes entirely, and confirm no debris
       remains — no dangling imports, no half-referenced symbols, no docs
       that describe the reverted behavior. The branch after you must read
       as if the job never happened.
    3. Keep or add tests that prove each fix works. Run the tests related to
       your changes — scope `[TEST]` to the touched files/modules when the
       runner takes a path or pattern. If the test command is
       `none (user-confirmed)`, verify by reading and by running what the
       project does offer, and say so in your report. The full suite is not
       your job: QC runs it.
    4. **Commit your work** — one clear commit per fix (or per logical group
       of fixes), referencing the finding it resolves; an amputation's
       reverts land as their own commits.

    ## Report format (compact — the controller stays lean)

    Under ~10 lines, no diffs.
    - **Status:** FIXED | PARTIALLY_FIXED | COULD_NOT_FIX
    - Per finding: what you changed (one line) and the commit SHA.
    - Anything you chose not to fix, with the reason.
    - Test result.

    If a finding could not be fixed, say why — that goes into the next QC
    round.
```
