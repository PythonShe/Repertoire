# Codex Reviewer (cross-model spec review)

Compose this stub with the shared contract in `shared/codex-reviewer-core.md`
— the conductor reads both and assembles one brief. The core carries the
invocation mechanics; this stub carries what to review.

**Object under review:** the whole committed spec — a **general** pass that
complements the single-lens Opus reviewers rather than repeating one of them;
it catches the ambiguity and the unstated assumption a room full of Claudes
will read right past. Run the call **in the same batch** as the Opus reviewer
dispatches so they all run together. This is a document review: no `--base`
flag; keep the spec's full path in the prompt so the runtime knows which file
to read. If Codex is unavailable, run the Opus lenses only and say so in your
report.

**Runtime review prompt** (substitute `[SPEC_FILE_PATH]` and the `[one line]`
summary before sending):

    review the design spec at [SPEC_FILE_PATH] for gaps, contradictions,
    ambiguity, and scope creep that would mislead an implementer: [one line
    on what the spec should deliver]

**Issues location format:** `[Section] · severity · one-line gap`.

**Fallback `codex exec` prompt** (replace `[SPEC_FILE_PATH]` and fill the
paste block before sending):

```text
You are an adversarial, cross-model reviewer of a design spec. Break confidence in
it. Read the spec file in full, then assume it is flawed until it proves otherwise.
Hunt for the expensive failures: requirements vague enough to build the wrong
thing, sections that contradict each other, undefined interfaces or data shapes,
unstated assumptions, missing error/edge-case handling, and scope that should have
been cut or decomposed. Report only material gaps; one strong finding beats five
weak ones.

Spec file: [SPEC_FILE_PATH]
This spec is meant to deliver:
[PASTE one paragraph: the idea and the agreed approach]

Output ONLY (same shape as the other reviewers):
- Verdict: PASS | FAIL
- Issues: [Section] · severity · one-line gap and why it matters
No rewrites, no restating the spec.
```

You fold only the compact verdict into the consolidated findings — then revise
the spec yourself and commit.
