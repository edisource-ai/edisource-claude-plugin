# CMS search and publish

CMS tools talk to the content system the organization has connected. `search_cms` and `publish_draft` are connected and off unless the team enabled them. Searching the CMS and publishing a draft are different actions; do not publish because a search was allowed.

Confirm the organization before either action. Never assume a previous chat already published or that a generation job published itself.

## Connected tools

- `search_cms` — search the organization's CMS and return titles and urls. Off unless the team enabled it. Send `explanation` and `organizationName`.
- `publish_draft` — publish one existing draft. Off unless the team enabled it, and the signed-in user must be an organization admin. Send `explanation`, `organizationName`, and `draftTitle`.

Publishing:

```json
{
  "explanation": "Publish Newest KEY TAKEAWAYS",
  "organizationName": "Billington Cybersecurity",
  "draftTitle": "Newest KEY TAKEAWAYS",
  "itemId": "<draft item id>"
}
```

## When the team has not allowed it

If `search_cms` or `publish_draft` returns `disabled_by_team_settings`, stop that action. Tell the user an admin or teammate has turned that capability off in Edisource Settings → Claude Plugin. Do not retry with a different tool that searches or publishes the same content.

`publish_draft` also requires the user to be an organization admin. If it is refused, say a team admin must turn on Publish to CMS, or that the signed-in user is not an admin. Do not try to publish by editing the draft, running a template, or calling `search_cms`.

## After a publish call

Never say content was published unless `publish_draft` succeeded. Say what the tool returned, and that the draft is still unpublished until a successful result says otherwise. Do not invent a CMS post url. A pending edit in Content Studio is not a publish.
