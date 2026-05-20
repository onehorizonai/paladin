---
paladin_conversion: create_review_task
paladin_action_destination: one_horizon
paladin_custom_action: ""
paladin_source_list: references/security-sources.md
one_horizon_workspace_id: w9ce7_a879c
one_horizon_initiative_id: tsktEvAS-rttEvA
one_horizon_team_ids: []
one_horizon_assignee_ids: []
---

# Paladin Configuration

This file tells Paladin how to handle review output for this repo.

## Quick Setup

Keep the frontmatter at the top of this file. Paladin reads those values before it reviews a PR, audits the repo, or plans a mitigation.

```yaml
---
paladin_conversion: create_review_task
paladin_action_destination: one_horizon
paladin_custom_action: ""
paladin_source_list: references/security-sources.md
one_horizon_workspace_id: w9ce7_a879c
one_horizon_initiative_id: tsktEvAS-rttEvA
one_horizon_team_ids: []
one_horizon_assignee_ids: []
---
```

## Action

`paladin_conversion` is Paladin's local action contract. It controls what Paladin does after it finds actionable work.

Paladin must follow this value exactly. It must not ask before creating an item unless this file explicitly sets `paladin_conversion: ask_first`.

| Value | Use when | Behavior |
| --- | --- | --- |
| `create_review_task` | You want findings tracked in the configured destination by default | Creates a concise review or mitigation item without asking when tools and required config are available |
| `ask_first` | You want control before item creation | Asks before creating an item |
| `report_only` | You only want chat output | Returns the report and does not create items |

If the field is missing, Paladin treats it as `create_review_task`.

If `create_review_task` is set but the destination tools or required config are unavailable, Paladin should return the report and state what prevented item creation. It should not ask for permission as a fallback.

## Action Destination

One Horizon is the default destination because it is bundled with this workflow. You can point Paladin at another tool or custom action from this same file.

```yaml
paladin_action_destination: one_horizon
paladin_custom_action: ""
```

| Destination | Use when | Behavior |
| --- | --- | --- |
| `one_horizon` | You want Paladin to create One Horizon work items | Uses the One Horizon fields below |
| `linear` | You want Paladin to create Linear issues | Uses Linear tools when available; otherwise returns a Linear-ready issue |
| `jira` | You want Paladin to create Jira issues | Uses Jira tools when available; otherwise returns a Jira-ready issue |
| `email` | You want Paladin to notify a security or SecOps team | Uses email tools when available; otherwise returns an email-ready message |
| `custom` | You have another workflow | Follows `paladin_custom_action` |

Examples:

```yaml
paladin_action_destination: linear
paladin_custom_action: "Create a Linear issue for the Security team with severity, evidence, fix, and required test."
```

```yaml
paladin_action_destination: email
paladin_custom_action: "Prepare an email to secops@example.com with the affected repo, severity, impact, and next action."
```

## Source List

`paladin_source_list` points to a Markdown list of official security sources Paladin should use when current vulnerability, advisory, public zero-day claim, or known-exploited context matters.

Default:

```yaml
paladin_source_list: references/security-sources.md
```

Paladin should read this source list before checking "latest" or recent issues for dependency changes, weekly sweeps, CVEs, GHSAs, OSV IDs, scanner findings, internet-facing infrastructure, public zero-day claims, or known exploited vulnerabilities.

The default path is repo-root relative. `paladin-setup` should create `references/security-sources.md` in the target repo from Paladin's bundled source list when the target repo does not already have one.

When source lookup is needed, Paladin should download or query advisory metadata from official feeds, evaluate potentially relevant records one by one against local repo inventory, and report only records that need action or verification. It may report `already protected` when useful, such as when a high-profile exploit exists but this repo already uses the fixed package.

Paladin must not download proof-of-concept exploit repositories, exploit payloads, weaponized code, or exploit playbooks.

When advisory feeds are used, Paladin should include a compact audit trail: feeds checked, records evaluated, action required, needs verification, and already protected items when useful.

If the file is missing or network access is unavailable, Paladin should mark the current advisory check as `not available` and continue with local repo evidence.

## Report Style

Paladin reports should be concise enough for a CEO to understand.

Every report should open with:

```text
Security check result: Action required / No action needed / Needs verification

- What Paladin checked:
- What matters:
- What to do next:
- Owner:
```

Use plain English first. Define unavoidable technical terms on first use. Put OWASP, CWE, ASVS, CVE, GHSA, OSV, KEV, and similar labels in the details after the plain-English explanation.

## One Horizon Fields

These fields are used only when `paladin_action_destination` is `one_horizon` and item creation is allowed.

| Field | Required | Description |
| --- | --- | --- |
| `one_horizon_workspace_id` | Yes | Workspace where Paladin should create review or mitigation items |
| `one_horizon_initiative_id` | No | Initiative to link created items under |
| `one_horizon_team_ids` | No | Teams to assign by default |
| `one_horizon_assignee_ids` | No | People to assign by default |

Use empty arrays when there is no default team or assignee:

```yaml
one_horizon_team_ids: []
one_horizon_assignee_ids: []
```

## Recommended Defaults

For shared team repos, use:

```yaml
paladin_conversion: create_review_task
```

For experiments, public demos, or repos where task creation would be noisy, use:

```yaml
paladin_conversion: report_only
```

For sensitive repos where a human should approve every task, use:

```yaml
paladin_conversion: ask_first
```
