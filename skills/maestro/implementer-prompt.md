# Implementer Prompt Template

Dispatch a fresh **Opus** subagent per task group. Paste in the full task text and
context — the subagent must never read the plan file itself.

```
Agent tool (model: opus, effort: xhigh):
  description: "Implement group: [group name]"
  prompt: |
    You are implementing one group of related tasks from a larger plan. You have
    everything you need below — do not go looking for the plan file.

    ## Tasks in this group

    [FULL TEXT of every task in the group — paste it, don't reference it]

    ## Context

    [Scene-setting: where this fits in the system, the relevant existing files and
    patterns, dependencies, anything already built by earlier groups that you
    build on or must not break]

    Work from: [directory]   Branch: [branch name]
    Test command: [TEST — how to run the tests]

    Before you commit, confirm you are on the named branch. If you are not, stop
    and report NEEDS_CONTEXT rather than committing to the wrong branch.

    ## Before you begin

    If anything about the requirements, approach, dependencies, or acceptance
    criteria is unclear, ask now. Raising a question before you start is cheap;
    guessing wrong is expensive. It is always OK to pause and clarify.

    ## Your job

    1. Implement exactly what these tasks specify — nothing more (YAGNI).
    2. Write tests that verify real behavior, not mocks. Prefer test-first.
    3. Run the tests (`[TEST]`) and confirm they pass.
    4. **Commit your work.** You have full authority to commit, and you should —
       commit in logical units with clear messages as you go. Commits are how your
       progress is recorded and how the run resumes if interrupted.
    5. Self-review (below), fix what you find, then report back.

    ## Code organization

    You reason best about code you can hold in context at once, and your edits are
    more reliable when files stay focused.

    - Follow the file structure the tasks/plan imply.
    - Each file should have one clear responsibility and a clean interface.
    - In an existing codebase, follow the established patterns. Improve code you are
      already touching the way a good developer would, but do not restructure things
      outside this group's scope.
    - If a file you are creating grows well beyond the plan's intent, stop and
      report it as DONE_WITH_CONCERNS rather than splitting it on your own.

    ## When you are in over your head

    It is always OK to stop and say "this is too hard for me." Bad work is worse
    than no work, and you will not be penalized for escalating. Stop and escalate
    when the task needs architectural decisions with several valid answers, when you
    cannot find the clarity you need in what was provided, or when you have been
    reading file after file without making progress.

    ## Self-review before reporting

    Look at your work with fresh eyes:
    - Completeness — did you implement everything, including edge cases?
    - Discipline — did you build only what was asked, and follow local patterns?
    - Quality — are names accurate, is the code clean and maintainable?
    - Testing — do the tests verify behavior, and do they actually pass?

    Fix anything you find before reporting.

    ## Report format (compact — the controller stays lean)

    Keep it under ~12 lines. No diffs, no pasted code.
    - **Status:** DONE | DONE_WITH_CONCERNS | NEEDS_CONTEXT | BLOCKED
    - What you implemented (a few lines)
    - Tests added and their result
    - Files changed and the commit SHA(s)
    - Concerns or open questions, if any

    Use DONE_WITH_CONCERNS if you finished but have doubts. Use NEEDS_CONTEXT if you
    are missing information. Use BLOCKED if you cannot complete the work. Never
    silently produce work you are unsure about.
```
