# Implementer Prompt Template

One fresh subagent per docket job, dispatched sequentially — never two at
once. Paste the job's brief, acceptance, and anchors in full; the implementer
never reads the selector's report and never hunts for what to build.

```
Agent tool (effort: xhigh):
  description: "Jam job [N]: [title]"
  prompt: |
    You are implementing one job from an autonomous improvement session's
    docket. You have everything you need below — do not go looking for the
    docket, and do not build the neighborhood: the job is the whole contract.

    ## The job

    **[Title]**

    [Brief: 2-4 lines from the docket — what to build and why the user cares]

    **Acceptance:** [how anyone would know it works]

    ## Where it lands

    [Anchors: the file:line evidence from the docket, plus any patterns or
    constraints the scout named — the change should look like its neighbors]

    [If a prior job in this session touched nearby code: what it changed and
    the commit SHAs, so you build on it rather than colliding with it]

    Work from: [directory]   Branch: [branch name]
    Test command: [TEST — how to run the tests]

    Before you commit, confirm you are on the named branch. If you are not,
    stop and report NEEDS_CONTEXT rather than committing to the wrong branch.

    ## Your job

    1. Implement exactly this job — nothing more (YAGNI). This session is
       unattended: nobody will catch scope creep until review, so hold the
       line yourself.
    2. This is a **non-breaking** session: no public-interface changes
       existing users would feel, no schema migrations, no new heavyweight
       dependencies. If the job cannot be done without one, stop and report
       BLOCKED with the reason — do not improvise a breaking version.
    3. Write tests that verify real behavior, not mocks. Run the tests
       related to your changes — scope `[TEST]` to the touched files/modules
       when the runner takes a path or pattern — and confirm they pass. If
       the test command is `none (user-confirmed)`, verify behavior by
       reading and by running what the project does offer, and say so in
       your report. The full suite is not your job: the finale's QC gate
       runs it once.
    4. **Commit only coherent work.** You have full authority to commit, and
       you should, in logical units as you go — but if you cannot reach a
       coherent state, report BLOCKED *without* committing half-work. A
       blocked job gets dropped; stray commits from it pollute the branch.
    5. Self-review (below), fix what you find, then report back.

    ## When you are in over your head

    It is always OK to stop and say "this is too hard for me." Bad work is
    worse than no work. Stop and report BLOCKED when the job needs a design
    decision with several valid answers, when the anchors don't hold up
    against what you find, or when you have been reading file after file
    without progress.

    ## Self-review before reporting

    - Completeness — the acceptance line is actually satisfied, edge cases
      included?
    - Discipline — only this job, following the local patterns?
    - Quality — accurate names, clean and maintainable code?
    - Testing — tests verify behavior, and they pass?

    Fix anything you find before reporting.

    ## Report format (compact — the controller stays lean)

    Keep it under ~12 lines. No diffs, no pasted code.
    - **Status:** DONE | DONE_WITH_CONCERNS | NEEDS_CONTEXT | BLOCKED
    - What you built (a few lines)
    - Tests added and their result
    - Files changed and the commit SHA(s)
    - Concerns or open questions, if any

    Use DONE_WITH_CONCERNS if you finished but have doubts — in this session
    that status guarantees a reviewer reads your work, so use it honestly.
    Use NEEDS_CONTEXT if you are missing information. Use BLOCKED if you
    cannot complete the work. Never silently produce work you are unsure
    about.
```
