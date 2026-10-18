---
name: edisource
description: Works in a user's Edisource publishing account to search or create drafts, edit a draft, manage folders, set up or summarize Discover feeds, create or edit prompt templates, run a template, or publish. Use when the user wants Claude to work in Edisource.
---

# Edisource

Edisource is the user's publishing workspace: drafts in Content Studio, prompt templates, Discover feeds, and optional CMS actions. The user must already have an Edisource account. Edisource is not open for public signup, and this plugin cannot register a new account. Read the matching reference file before acting. Do not invent tool calls.

Call only the tools listed under Tools connected now.

## Active organization

Call `list_organizations` first if the organization is unknown. Use `set_active_organization` to switch. Content is organization-wide except Discover feeds, which may be private to the user. Never assume the last chat's organization is still active.

## Approval

Every tool call includes `explanation`: a few words or one sentence for the person approving. Say what the action does in everyday language. Do not put ids in it.

Also send every name field that tool lists below. Copy the name from the latest tool result. Edisource does not use these names to find the record. The id is what it uses.

| Tool | Name fields |
| --- | --- |
| `list_organizations` | none |
| `set_active_organization` | `organizationName` |
| `search_drafts`, `list_templates`, `list_feeds`, `search_cms` | `organizationName` |
| `get_draft`, `apply_draft_operations`, `publish_draft` | `organizationName`, `draftTitle` |
| `create_draft` | `organizationName`, `draftTitle`, `parentFolderName` |
| `create_folder` | `organizationName`, `parentFolderName`. The new folder's name is `folderName`. |
| `get_template`, `patch_template_content`, `update_template_settings`, `run_template`, `get_job` | `organizationName`, `templateName` |
| `create_template` | `organizationName`. The new template's name is `templateName`. |
| `summarize_feed`, `update_feed` | `organizationName`, `feedName` |
| `create_feed` | `organizationName`. The new feed's name is `feedName`. |

`parentFolderName` is `Top level` when the draft or folder is not inside another folder. `update_feed` sends `newName` only when the feed's name is changing.

Examples of `explanation`: `Switch to Billington Cybersecurity`, `Find recent drafts`, `Open Newest KEY TAKEAWAYS`, `Remove the KEY TAKEAWAYS line at the top of Newest KEY TAKEAWAYS`.

## Team settings

If a tool returns `disabled_by_team_settings`, stop that action and tell the user an admin or teammate has turned that capability off in Edisource Settings → Claude Plugin. Do not retry with a different tool that does the same thing.

## Which reference to read

| Task | Read before acting |
| --- | --- |
| Drafts or folders | `references/drafts.md` |
| Templates, Handlebars, or publish XML tags | `references/templates.md` |
| Discover feeds | `references/discover.md` |
| Running a template or source material | `references/generation.md` |
| CMS search or publish | `references/cms.md` |

Open only the reference the task needs.

## Tools connected now

- `list_organizations` — organizations the user can work in. Send `explanation`.
- `set_active_organization` — organization for the rest of this chat. Send `explanation` and `organizationName`.
- `search_drafts` — find drafts and folders in the active organization. Send `explanation` and `organizationName`.
- `get_draft` — load one draft's blocks. Send `explanation`, `organizationName`, and `draftTitle`.
- `create_draft` — create a draft in the active organization. Send `explanation`, `organizationName`, `draftTitle`, and `parentFolderName`.
- `create_folder` — create a normal folder in the active organization. Send `explanation`, `organizationName`, `folderName`, and `parentFolderName`.
- `apply_draft_operations` — edit an existing draft by block. Send `explanation`, `organizationName`, and `draftTitle`. Each edit's action key is `operation`, not `type`. The change stays pending until the user accepts or rejects it in Content Studio.
- `list_templates` — list templates in the active organization. Send `explanation` and `organizationName`.
- `get_template` — load one template's markdown body and settings. Send `explanation`, `organizationName`, and `templateName`.
- `create_template` — save a template the user has already approved. Draft it first as a Markdown artifact beside the chat, and call this only when they ask to save it. Send `templateName`, `markdown`, `explanation`, and `organizationName`.
- `patch_template_content` — change copy in a template's markdown with find and replace. Send `explanation`, `organizationName`, and `templateName`.
- `update_template_settings` — change a template's model, tools, or Handlebars parameter definitions. Send `explanation`, `organizationName`, and `templateName`.
- `list_feeds` — list Discover feeds the user can see. Send `explanation` and `organizationName`.
- `summarize_feed` — source items for a feed over a time window. Send `explanation`, `organizationName`, and `feedName`.
- `create_feed` — create a Discover feed, private to the user unless they ask to share it. The new name is `feedName`. Send `explanation` and `organizationName`.
- `update_feed` — change a feed's keywords or visibility. Send `explanation`, `organizationName`, and `feedName`. Use `newName` only when renaming.
- `run_template` — start a generation from a template id plus source material. Each source is a URL or plain text. Returns a job id immediately. Send `explanation`, `organizationName`, and `templateName`.
- `get_job` — poll that job until status is complete or failed. On complete, the result is a draft id. Send `explanation`, `organizationName`, and `templateName`.
- `search_cms` — search the organization's CMS and return titles and urls. Off unless the team enabled it. Send `explanation` and `organizationName`.
- `publish_draft` — publish one existing draft. Off unless the team enabled it, and the signed-in user must be an organization admin. Send `explanation`, `organizationName`, and `draftTitle`.

Call only these tools, and stop when a tool returns `disabled_by_team_settings`.
