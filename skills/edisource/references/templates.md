# Prompt templates

Templates are reusable generation recipes stored for the active organization. The body is markdown in a file: role, structure, voice, and instructions the model follows each time someone runs the template. Settings (model, tools, parameters) are separate from that file.

Confirm the organization before creating or changing a template. Template changes affect everyone who can run it.

## Connected tools

Send `explanation` and `organizationName` on every call. Copy the organization name from `list_organizations`. For a template that already exists, also send `templateName` copied from `list_templates` or `get_template`. A new template's name is `templateName`. Do not put ids in `explanation`.

- `list_templates` — list templates in the active organization.
- `get_template` — load one template's markdown body and settings.
- `create_template` — create a template with `templateName` and a markdown body.
- `patch_template_content` — change copy in the markdown body with find and replace.
- `update_template_settings` — change model, enabled tools, and parameter definitions.

Changing copy:

```json
{
  "explanation": "Change the deck instruction in Weekly briefing",
  "organizationName": "Billington Cybersecurity",
  "templateName": "Weekly briefing",
  "templateId": "<template id>",
  "find": "<exact text that appears once>",
  "replace": "<replacement text>"
}
```

Read an existing template with `get_template` before changing it. Do not rewrite the whole template.

## Changing the body

`patch_template_content` is connected. Change copy with a `find` string that appears exactly once, plus `replace`. If the phrase might match twice, use a longer snippet that includes the surrounding lines. Never rewrite the whole template to change a sentence. Do not edit the markdown to change a model.

Leave every section, parameter token, and publish tag the user did not ask to change.

## Settings are not markdown

Model, enabled tools, and parameter definitions change only through `update_template_settings`. Do not edit the markdown to change a model.

Enabled tools are the capabilities the model may use while that template runs:

- Web search
- CMS search (only useful when the organization has a CMS connected, and only if the team has allowed CMS search)
- Custom function tools the organization has defined

Turning a tool on in the markdown, or writing "use GPT" in the prompt, does not change the setting. Use `update_template_settings` for the model, enabled tools, and parameter definitions.

## Handlebars parameters

`{{parameterName}}` is an input the user fills in when they run the template. The token is replaced at run time. It is not a publish XML tag and not a knowledge block.

When creating a template, put the token in the markdown, for example `{{target_audience}}`, and mention each parameter you added. Registering that token in the parameter list (label, type, and whether it is required) uses `update_template_settings`.

Removing a parameter later means removing the token with `patch_template_content` and the list entry with `update_template_settings`. A token with no definition, or a definition the body never uses, leaves the template inconsistent.

Built-in tokens the product already understands:

| Token | Filled from |
| --- | --- |
| `{{source_material}}` | URLs or text supplied for this run. Required when the template should read uploaded source. |
| `{{style_guide}}` | The organization's style guide. |
| `{{brand}}` | The publication name for this run. |
| `{{user_notes}}` | Optional notes the person adds for this run only. |

Custom parameters are short text, long text, single select, or multi select. Select options can carry a value that is injected when the writer picks that option.

`{{block_…}}` is a knowledge block: organization content inserted automatically, not an input the user types at run time. Do not add it to the parameter list. Do not rename a `block_` token unless the user asked to point the template at a different block.

## Publish XML tags

Templates can instruct the model to wrap segments in XML tags the user wants parsed at publish time. The integration reads those tags out of the body and maps each tag name to a destination field.

Common tags:

- `<title>…</title>` — headline
- `<dek>…</dek>` — deck or excerpt
- `<tags>…</tags>` — topic labels
- `<category>…</category>` — section or category
- Other metadata the team maps, such as SEO title or SEO description

Example instruction inside a template body:

```xml
Wrap the output in these tags and do not add tags that are not listed:
<title>headline</title>
<dek>one-sentence deck</dek>
<tags>comma-separated topics</tags>
<category>section name</category>
```

Structural brackets such as `<instructions>` and `<source_material>` organize the prompt. They are not publish fields. Do not treat them as metadata, and do not strip them when editing copy.

Renaming or removing a publish tag changes what Edisource can extract for the destination platform. A CMS mapping that expects `dek` will not receive that field if the template starts emitting `deck` instead. Preserve tags the user did not ask to change. When the user does rename a tag, say which destination field will stop matching until someone updates the integration mapping.

## Creating a template

Draft a new template in a Markdown artifact beside the chat, not as a code block in the reply. That panel is the working copy the user can edit with you. Do not use a Design canvas, and do not open a rich-text Claude Doc: Handlebars tokens and publish XML tags have to stay as plain text.

Do not call `create_template` while you are still drafting. Ask what they want changed, edit the artifact, and wait until they ask to save it in Edisource.

When they do, call `create_template` with `templateName`, `explanation`, and `organizationName`. Set `markdown` to the current text of the artifact, including edits they made in the panel. `create_template` has no artifact id, doc id, or file id. If you cannot see their latest edits in the panel, ask them to send the current text before saving, rather than saving an older draft.

If the user describes the job but does not supply the full prompt, draft the markdown in that artifact. Use Handlebars `{{parameter}}` for inputs they will fill in. Use XML tags such as `<title>`, `<dek>`, `<tags>`, and `<category>` only when they want those fields parsed at publish. Mention the parameters you added.

Include `{{source_material}}` when the template is meant to transform something the user supplies. Do not rewrite an existing template. An existing template stays on `get_template` and `patch_template_content`.
