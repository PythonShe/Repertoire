# Plan Writer Prompt (plan sets only)

Dispatched in Phase 3 only when the structure checkpoint approved a **plan
set** — one fresh subagent per plan file, all **in parallel**, **unpinned**
(build seats inherit the session model). A single plan is authored by you and
never delegated, and it gets no overview file. Writers write exactly one file
each and never commit; you reconcile the manifests and commit the whole
directory.

Fill every `[SLOT]` with resolved content — paste the plan's approved
structure slice from the checkpoint (its movements and file map), never a
vague topic. Resolve `[PLAN_TEMPLATE_PATH]` to this skill's `plan-template.md`
via `${CLAUDE_PLUGIN_ROOT}/skills/score/plan-template.md`.

```
Agent tool:
  description: "Write plan [NN]: [slug]"
  prompt: |
    You are writing ONE implementation plan file of a coordinated multi-plan
    set. Sibling writers are producing the other plans in parallel; you write
    exactly your file and nothing else — no commits, no edits elsewhere.

    ## Your plan

    - File to write: [PLAN_FILE_PATH, e.g. docs/repertoire/plans/2026-07-11-<topic>/plan-02-<slug>.md]
    - Delivers: [one paragraph: what this plan builds and why it stands alone]
    - Approved structure — follow it, do not restructure:
    [PASTE this plan's checkpoint-approved slice: movements, task outline,
    file map]

    ## Read before writing

    - The spec: [SPEC_FILE_PATH — or "no written spec; the clarified
      requirements follow:" + paste them]
    - The set overview: [OVERVIEW_PATH] — the cross-plan contracts. Interfaces
      and shapes it defines are law: consume them exactly as written, and
      restate any you consume inside the task that uses them.
    - The plan template: [PLAN_TEMPLATE_PATH] — follow its structure exactly,
      and add an `**Overview:** `00-overview.md`` line to the header so a
      conductor finds the contracts.

    ## Codebase context (from the scout)

    [PASTE the scout findings that touch this plan's scope: touch points,
    build/test commands, conventions, constraints]

    ## Decision-complete, not code-complete

    Your plan will be executed by a fresh implementer, one movement at a time,
    with zero conversation context and no sibling plan in sight — every
    decision it needs must be in your file or the overview. Make every
    decision: exact paths, spelled-out interfaces and data shapes, named tests
    with their key assertions, exact verify commands, build/test commands in
    the header. Stop short of function bodies — full code blocks for routine
    work are implementation in disguise.

    Never write:
    - "TBD", "TODO", "decide later", "fill in details".
    - "Add appropriate error handling / validation" — name each failure mode
      and what handles it.
    - "Write tests for the above" — name each test and the behavior it pins.
    - "Similar to Task N" / "see plan 01" — repeat the interface at the point
      of use; tasks are read in isolation.
    - References to types, functions, or signatures defined nowhere in your
      plan, the overview, or the existing codebase.

    Stay inside your scope. If work you need belongs to a sibling plan,
    consume its interface from the overview rather than planning it yourself.
    If the overview is missing a contract you need, note it in your report —
    never invent a global decision locally.

    Write the file, then report compactly (no restating the plan):

    - **Written:** path · N movements · M tasks
    - **Defines:** each public interface this plan introduces — exact
      signature or shape
    - **Consumes:** each cross-plan interface used, and the exact shape you
      relied on (from the overview or a named sibling plan)
    - **Assumptions / missing contracts:** anything you had to assume, or a
      gap in the overview — one line each, or "none"
```

The manifests are your reconciliation input: every **Consumes** shape must
match its definition exactly, and a **missing contract** means the overview
needs an edit — yours to make — before the review panel runs.
