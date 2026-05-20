---
name: paladin-setup
description: Configure Paladin for a repository by creating or updating PALADIN.md. Use when the user asks to set up Paladin, initialize Paladin config, configure review actions, choose One Horizon/Linear/Jira/email/custom destinations, or prepare a repo for Paladin security review.
---

# Paladin setup

Create or update `PALADIN.md` in the current repository.

## Setup contract

Collect only the slots required for the selected action.

| Slot | Required when | Allowed values or format |
| --- | --- | --- |
| `target_repo` | Always | Current working directory, unless the user names another repo |
| `existing_config` | When `PALADIN.md` exists | Preserve unless the user asks to replace it |
| `paladin_conversion` | Always | `create_review_task`, `ask_first`, `report_only` |
| `paladin_action_destination` | When conversion is not `report_only` | `one_horizon`, `linear`, `jira`, `email`, `custom` |
| `paladin_custom_action` | For `linear`, `jira`, `email`, or `custom` | One sentence with the exact handoff rule |
| `one_horizon_workspace_id` | For One Horizon create or ask flows | Workspace ID from MCP or user input |
| `one_horizon_initiative_id` | Optional | Initiative ID to link findings under |
| `one_horizon_team_ids` | Optional | YAML array of team IDs |
| `one_horizon_assignee_ids` | Optional | YAML array of user IDs |
| `paladin_source_list` | Always | Default `references/security-sources.md` |

## Decision flow

1. Confirm `target_repo`.
2. Read `PALADIN.md` if it exists.
3. If a config already exists, summarize the current action and destination, then ask whether to update it or leave it unchanged.
4. Ask for `paladin_conversion` when it is missing or the user wants to change it.
5. If conversion is `report_only`, set `paladin_action_destination: custom` and `paladin_custom_action: ""`, then skip destination setup.
6. If conversion is `create_review_task` or `ask_first`, ask for `paladin_action_destination`.
7. If the destination is `one_horizon`, run the One Horizon MCP check.
8. If the destination is `linear`, `jira`, `email`, or `custom`, ask for `paladin_custom_action`.
9. Ensure the source list path exists or record it under `Still needed`.
10. Write or update `PALADIN.md`.
11. Re-read `PALADIN.md` and validate the frontmatter.

Ask one question at a time. Do not ask for slots that are not needed for the selected action.

## Question order

Use these questions when the answer is not already known from the request or existing config:

```text
Which repo should I configure? I am currently in {cwd}.
```

```text
After Paladin finds security work, should it create an item automatically, ask first, or only report?
```

```text
Where should the item go: One Horizon, Linear, Jira, email, or a custom workflow?
```

```text
What exact handoff should Paladin prepare for {destination}?
```

## Defaults

- Default `paladin_conversion` to `create_review_task` only when the user wants setup and does not choose another action.
- Default `paladin_action_destination` to `one_horizon` only when conversion is `create_review_task` or `ask_first`.
- Default `paladin_source_list` to `references/security-sources.md`.
- Default team and assignee arrays to `[]`.
- Leave optional IDs blank when unknown.
- Do not invent workspace, initiative, team, assignee, Linear, Jira, or email values.

## One Horizon MCP check

When the selected destination is One Horizon:

1. Check whether One Horizon MCP tools are available in the current tool list. Common tool namespaces include `mcp__onehorizon__` and `mcp__codex_apps__one_horizon__`.
2. If available, call a lightweight identity or workspace check such as `who_am_i` or `list_workspaces`.
3. If the check succeeds:
   - use the existing `one_horizon_workspace_id` when present
   - use the default workspace only when exactly one clear default is returned
   - otherwise ask which workspace ID to use
   - ask for an initiative ID only if the user wants findings linked to an initiative
   - use empty arrays for teams and assignees unless the user provides IDs
4. If One Horizon tools are missing or auth fails, ask exactly:

```text
One Horizon is not configured in this session. Do you want to configure it now, choose another destination, or use report_only?
```

If the user wants to configure One Horizon now, explain that the One Horizon MCP/app connector must be enabled in the current agent environment. Do not fabricate MCP settings.

If the user does not want to configure One Horizon now, do not block setup. Write `PALADIN.md` with the destination and action they choose.

## PALADIN.md frontmatter

Keep frontmatter keys in this order:

```yaml
---
paladin_conversion: {paladin_conversion}
paladin_action_destination: {paladin_action_destination}
paladin_custom_action: "{paladin_custom_action}"
paladin_source_list: {paladin_source_list}
one_horizon_workspace_id: "{one_horizon_workspace_id}"
one_horizon_initiative_id: "{one_horizon_initiative_id}"
one_horizon_team_ids: []
one_horizon_assignee_ids: []
---
```

For `report_only`, use:

```yaml
paladin_conversion: report_only
paladin_action_destination: custom
paladin_custom_action: ""
```

## Write rules

- Preserve existing non-frontmatter body text when updating `PALADIN.md` unless the user asks to replace it.
- If `PALADIN.md` has YAML frontmatter, update only the Paladin keys unless the user asks for a rewrite.
- If `PALADIN.md` has no YAML frontmatter, add the Paladin frontmatter at the top and preserve the body below it.
- If creating a new file, add this short body:

```markdown
# Paladin configuration

This file controls what Paladin does after it finds security work in this repo.
```

- Use repo-root `references/security-sources.md` as the canonical source list.
- If `references/security-sources.md` is missing in the target repo, locate the bundled Paladin source list by walking up from the current skill folder until both `plugin.json` and `references/security-sources.md` exist, then copy that file into the target repo. This is the only extra file setup should create.
- Do not use parent-directory fallback paths from an individual skill folder; they resolve differently across `.agents`, `.claude`, and plugin installs.
- If the source list cannot be created, still write `PALADIN.md` and include the missing source list in `Still needed`.
- If the selected destination tool is unavailable, still create `PALADIN.md` and state what remains to configure.

## Validation

After writing, re-read `PALADIN.md` and verify:

- all required frontmatter keys are present
- `paladin_conversion` is one of `create_review_task`, `ask_first`, or `report_only`
- `paladin_action_destination` is one of `one_horizon`, `linear`, `jira`, `email`, or `custom`
- `paladin_custom_action` is present for `linear`, `jira`, `email`, and `custom` when conversion is not `report_only`
- `one_horizon_workspace_id` is present for One Horizon create or ask flows, or listed under `Still needed`
- the configured source list exists, or is listed under `Still needed`

## Final response

Keep the final response short:

```text
Configured Paladin for this repo.
- Action:
- Destination:
- Config file:
- Still needed:
```
