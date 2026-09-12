# composio-automation

**Pack:** bot-company-os

## Use case

Use when calling Composio app toolkits.

## How to use it

Follow the skill body step by step; verify with tool output before handover.

## Pitfalls

- A masked contact in fetched page text (`[email protected]`) is not an address — validators reject it; route to the posting's form/apply link instead.
- Never bot-click Apply/submit/send on the owner's accounts without explicit per-action approval — ban and reputation risk outlive any convenience; prep paste-ready kits and let the owner click.
- After WAIT reports ACTIVE, compare the connected account identity against the identity the task assumes (sender address, profile owner) — a valid connection on the wrong account sends mail or acts as the wrong person; flag the mismatch before firing.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/composio-automation/`). Video walkthrough ships with the pack.
