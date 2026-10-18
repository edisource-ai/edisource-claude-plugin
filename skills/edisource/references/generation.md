# Running a template

Running a template generates content from a saved template plus source material. It does not publish, and it does not edit the template.

Confirm the organization, then identify the template. Read `references/templates.md` first if you still need to create or change the template.

## Connected tools

`run_template` and `get_job` are connected.

- `run_template` — start a generation job from a template id plus source material. Returns a job id immediately. Send `explanation`, `organizationName`, and `templateName`.
- `get_job` — poll that job until status is complete or failed. Send the same `organizationName` and `templateName`, with an `explanation` such as `Check the Weekly briefing job`.

## Source material

`run_template` takes a template id plus source material. Each source is a URL or plain text:

- a URL the user supplied, or
- plain text Claude writes from what the user provided (notes, a paste, or a short brief).

Do not invent facts to fill source material. If the template requires `{{source_material}}` and the user has not provided any, ask for a URL or the text to use.

Parameter values the template defines are filled by the user for that run. Do not skip a required parameter by editing the template body.

## Jobs

`run_template` returns a job id immediately. Poll `get_job` with that id until status is complete or failed. Tell the user generation is in progress until the job finishes.

On complete, the result is a draft id (`contentDraftItemId`). Open it with `get_draft` if the user wants the text. On failed, report the failure. Do not invent a draft.

Do not publish the result unless the user asked to publish. Publishing is a separate action covered by `references/cms.md`.

If a tool returns `disabled_by_team_settings`, stop and follow the skill's team-settings rule. Do not run a different template to bypass the refusal.
