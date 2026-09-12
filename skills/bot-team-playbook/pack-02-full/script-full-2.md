
## S10 EXAMPLE SETUP
Let's build one live. The task: scan three competitors and draft our answer post. One orchestrator, three workers: researcher, writer, checker. Bounds first: only their pricing pages plus one review each, answers under two hundred words, everything lands in one markdown file. Ten-minute idle timeout. That is the whole setup, and it fits in a single message.

## S11 ORCHESTRATOR PROMPT
The orchestrator prompt says: break this into three cards, hand one to each worker, verify every return against file proof, allow one revision each. It also says the line: you never execute. If the orchestrator starts summarizing pages itself, stop it — that is the bottleneck returning through the back door. Managers manage.

## S12 WORKER PROMPTS
Worker prompts are narrow on purpose. Researcher: pull pricing and one weakness per competitor, cite every number. Writer: draft two hundred words from the research file only, no new claims. Checker: verify each number against the research file, cut anything uncited. Each worker gets its files, its cap, and its handoff: back to the orchestrator. Nothing sideways.

## S13 READING THE BOARD
Run it and read the board. Researcher finishes first — prices plus weaknesses, cited. Writer drafts from that file. Checker cuts two uncited lines and sends it back once; the writer fixes from the same file. The orchestrator merges and reports: one file path, three sources, zero invented numbers. Four agents, one command, eleven minutes. That is the pattern working.
