---
name: worker-cli-proxy
description: "Use when routing worker CLIs via a local proxy."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [windows, linux, macos]
---

# Worker CLI Proxy Routing

Launch external worker coding CLIs (Claude Code, Codex, OpenCode) through
Darren's local Anthropic-compatible proxy with per-command env overrides.
Never edit the user's proxy config to fix orchestration problems.

## Procedure

1. Read the proxy token from the proxy's own config file inside the
   launcher script (python `subprocess` with a built env dict), so the
   secret never appears in shell history, logs, or prompts.
2. Export exactly three variables into the child env:
   `ANTHROPIC_API_KEY=<proxy token>`, `ANTHROPIC_BASE_URL=<proxy /v1>`,
   `ANTHROPIC_MODEL=<exact proxy model id>`.
3. Never pass `--model <alias>` — the CLI expands aliases to first-party
   names the proxy rejects with HTTP 400. The env model id wins.
4. Smoke-test with a trivial `--max-turns 1` prompt first; a hang or 400
   there is an env problem, not a task problem.
5. Judge runs by exit code plus real output. `unrecognized_model` catalog
   warnings are non-fatal noise (they also fire on session-title steps).

## Pitfalls

- Print or echo the proxy token while debugging — once in a log, it must
  be rotated; always pass secrets via constructed env, never the command line.
- Treat a model-catalog warning as a failed run — the job behind it often
  succeeded; check the exit code and the artifact first.
- 'Fix' auth by rewriting the user's proxy settings — the next session
  inherits the damage; override per command instead.
- Trust `claude auth status` over a failing run — with a proxy in play,
  `loggedIn:true` plus `Not logged in` at runtime means the OAuth token is
  dead; the env triple is the real credential, so re-check it instead of
  chasing login flows.
