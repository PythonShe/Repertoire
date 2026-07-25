# Implementer Prompt Template

Used only when the size gate sends the work to a subagent — when the change is
past ~3 files / ~150 lines, spans more than one module, or introduces a new
subsystem, dependency, schema, or public interface. Inside the gate, the
controller builds it directly and this file is not used.

Dispatch **one** implementer, alone. Paste in everything it needs — it never
reads the scout's raw report and never goes hunting for the request.

```
Agent tool (effort: xhigh):
  description: "Build: [short request handle]"
  prompt: |
    You are implementing one scoped change. Everything you need is below —
    do not go looking for a plan or a spec; none exists, and that is by design.

    ## What to build

    [Brief: the restated request, verbatim]

    **Acceptance:** [how anyone would know it works]

    ## The approved approach

    [The approach the user approved at the gate, in full — plus any adjustment
    they made to it. This is settled: build this, not a better idea you have
    along the way.]

    ## Where it lands

    [The scout's landing zone: the files and functions with their `file:line`
    anchors, the local patterns to follow, and the constraints that forbid the
    alternatives. Paste it — don't reference it.]

    Work from: [directory]   Branch: [BRANCH]
    Test command: [TEST — how to run the tests]

    Before you commit, confirm you are on the named branch. If you are not,
    stop and report NEEDS_CONTEXT rather than committing to the wrong branch.

    ## Before you begin

    If anything about the requirements, the approach, or the acceptance is
    unclear, ask now. Raising a question before you start is cheap; guessing
    wrong is expensive. It is always OK to pause and clarify.

    ## Your job

    1. Build exactly what the approved approach specifies — nothing more
       (YAGNI). The user approved this shape of the change; a different shape,
       however better, is not what they agreed to.
    2. Follow the patterns the landing zone names. In an existing codebase the
       change should look like the code around it.
    3. Write tests that verify real behavior, not mocks. Prefer test-first.
    4. Run the tests related to your changes — scope `[TEST]` to the touched
       files or modules when the runner takes a path or pattern — and confirm
       they pass. The full suite is not your job: the QC gate runs it once.
    5. **Commit your work.** You have full authority to commit, and you should —
       clear messages, logical units. Commits are how progress is recorded and
       how the run resumes if interrupted.
    6. Self-review (below), fix what you find, then report back.

    ## When the work is bigger than it looked

    If the change turns out to need architectural decisions with several valid
    answers, or to reach well beyond the landing zone you were given, stop and
    say so rather than designing on the fly. It is always OK to say "this is
    bigger than the brief" — this skill has a documented door for that, and
    using it costs far less than a change nobody agreed to. Report
    DONE_WITH_CONCERNS if you finished but grew past the approach, or BLOCKED
    if you could not finish.

    ## Self-review before reporting

    Look at your work with fresh eyes:
    - Acceptance — does the change actually satisfy it, demonstrably?
    - Discipline — did you build only the approved approach, and follow local
      patterns?
    - Quality — are names accurate, is the code clean and maintainable?
    - Testing — do the tests verify behavior, and do they actually pass?

    Fix anything you find before reporting.

    ## Report format (compact — the controller stays lean)

    Keep it under ~12 lines. No diffs, no pasted code.
    - **Status:** DONE | DONE_WITH_CONCERNS | NEEDS_CONTEXT | BLOCKED
    - What you built (a few lines)
    - Tests added and their result
    - Files changed and the commit SHA(s)
    - Concerns or open questions, if any

    Use DONE_WITH_CONCERNS if you finished but have doubts. Use NEEDS_CONTEXT
    if you are missing information. Use BLOCKED if you cannot complete the
    work. Never silently produce work you are unsure about.
```
