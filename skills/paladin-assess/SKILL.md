---
name: paladin-assess
description: Paladin's security context dispatcher. Use when no specific Paladin skill is requested, when the user asks for a security review but the mode is unclear, or when Codex should inspect git state and route to code review, PR review, broad security review, repository audit, setup, or vulnerability mitigation.
---

# Assess

Detect the current security-review context and route to the right Paladin skill.

## Protocol

1. Read `PALADIN.md` if it exists. Use its frontmatter for conversion defaults, `paladin_action_destination`, `paladin_custom_action`, and the `paladin_source_list` path.
2. Run the checks below in order. Stop at the first clear match.
3. Announce what was detected in 2-3 lines.
4. Route immediately unless the request is genuinely ambiguous.

## Routing Contract

Use exactly one route:

| Route | Use when |
| --- | --- |
| `paladin-setup` | The user asks to set up Paladin, configure Paladin, create or update `PALADIN.md`, choose an action destination, or initialize repo config |
| `paladin-mitigate` | The user provides a CVE, CWE, dependency alert, scanner result, audit finding, or vulnerability description |
| `paladin-pr-review` | The user explicitly asks for PR review, an open PR exists for the current branch, or a PR diff is supplied |
| `paladin-code-review` | Changed files, staged files, uncommitted local changes, branch diffs, or pasted diffs need security review |
| `paladin-security-review` | The user asks for a broad security review, repo sweep, current advisory check, public zero-day claim evaluation, known-exploited vulnerability check, or exposure review |
| `paladin-repo-audit` | The user specifically asks for a backlog-style repository audit, weekly metrics report, or recently merged PR sweep |

If more than one route matches, use the first matching row in this table. Do not ask a question when the route is clear.

## Detection Checks

### Check A - Setup Or Configuration

If the user asks to set up Paladin, configure Paladin, create or update `PALADIN.md`, choose an action destination, or initialize repo config, route to `paladin-setup`.

### Check B - Existing Finding

If the user provides a CVE, CWE, dependency alert, scanner finding, audit note, or vulnerability description, route to `paladin-mitigate`.

### Check C - Explicit PR Or Open PR

If the user asks for a PR review, pull request review, inline review comments, or PR decision, route to `paladin-pr-review`.

Otherwise run:

```bash
git branch --show-current
gh pr view --json number,title,url 2>/dev/null
```

If a PR exists for the current branch, route to `paladin-pr-review`.

If `gh` is missing, unauthenticated, or fails, check common PR environment signals:

```bash
printf '%s\n' "$GH_PR_NUMBER" "$PR_NUMBER" "$CHANGE_ID" "$GITHUB_HEAD_REF" "$GITHUB_REF" "$CI_PULL_REQUEST" "$CIRCLE_PULL_REQUEST" "$BUILDKITE_PULL_REQUEST"
```

If a signal identifies a PR number, PR URL, or pull-request ref, route to `paladin-pr-review`.

### Check D - Local Changed Code

Run:

```bash
git status --porcelain
git diff --name-only
git diff --cached --name-only
git diff --stat
```

If there are changed or staged files, route to `paladin-code-review`.

### Check E - Broad Security Review

If the user asks for a security review, repo sweep, current advisory check, public zero-day claim evaluation, known-exploited vulnerability check, or exposure review, route to `paladin-security-review`.

If the user specifically asks for a backlog-style repository audit, weekly metrics report, recently merged PR sweep, or security backlog, route to `paladin-repo-audit`.

### Check F - No Clear Context

Ask one concise question:

```text
Do you want setup, code security review, PR security review, broad security review, repository audit, or mitigation planning for a known finding?
```

## Routing Rules

- Do not perform a full review inside this skill. Detect context, announce, and route.
- Do not check current advisory sources inside this skill. Pass the configured source-list path to the routed skill.
- Do not reveal scratch reasoning. Report the detected signal and chosen route only.
