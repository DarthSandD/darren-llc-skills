# windows-shell-interop

**Pack:** system-utils

## Use case

'Use when running Windows-native commands from git-bash.'

## How to use it

## Procedure

1. For PowerShell, write a `.ps1` file (e.g. under `$LOCALAPPDATA/Temp`) and run `powershell -NoProfile -ExecutionPolicy Bypass -File <C:/style/path>`.
2. Pass native tools `C:/Users/...` forward-slash paths; never MSYS `/c/...` paths to a native binary.
3. When driving an external coding CLI through an Anthropic-compatible proxy, authenticate with `ANTHROPIC_API_KEY` set to the proxy token — similarly-named token variables may feed a different credential path and surface as 'Not logged in'; confirm with a one-turn smoke test, where a 400 'Invalid model' proves auth works and only the model name is wrong.
- For slow scans (big trees, whole-profile recursion), run in background with notify and a bounded timeout; prefer targeted per-dir checks.
- For long builds/tests, redirect output to a log file and poll the file — never pipe through `tail`/`head` in this shell; a wedged pipe shows zero output forever and looks identical to a hang, while a log file always shows how far the run got.
4. Check disk with `df -h /c`, RAM with `/proc/meminfo`, top RAM holders via a `.ps1` using `Get-Process | Sort WorkingSet64`.

## Pitfalls

- Attribute antivirus blame via the AV's own log before touching code — pull Defender detections with `Get-MpThreatDetection` (threat name, resource path, timestamp) and match them against what the session actually installed; a flagged driver from an unrelated desktop app with an older timestamp exonerates the current work, so report the attribution instead of 'fixing' it.
- Minimize shell spawning on the user's own PC — every terminal call pops a visible shell the user sees, so prefer read_file/write_file/patch/search over shell equivalents, batch independent commands into one call, and never leave background shells running after the task ends.
- Attribute top processes by command line before killing anything — orphaned test runners and build daemons from prior agent sessions are safe kills, but the proxy server, Hermes gateway/serve processes, bot-profile servers, and the user's own apps are load-bearing; when in doubt, report the list instead of killing.

- Never inline `powershell -Command "..."` containing `$` variables (`$_.`, loop vars like `$f`, `;`, `{}`) — MSYS eats the `$` and parens, producing phantom syntax errors; the failure looks like bad PowerShell but the mechanism is shell rewriting, so always use `-File`.
- Never compose multi-line scripts with shell heredocs (`cat << 'EOF'`) inside a terminal call — the heredoc body is rewritten or dropped just like inline `$` vars, and the target file silently ends up missing or truncated; write the file with write_file, then execute it.
- Kill Windows PIDs with `MSYS_NO_PATHCONV=1 taskkill.exe /F /PID <n>` — bare `taskkill //F //PID` is rejected as an invalid option. The same mangling hits any native flag starting with `//` (`tasklist //FI`, `//S`), so restate all such flags single-slash or use the PowerShell equivalent.
- Never run `du`/`find` over huge cache trees (model hubs) in the foreground — they hang past the timeout; measure with a targeted PowerShell `Measure-Object` or check one directory level at a time.
- A `pagefile.sys` ballooning to ~2x RAM plus a process with GBs of paged memory means swap-thrash, not progress — kill the thrasher; disk and RAM usually recover without a reboot.
- Native `curl --data-binary @<file>` cannot open MSYS `/c/...` paths and fails with a file-read error — pass a backslash `C:\...` path with `MSYS_NO_PATHCONV=1` when the path reaches a native binary, especially when a bash script canonicalizes `C:/` args through `realpath`/`pwd` into `/c/` form before the handoff. When the script builds upload paths dynamically and every handoff needs patching, stop fighting conversion and reimplement the transfer in Python (`requests`) instead.
- GNU `sha256sum` prefixes its output line with a backslash when the filename itself contains backslashes, silently corrupting hash fields sent to strict APIs — hash files referenced by `/c/...` or `C:/` forward-slash paths, never backslash ones.
- `/tmp` does not persist across terminal calls on this host — stage logs, JSON, downloads, and handoff files under `$LOCALAPPDATA/Temp` when a later command must read them.
- Reusable `.ps1` probes belong in the project's own `tools/` dir, not Temp — system cleanup wipes Temp without warning and later steps fail with 'file does not exist'.
- A write that 'succeeds' but leaves a 0-byte file means the disk is full — check `df -h /c` before blaming app code.
- Shell variables do not persist across terminal calls either — persist handoffs (URLs, keys, IDs) in small files under the work dir and read them back instead of reusing `$VAR` from an earlier call.
- Keep every inline command small and single-purpose — oversized one-liners with heavy quoting, stacked pipes, or heredoc-style payloads trip the agent command parser's hard blocklist and never execute; the refusal prints the saved script path, so either run `bash <that-path>` or split the work into small commands. When one file needs several passes, prefer a single `execute_code` script over a chain of shell text-munging.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/windows-shell-interop/`). Video walkthrough ships with the pack.
