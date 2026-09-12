# prompt-engineering

**Pack:** content-fleet

## Use case

'Use when sharpening prompts for max output.'

## How to use it

## Procedure

1. Straight answer — direct result with no fluff, token-conscious. Lead with verdict, max 4-6 bullets.
2. Max prompt — copy-paste builder block with role + scope lock + constraints + acceptance criteria. Constrain with preserve-existing, no backend/framework change unless asked, local-preview-only when publishing is gated.
3. Why — one line stating the mechanism that makes the prompt work (scope lock, anti-regression, forced verification).
4. What I need — one question to push further, with tappable choices and recommended option first.
5. Format for Telegram: airy — short header, blank lines between sections, bold section labels, links on own lines. Never dense walls.

## Pitfalls

- Answer before meta-talk — user judges value on the direct result, not prompt theory, because abstraction without payoff reads as filler.
- Lock scope to one gap per builder prompt with preserve + report changed/verified/left — unconstrained prompts regress working tools because the model expands scope by default.
- Gate external side effects (push, publish, spend) with explicit local-only + supervision-required — builder defaults push when done because completion looks like delivery.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/prompt-engineering/`). Video walkthrough ships with the pack.
