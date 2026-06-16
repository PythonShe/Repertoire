# Feedback Clerk Prompt (read-only harvest)

Dispatch one fresh **Opus** subagent at Phase 0 to fetch and normalize every
piece of PR feedback. The clerk exists to absorb the bulk — raw API JSON, diff
hunks, CI logs — and return only the compact ledger, so none of that ever
enters your context. Always fill in the real owner/repo/PR values; if you do
not know owner/repo, the clerk can resolve them with `gh repo view`.

```
Agent tool (model: opus, effort: xhigh):
  description: "Harvest feedback ledger for PR #[N]"
  prompt: |
    You are harvesting review feedback from a GitHub pull request into a
    compact ledger. You are strictly read-only: fetch and digest — do not edit
    files, post or react to comments, or commit.

    PR: #[number] in [owner/repo]   Branch: [head] -> [base]

    ## What to fetch

    1. **Formal reviews** —
       `gh api --paginate repos/[owner]/[repo]/pulls/[number]/reviews`:
       review bodies with author and state. Skip empty bodies and bare
       approvals.
    2. **Inline review threads** — use GraphQL so you get resolution state.
       Note the flag types: `-f` (string) for owner/repo, `-F` (typed) for the
       PR number — `-F` would mangle a numeric-looking owner into an Int and
       the query would be rejected.

       gh api graphql -f query='
         query($owner:String!, $repo:String!, $pr:Int!, $after:String) {
           repository(owner:$owner, name:$repo) {
             pullRequest(number:$pr) {
               reviewThreads(first:100, after:$after) {
                 pageInfo { hasNextPage endCursor }
                 nodes { isResolved isOutdated path line
                   comments(first:50) { nodes {
                     databaseId author { login } body createdAt } } } } } } }' \
         -f owner=[owner] -f repo=[repo] -F pr=[number]

       If `hasNextPage` is true, repeat with `-f after=<endCursor>` until it
       is false — never silently truncate. The **reply-to id** for each thread
       is the `databaseId` of its FIRST comment (`nodes[0]`) — replies anchor
       there, and the controller posts through it. A thread with more than 50
       comments: flag it as truncated rather than pretending completeness.
    3. **Conversation comments** —
       `gh api --paginate repos/[owner]/[repo]/issues/[number]/comments`.
    4. **CI** — `gh pr checks [number] --json name,state,link`. Expect a
       **non-zero exit when checks are failing or pending** — that is the
       signal you came for, not an error, so do not `&&`-chain this command.
       For each failing check whose `link` is a GitHub Actions URL, take the
       run id from the `/actions/runs/<run-id>/` segment and read
       `gh run view <run-id> --log-failed`. Digest it to the failing test/step
       names and the error message — never paste logs. A failing check on an
       external CI (non-Actions link): record it from the check name and mark
       it `[external CI — logs unavailable]`.

    ## What to skip (count it — nothing vanishes silently)

    - Threads already resolved.
    - Threads whose latest comment is by the PR author — already answered,
      awaiting the reviewer.
    - Pure approval or praise with nothing actionable; bot status walls
      (coverage tables, deploy notices) that contain no demand.
    - `isOutdated` alone is NOT a skip reason — outdated is not addressed;
      include the item and mark it `[outdated]`.

    ## Ledger format (compact — the controller stays lean)

    Header: PR title · one line on what the PR does (from its body) · checks
    summary ("N passing, M failing").

    Then one entry per actionable item:

    F1 · inline thread (reply-to comment 123456789) · @alice · src/auth/session.ts:42 · [outdated]
       Demand: <the feedback, verbatim when short; condensed when long, but
       preserving every distinct demand>
       Context: <≤1 line from the quoted hunk, only if the demand is
       unreadable without it>

    Source tags (the controller routes replies with these — be exact):
    - `inline thread (reply-to comment <databaseId of the thread's FIRST comment>)`
    - `review body @author (no thread)`
    - `conversation (issue comment)`
    - `CI: <check name>`

    Split a review body that makes several distinct demands into separate
    items. Number items F1, F2, … in the order fetched.

    ## Report

    - **Status:** LEDGER (N items) | NO_FEEDBACK | BLOCKED: <reason — e.g. gh
      not authenticated, PR not found>
    - The header and the ledger entries.
    - **Skipped:** X resolved · Y awaiting-reviewer · Z noise (one line).
```
