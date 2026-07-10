# Codex Reviewer (cross-model plan review)

Compose this stub with the shared contract in `shared/codex-reviewer-core.md`
— the conductor reads both and assembles one brief. The core carries the
invocation mechanics; this stub carries what to review.

**Object under review:** the whole implementation plan against its spec — a
**general** pass that complements the single-lens Opus reviewers rather than
repeating one of them; it catches the underspecified task and the unstated
dependency a room full of Claudes will read right past. Run the call **in the
same batch** as the Opus reviewer dispatches so they all run together. This
is a document review: no `--base` flag; keep both full paths in the prompt so
the runtime knows which files to read. If no written spec exists (the
clarify-pass branch), drop the "against the spec at …" clause and fold the
clarified requirements into the `[one line]` summary instead. If Codex is
unavailable, run the Opus lenses only and say so in your report.

**Runtime review prompt** (substitute `[PLAN_FILE_PATH]`, `[SPEC_FILE_PATH]`,
and the `[one line]` summary before sending):

    review the implementation plan at [PLAN_FILE_PATH] against the spec at
    [SPEC_FILE_PATH] for gaps that would derail a fresh implementer: spec
    requirements with no task, undefined interfaces or placeholder steps,
    tasks out of dependency order, and scope the spec never asked for:
    [one line on what the plan should deliver]

**Issues location format:** `[Movement N, Task N.M] · severity · one-line gap`.

**Fallback `codex exec` prompt** (replace `[PLAN_FILE_PATH]`,
`[SPEC_FILE_PATH]`, and the paste block before sending; with no written spec,
set the spec line to `Spec file: none — requirements follow` and put the
clarified requirements in the paragraph block):

```text
You are an adversarial, cross-model reviewer of an implementation plan. Break
confidence in it. Read the plan and its spec in full, then assume the plan is
flawed until it proves otherwise. It will be executed by fresh implementers who
each receive one task group and zero conversation context. Hunt for the
expensive failures: spec requirements no task implements, tasks that reference
types or interfaces defined nowhere, placeholder steps ("add appropriate error
handling", "similar to task N"), tests too vague to write, tasks out of
dependency order, and scope the spec never asked for. Report only material
gaps; one strong finding beats five weak ones.

Plan file: [PLAN_FILE_PATH]
Spec file: [SPEC_FILE_PATH]
This plan is meant to deliver:
[PASTE one paragraph: the goal and the chosen approach]

Output ONLY (same shape as the other reviewers):
- Verdict: PASS | FAIL
- Issues: [Movement N, Task N.M] · severity · one-line gap and why it matters
No rewrites, no restating the plan.
```

You fold only the compact verdict into the consolidated findings — then revise
the plan yourself and commit.
