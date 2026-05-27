---
name: paladin-pr-review
description: Orchestrate security review for pull requests and PR diffs. Use for GitHub PR security review, inline PR comments, previous review-thread follow-up, and final PR security decisions; delegate core changed-code analysis to paladin-code-review.
---

# PR Security Review

Handle PR-specific context, automation, and final review decisions. Use `paladin-code-review` for the core security analysis of the changed code.

## Load Order

1. Read `PALADIN.md` if present.
2. Read `../paladin-code-review/SKILL.md`.
3. Read `../paladin-code-review/references/output-contract.md` before writing the final review, posting comments, or making a task.
4. Read the configured source list only when current advisory context matters. Use `PALADIN.md` `paladin_source_list`, or default to repo-root `references/security-sources.md`.

## Workflow

1. Resolve PR context when tooling is available:

```bash
git branch --show-current
gh pr view --json number,title,url,baseRefName,headRefName,files
git diff --stat
```

2. If GitHub review tooling is available, review previous unresolved security-review threads and re-check whether each still applies.
3. Run the `paladin-code-review` workflow with the PR diff as the scope. Review only changed code plus unchanged surrounding code required to prove reachability, controls, exploitability, and what security property changed.
4. Use the `paladin-code-review` output contract for findings, checks, misuse cases, advisory lookup, and final decision.
5. Post inline PR comments only for current high-confidence findings on exact diff lines. Keep each comment to severity, issue, impact, and required fix.
6. If no high-confidence vulnerability remains, leave no new finding comments and return the clean review summary.
7. Post a Slack summary only when Slack tooling is available and configured.
8. Do not push code, open fix PRs, or mutate production systems from this workflow.

## PR Automation Rules

- Prefer fresh evidence over stale review comments.
- Re-report still-valid prior findings with updated file and line evidence.
- Resolve or supersede old automation threads only when the current tool supports it and the user has authorized that workflow.
- If inline comments are unavailable, return comment-ready notes in the final response.
- If a PR cannot be detected and no PR diff is supplied, route to `paladin-code-review` for local changed-code review.

## Conversion

Follow the conversion contract from `paladin-code-review`: read `paladin_conversion`, `paladin_action_destination`, and `paladin_custom_action` from `PALADIN.md`; create follow-up items only when the local action contract allows it and actionable findings exist.
