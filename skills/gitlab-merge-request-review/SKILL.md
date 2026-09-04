---
name: gitlab-merge-request-review
description: Review a GitLab merge request by number, checkout GitLab merge-request refs, and write review.md. Use when the user says GitLab MR, merge request, MR review, or provides an MR number to review.
---

# GitLab Merge Request Review

Use this skill only inside the target Git repository. The remote `origin` must
be configured to fetch GitLab merge-request refs:

```ini
fetch = +refs/merge-requests/*/head:refs/remotes/origin/merge-requests/*
```

1. Get the merge request number from the user. If it is missing, ask for it.
2. Fetch and checkout its head without changing any local branch:

```bash
git fetch origin "refs/merge-requests/$MR/head:refs/remotes/origin/merge-requests/$MR"
git switch --detach "origin/merge-requests/$MR"
```

3. Determine the target branch with `glab mr view "$MR" --json target_branch --jq .target_branch` when `glab` is available; otherwise use `origin/HEAD` as the comparison base. Fetch the target branch if necessary.
4. Review the merge-base diff, the changed code in context, and relevant callers. Focus on correctness, regressions, security, data loss, and missing tests. Do not invent findings.
5. Write `review.md` at the repository root. List actionable findings first, ordered by severity, with file and line references. State `No findings` when applicable. Include a short testing/verification note.
6. In chat, give a short summary of the most important findings, what to inspect closely, and what should be reviewed before merging. Mention the path to `review.md`.

Do not commit, push, modify source files, or leave the checkout on a local branch.
