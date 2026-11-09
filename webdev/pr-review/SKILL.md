---
name: pr-review
description: Review a PR or branch against main and post it as a PR comment (merge confidence, regressions, migrations, pre-merge checklist, a small Mermaid map). Use when asked to review a PR or branch, or when a PR process asks for a pr-review.
---
# PR review

Review the current PR or branch against main.

## Gather
1. `gh pr view --json number,title,headRefName,baseRefName,url` for the current branch. No PR → review the branch against main.
2. Read the diff and enough of the surrounding code to judge it.
3. Check the PR head in a separate git worktree (and a separate build/target dir), so the main checkout stays untouched. Never push, merge or change the repo.

## Check before you score
Do every check you'd otherwise list as a confidence bump, and state its result in the review:
- Run the project's tests, build, typecheck, lint and fmt check.
- UI or TUI change: run the app and capture a screenshot or recording (see `pr-media`). Behavior change: exercise the changed path (a VM, a temp config, a scratch dir).
- Try the failure and edge paths the review would otherwise ask about.

Score confidence after the checks, not before.

## Write
Body in this order, short and plain:

```
<!-- pr-review -->
`head-branch` → `base-branch`
Model: <model id>, <thinking effort, or "effort unknown">

## Confidence: X/5
<one line on why>
**Checks run:** what you ran and the result (e.g. "cargo test: 212 passed", "White theme screenshot below").
**Confidence bumps:** only what you truly can't do yourself (real hardware, another OS, secrets, production data), each with why and what it would raise the score to (e.g. "run the migration on staging (no staging access) → 4.5/5"). Otherwise "none left".

## Summary
<small description, ready to read as a PR comment>

## Map
<Mermaid flowchart of what changed: touched files/modules as nodes, edges for how they call or depend on each other. At most ~10 nodes; group small files. Its job is to tell a reader where to start.>

## Regressions
## Migrations
<needed or not, and what>
## Before merging
- [ ] checklist
```

## Post
- **On a PR, with write access:** post it as a comment. Keep one review comment per author: if one exists, update it.
  ```
  me=$(gh api user --jq .login)
  id=$(gh api "repos/{owner}/{repo}/issues/<n>/comments" --paginate \
    --jq ".[] | select(.user.login==\"$me\" and (.body|startswith(\"<!-- pr-review -->\"))) | .id" | tail -1)
  [ -n "$id" ] && gh api -X PATCH "repos/{owner}/{repo}/issues/comments/$id" -F body=@review.md \
               || gh pr comment <n> --body-file review.md
  ```
  Use the GitHub account the repo's own rules say.
- **No PR, or no write access:** write it to `PR_REVIEW_{TIMESTAMP}.md` in the repo root instead, and say where it is.
