# telegram-progress-reporting

**Pack:** bot-company-os

## Use case

'Use when reporting progress to Darren on Telegram.'

## How to use it

## Procedure

1. **Track in Multica same turn** — comment the result on the relevant issue in workspace `Darren111` (`multica issue comment add <uuid> --content "..."`), move status with `--no-start`, then read the issue back before claiming tracking succeeded — a successful write call is not proof the board reflects it.
2. **Clarify garbled speech-to-text before working** — ask exactly which road-traffic vs website-traffic meaning was intended plus the exact location or URL, since guessing the interpretation wastes the whole task.
3. **Verify before reporting** — ping live bots, re-read files, or re-list issues; report only what tool output actually confirmed.
4. **Lead with the answer in three labeled sections** — What changed / Verified / Left — so Darren sees outcome, evidence, and remainder without process replay.
5. **End with tappable next actions** — numbered short options with the recommended one first, never a bare "reply 1" line.
6. **De-escalate on overload** — when Darren says overloaded/tired, drop the curriculum dump immediately, keep one tiny next action only, and confirm tracking so he carries no memory load.
7. **Batch silently, deliver once** — when Darren says to just finish it, run all intermediate steps without interim replies and answer once with the artifact plus the verdict.

## Pitfalls

- Never emit a tool-only turn with an empty message body — on this platform it renders as a 'model returned no response' warning and reads as broken; every turn that runs tools still carries at least one line of real status text (what is running and what it decides).
- Answer the scoped question asked — when Darren asks about one bot/worker ('which bot is idle', 'what does the worker do'), name that worker plus its job only; never dump the whole fleet inventory unless he asks for the full picture. A 'concise it' correction means the last answer covered too much territory, not that it used too many words.
- Lead every recommendation with Darren's context, not your most-tested option — he lives in Jakarta, so Jakarta options come first; rotate choices and never push the same single item across turns. A 'why do you always say X' correction means the recommendation served your convenience, not his situation.

- Claim a file delivery only after the send confirms it — state the local path and what is needed until the transfer tool reports success, never announce a send that never ran.
- State free-flow model output as free-flow, never as live conditions — OSRM-style estimates ignore jams, so pair every estimate with a live-check link.
- Report a blocker honestly with what was tried instead of inventing a result — a frank gap preserves trust that fabricated output destroys.
- Feature only verified done work on public profiles — check the repo/file/site is live before listing it, and leave unfinished builds out until the owner confirms done.
- Never claim a LinkedIn/external profile edit — there is no write access without the owner's login session, so ship a copy-paste draft plus banner for approval and state nothing changed.
- Generate profile banners wide with the photo corner left dark and clean — text-minimal, role motifs subtle, delivered as photo for approval first.
- Never declare a user-facing fix done from tests plus endpoint checks alone — open the public artifact the way Darren will (fresh load, his exact tap sequence) and report what actually played; when the last mile needs his device, name that single unproven step instead of claiming works.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/telegram-progress-reporting/`). Video walkthrough ships with the pack.
