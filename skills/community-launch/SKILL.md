---
name: community-launch
description: 'Use when building Darrenapos;s Discord or classroom.'
---

# Community Launch

Stand up Darren's free-vs-premium community: Discord server with gated channels, plus classroom content (GroupApp/Skool) and member posts. Free shows the what and why; premium sells the how, the order, and the copy-paste files.

## Procedure

1. Connect Discord via Composio MANAGE_CONNECTIONS (auth link expires in ~10 minutes) and confirm ACTIVE with WAIT_FOR_CONNECTIONS before any bot call.
2. The owner creates the server (2 minutes in the Discord app) and posts a never-expire invite — then build everything inside it through the bot connection.
3. The owner creates the Free / Premium / Captain roles in the Discord app (no role-create tool exists on the connector) and grants the bot's own role Manage Channels + Manage Roles, positioned above the managed roles with changes saved. Then create channels (start-here, announcements, build-videos, fleet-maps, general, premium-only packs + live voice), with premium locks as explicit permission overwrites: deny `1024` (view) for @everyone, allow view+send for Premium — overwrites need the real role snowflake IDs, never placeholders.
4. Post rules + welcome in start-here, then verify by listing the channels back.
5. Fill the classroom in parallel: free module (watch + draw-along assignment), premium module (exact scripts, voiceovers, templates, QC checklist), welcome post, first pack drop.

## Pitfalls

- Bots cannot create Discord guilds (400 code 20001) — never retry guild-create; hand the owner the create steps and build inside afterward.
- Roles alone gate nothing — without channel overwrites every channel is public, so set deny/allow on each premium channel at build time.
- Verify the connector exists before promising automation — SEARCH_TOOLS with auto, then tool_search; a platform with no public API gets the content-engine workaround (you write posts and packs, the owner pastes), never a faked connection.
- Discord invites die (404 expired/revoked) — always ask the owner for a never-expire invite link.
