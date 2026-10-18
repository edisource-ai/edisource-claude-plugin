# Drafts and folders

Drafts and folders belong to the active organization. Confirm the organization with `list_organizations` and `set_active_organization` before creating or changing anything. Never assume the last chat's organization.

## Connected tools

Send `explanation` on every call, plus the name fields in the skill's Approval section. Copy names from the latest result. Do not put ids in `explanation`.

- `search_drafts` — find drafts and folders in the active organization. An empty query returns results newest first. Send `organizationName`.
- `get_draft` — load one draft's blocks. Always call this before editing, and use only ids from that response. Send `organizationName` and `draftTitle`.
- `create_draft` — create a draft in the active organization. The title is `draftTitle`. Send `organizationName` and `parentFolderName` (`Top level` when unfiled).
- `create_folder` — create a normal folder in the active organization. This is not a project. The name is `folderName`. Send `organizationName` and `parentFolderName`.
- `apply_draft_operations` — edit an existing draft by block. Do not send a rewritten document. Send `organizationName` and `draftTitle`.

Switching organizations:

```json
{
  "explanation": "Switch to Billington Cybersecurity",
  "organizationName": "Billington Cybersecurity",
  "organizationId": "<organization id from list_organizations>"
}
```

Searching drafts:

```json
{
  "explanation": "Find recent drafts",
  "organizationName": "Billington Cybersecurity"
}
```

## Finding a draft

If the user names a draft loosely, call `search_drafts`, then `get_draft` on the match they mean. For the most recent draft, call `search_drafts` with an empty query so results come back newest first, then `get_draft` on the first match the user means.

## Reading a draft

`get_draft` returns block ids and the text of each block. Edits use only those ids. Do not invent an id, and do not rewrite the draft as one document.

Always call `get_draft` before `apply_draft_operations`. Use only ids from that response.

## Creating a draft

Create a draft in the active organization with `create_draft`. Use the title the user asked for. Put the draft in a folder only when they named one. When they named a folder, search for it with `search_drafts` first. If that folder does not exist, create it with `create_folder`, then create the draft in it.

`create_folder` makes a normal folder, not a project.

After creating a draft, tell the user the title and that it is in Content Studio. Do not claim you edited the body with node operations.

## Editing

`apply_draft_operations` is connected. Always call `get_draft` first and use only ids from that response. Do not send a rewritten document.

`apply_draft_operations` accepts `modify`, `delete`, `insert_after`, and `insert_before`. The action key is `operation`. Do not put the action under `type`.

Always send `explanation`, `organizationName`, and `draftTitle`. `explanation` is one sentence a reader would understand. Example: `Remove the KEY TAKEAWAYS line at the top of Newest KEY TAKEAWAYS.` Do not put ids in that sentence. The block edit still goes in `node_operations`.

- `modify` sends `operation`, `target_node_id`, and `new_text`. Change only the blocks the user asked to change.
- `delete` sends `operation` and `target_node_id`.
- `insert_after` and `insert_before` send `operation`, `anchor_node_id`, and `content` as plain text. `new_node_type` is optional and defaults to paragraph. Do not send `new_node` JSON.

To add text at the top of a draft, insert before the first block id from `get_draft`:

```json
{
  "explanation": "Add a TL;DR paragraph at the top of Newest KEY TAKEAWAYS.",
  "organizationName": "Billington Cybersecurity",
  "draftTitle": "Newest KEY TAKEAWAYS",
  "itemId": "<draft item id>",
  "node_operations": [
    { "operation": "insert_before", "anchor_node_id": "<block id>", "content": "TL;DR text" }
  ]
}
```

To remove a line at the top, delete the first block id from `get_draft`:

```json
{
  "explanation": "Remove the KEY TAKEAWAYS line at the top of Newest KEY TAKEAWAYS.",
  "organizationName": "Billington Cybersecurity",
  "draftTitle": "Newest KEY TAKEAWAYS",
  "itemId": "<draft item id>",
  "node_operations": [
    { "operation": "delete", "target_node_id": "<block id>" }
  ]
}
```

After a successful apply, tell the user the change is pending in Content Studio until they accept or reject it. Do not say the draft is final.

If the tool returns `disabled_by_team_settings`, stop and follow the skill's team-settings rule. Do not recreate the draft to get around a refused edit.
