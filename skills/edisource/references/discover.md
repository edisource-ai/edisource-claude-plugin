# Discover feeds

Discover feeds collect source material for the active organization. A feed may be shared with the organization or private to the user. When a tool result includes that visibility, say which it is. Do not describe a private feed as shared, or a shared feed as personal.

Confirm the organization before listing or changing feeds. A private feed still belongs to the active organization context; switching organizations does not carry another org's feeds into this chat.

## Connected tools

Send `explanation` and `organizationName` on every call. For a feed that already exists, also send `feedName` copied from `list_feeds`. A new feed's name is `feedName`. Do not put ids in `explanation`.

- `list_feeds` — list feeds the user can see, including whether each feed is private to the user or shared with the organization.
- `summarize_feed` — return source items for a time window (default the last 7 days) and whether the feed is private to the user or shared with the organization.
- `create_feed` — create a Discover feed in the active organization.
- `update_feed` — change a feed's keywords or visibility. Send `newName` only when renaming the feed.

## Creating and updating a feed

Create a feed with `create_feed`. New feeds are private to the user unless they ask for the feed to be shared with the organization. Say which scope was used.

`update_feed` changes keywords or visibility, and renames the feed only when `newName` is set. Do not delete a feed. `feedName` is the current name, for the person approving.

## Summaries

`summarize_feed` returns the source items for the window. Write the summary in the reply from those items. Do not invent items the tool did not return. The default window is the last 7 days. Say whether the feed is private to the user or shared with the organization.

If a tool returns `disabled_by_team_settings`, stop and follow the skill's team-settings rule.
