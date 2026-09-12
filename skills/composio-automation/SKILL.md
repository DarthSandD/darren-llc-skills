---
name: composio-automation
description: Use when calling Composio app toolkits.
version: 1.0.0
author: Hermes Agent
license: MIT
---

# Composio Automation

Call-shape discipline and preflight for every Composio toolkit workflow (Gmail, LinkedIn, Canva, ...). Load this before SEARCH_TOOLS / MULTI_EXECUTE on any toolkit — shape errors fail validation and burn turns.

## Session handling

- Open with COMPOSIO_SEARCH_TOOLS for the exact use case; reuse its `session_id` on every follow-up call in that workflow.
- After COMPOSIO_MANAGE_CONNECTIONS returns a redirect link: share it as a clickable markdown link first, then call COMPOSIO_WAIT_FOR_CONNECTIONS before executing anything.
- After WAIT reports ACTIVE, note which account id/alias connected — multi-account toolkits need it on every execution call.

## Call shapes (strict)

- SEARCH_TOOLS `queries` is an array of `{use_case}` objects — bare strings are rejected.
- MULTI_EXECUTE requires BOTH `tools: [{tool_slug, arguments}]` AND `sync_response_to_workbench` — omitting either fails validation.
- Scalar fields stay scalar (`recipient_email` is one string, never an array). When unsure, pull COMPOSIO_GET_TOOL_SCHEMAS first — never guess slugs or fields.
- Get schemas with COMPOSIO_GET_TOOL_SCHEMAS, execute with COMPOSIO_MULTI_EXECUTE_TOOL; deferred `tool_call` by slug alone is refused — route through the executor.

## Preflight before promising an action

- Confirm an ACTIVE connection for the toolkit, then check capability endpoints for gated features (e.g. Canva capabilities + brand-template list before promising autofill).
- Reads succeed where writes don't: profile/post reads never imply apply/edit/submit endpoints exist. Verify the endpoint, then promise.
- Attachments are staged `{name, mimetype, s3key}` refs — never local paths, URLs, or inline data. With no staging path available, send body-only or save a draft and state plainly that the file rides separately.

## Pitfalls

- A masked contact in fetched page text (`[email protected]`) is not an address — validators reject it; route to the posting's form/apply link instead.
- Never bot-click Apply/submit/send on the owner's accounts without explicit per-action approval — ban and reputation risk outlive any convenience; prep paste-ready kits and let the owner click.
- After WAIT reports ACTIVE, compare the connected account identity against the identity the task assumes (sender address, profile owner) — a valid connection on the wrong account sends mail or acts as the wrong person; flag the mismatch before firing.
