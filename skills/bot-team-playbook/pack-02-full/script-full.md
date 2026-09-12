# Skill 02 FULL — bot-team-playbook (~8:30, blackboard, AdSense cut)

## S1 HOOK — the bottleneck (45s)
One agent doing everything is a bottleneck with extra steps. You ask for research, code, and a post — it does them one by one, forgets the middle, and burns your context. Real teams don't work like that. They have a manager who never touches the work, and workers who each own one job. This skill is that pattern, written down so your agents follow it every single time. By the end you'll run a fleet from one command.

## S2 WHAT IT IS (50s)
The bot team playbook turns one big job into cards on a board: to do, doing, done. An orchestrator breaks your request into small cards. Worker sub-agents pick them up in parallel — one worker per card. A group is two to six bots, capped at three rounds or ten messages, so nothing spirals. You give one command before lunch. You come back, the board moved. That is the moment it stops feeling like a chatbot and starts feeling like a team.

## S3 ONE BOT ONE JOB (50s)
Rule one: one bot, one job. Every worker is a profile with its own chat, role, model, memory, skills, even avatar. No universal agents — a bot that does everything does nothing well. The researcher reads files and searches the web. The builder writes code. The reviewer checks. When a worker finishes, its default handoff is the orchestrator, never sideways to another worker. Sideways handoffs are how work gets lost and two bots redo the same card.

## S4 SMART TOP, FREE BELOW (50s)
Put the smartest model on top and free models below. Your orchestrator thinks — it plans, splits, and judges — so it gets muse-spark or Solar-Pro. Workers execute narrow cards, so free tier is fine. This is the whole economics of fleets: one expensive brain directing many cheap hands. A top model alone can beat a team on quality for a one-off. But for repeatable work, the team wins on cost every week, forever.

## S5 TRIM BEFORE RUNNING (50s)
Skills lazy-load, but tools are always live — every tool rides every call. So prune tools before running. The researcher turns off delegation, code execution, and cron; it keeps files, terminal, and web search. The orchestrator turns off web and browser entirely; it keeps files and read-only verification. Unneeded tools cause extra calls, quota burn, and the classic failure: a worker delegating to itself in a twenty-minute loop. Trim first, run second.

## S6 BOUND EVERY TASK (50s)
Every card gets bounds: source caps, word caps, a fixed file path, and an idle timeout of ten minutes or more for long runs. In groups, names scope the speakers — @researcher, @builder — silence means pass, and @user escalates to you. If a cap hits mid-fix, say the worker's name plus please continue, or message it directly. Bounds are what keep six bots from turning one task into sixty messages.

## S7 THE QC GATE (50s)
The manager never forwards unchecked results. Every return gets re-read against file proof, then the QC gate, then at most one focused revision. Numbers and research carry sources or file links — no citation, no claim, the line gets cut. After runs, ask each bot what it learned: which paths worked, which retry rules fired. Persist that to memory so the next run skips a step. Teams that don't debrief repeat their mistakes weekly.

## S8 THE ONE LINE (45s)
And the one line that makes all of it work: the orchestrator never does the work itself. Its only job is break down and hand out. The moment it starts executing, everything bottlenecks through one agent. Manager manages. Workers work. One command moves the board.

## S9 INSTALL (40s)
Get it free in the DARREN LLC skill library on GitHub — bot-team-playbook, skill file plus use-case doc, link below. Next skill video drops ascending: cloudflare-deploy. Draw your first board today: one task, three cards, two workers. Post it in the Discord and get reviewed.
