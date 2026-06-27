---
name: pr-review
description: Perform structured GitHub Pull Request reviews using the gh CLI, including analysis, comments, and approval or change requests
license: MIT
compatibility: opencode
metadata:
  workflow: github
  audience: developers
---

# GitHub Pull Request Review Skill

This skill performs a structured review of a GitHub Pull Request using the `gh` CLI.

It analyzes code changes, identifies problems, and posts review comments through GitHub.

---

# When to use this skill

Use this skill when:

- A pull request needs to be reviewed
- Code quality or architecture validation is required
- A contributor asks for feedback
- CI passed but human review is still needed

---

# Requirements

The following tools must be available:

- `git`
- `gh` (GitHub CLI)
- authenticated GitHub session (`gh auth login`)

---

# Review Workflow

Follow these steps strictly.

## 1. Identify the PR

If a PR number is provided:

```bash
gh pr view <PR_NUMBER>
````

If none is provided:

```bash
gh pr list
```

Ask the user which PR should be reviewed.

---

## 2. Fetch PR metadata

Collect:

```bash
gh pr view <PR_NUMBER> \
  --json title,body,author,baseRefName,headRefName,files,additions,deletions
```

Analyze:

* purpose of the PR
* scope of changes
* affected files

---

## 3. Checkout the PR locally

```bash
gh pr checkout <PR_NUMBER>
```

This ensures the correct branch is reviewed.

---

## 4. Inspect the diff

Use:

```bash
gh pr diff <PR_NUMBER>
```

Focus review on:

* correctness
* security issues
* performance risks
* code style
* maintainability
* architectural impact
* test coverage

---

## 5. Perform code analysis

Check for:

### Logic errors

* incorrect conditions
* edge cases
* null handling
* incorrect async usage

### Security issues

* injection risks
* secrets committed
* unsafe deserialization

### Maintainability

* overly complex functions
* duplicated logic
* missing documentation

### Performance

* unnecessary allocations
* inefficient loops
* blocking operations

---

## 6. Create structured review comments

When issues are found, comment inline:

```bash
gh pr comment <PR_NUMBER> \
  --body "Review: <comment>"
```

If commenting on a specific file:

```bash
gh pr review <PR_NUMBER> \
  --comment \
  --body "Feedback about the implementation..."
```

---

## 7. Submit final review

Choose one of the following outcomes.

### Approve

```bash
gh pr review <PR_NUMBER> --approve
```

### Request changes

```bash
gh pr review <PR_NUMBER> \
  --request-changes \
  --body "Please address the review comments before merging."
```

### General feedback

```bash
gh pr review <PR_NUMBER> \
  --comment \
  --body "Overall review summary..."
```

---

# Review Checklist

Before approving ensure:

* Code compiles
* Tests pass
* Naming is clear
* No debug code remains
* Architecture fits the project
* No obvious security issues exist

---

# Output Format

Always summarize your review:

```
PR SUMMARY
Files changed:
Risk level:
Major issues:
Minor issues:
Suggested improvements:
Final decision:
```

---

# Notes

* Prefer actionable comments.
* Avoid vague feedback.
* Provide examples when suggesting improvements.

