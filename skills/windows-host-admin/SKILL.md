---
name: windows-host-admin
description: 'Use when configuring Windows power, lid, sleep, or WiFi.'
---

# Windows Host Admin

Darren runs Hermes on a Windows 11 notebook and wants lid-off uptime with aggressive WiFi reconnect. Work through `terminal` (git-bash/MSYS shell: POSIX syntax, native Windows tools like `powercfg`, `netsh`, `reg`, `powershell`).

## Lid-off uptime procedure (in this order)

1. Inspect: `powercfg /list`, `powercfg /q SCHEME_CURRENT SUB_SLEEP`, `netsh wlan show profiles`, `netsh wlan show interfaces`.
2. Lid close to do-nothing (AC + DC): `powercfg /setacvalueindex SCHEME_CURRENT SUB_BUTTONS LIDACTION 0` and the `setdcvalueindex` twin.
3. Never sleep/hibernate: `STANDBYIDLE 0` + `HIBERNATEIDLE 0` (AC + DC), `HYBRIDSLEEP 0` (AC + DC).
4. Wake timers on: `RTCWAKE 1` (AC + DC) so scheduled work can wake the machine.
5. WiFi maximum performance (AC + DC): `powercfg /setacvalueindex SCHEME_CURRENT 19cbb8fa-5279-450e-9fac-8a3d5fedd0c1 12bbebe6-58d6-4636-95bb-3217ef867c1a 0` plus the DC twin.
6. USB selective suspend off (AC + DC), then `powercfg /setactive SCHEME_CURRENT` to apply.
7. Every saved WiFi profile to auto-connect: `netsh wlan set profileparameter name="<profile>" connectionmode=auto` in a loop over `netsh wlan show profiles`.
8. Verify every write by read-back before reporting (registry for lid, `powercfg /q` for sleep/WiFi, `netsh wlan show interfaces` for link state).

## Pitfalls

- Verify the lid-close action in the REGISTRY, never trust `powercfg /q` for it — the lid setting is hidden from `powercfg /q` output (Attributes=1) even while set correctly; read `HKLM\SYSTEM\CurrentControlSet\Control\Power\User\PowerSchemes\<active-scheme>\4f971e89-eebd-4455-a8de-9e59040e7347\5ca83367-6e45-459f-a27b-476b1d01c936` and confirm `ACSettingIndex`/`DCSettingIndex` are `0x0`.
- No connection can exist DURING sleep or hibernate — the OS cuts the network stack by design; when the user asks for connectivity while asleep, push back once and deliver prevention (never-sleep + lid do-nothing) instead.
- `powercfg /hibernate off` requires elevation — treat denial as optional cleanup, since `HIBERNATEIDLE 0` already prevents auto-hibernation; report it as a left-over, not a failure.
- `netsh wlan set autoconfig` may be denied without admin — check `netsh wlan show autoconfig` first, since already-enabled is the common case and denial changes nothing.
- `Get-/Disable-NetAdapterPowerManagement` can fail with WMI error 31 on Intel WiFi drivers — the powercfg WiFi max-performance setting is the reliable lever, so cmdlet failure is not task failure; fall back to a manual Device Manager step only if drops are actually observed.
- The Modern Standby networking GUID may not exist on a given machine — if `powercfg /q` shows no such setting, skip it; absence is hardware-dependent, not an error to chase.
- Single-quote PowerShell `-Command` strings in this terminal — git-bash expands `$variables` inside double quotes and silently breaks the script before PowerShell ever sees it.
- When uptime is suspect, read the service's own logs before trusting `powercfg` — a multi-hour log gap ending in a reconnect/timeout retry storm means the machine idled despite correct settings, so investigate the upstream link next (a phone hotspot sleeps on its own schedule regardless of PC power settings).
- Locate long-running services by the PID their own status command reports — the Hermes gateway runs as `python`, so filtering `tasklist`/`Get-Process` for `Hermes.exe` misses it and makes a live gateway look dead.
- Verify the gateway reboot chain end-to-end, never trust `hermes gateway status` alone — it reports the Startup shim as healthy from the shim file's existence while the chained `gateway-service/` target it delegates to may be missing (restores wipe it and leave a silent dead link that quits on every login), so confirm the target `.vbs`/`.cmd` exists and repair with `hermes gateway install`, which regenerates the launchers without restarting a live gateway (a schtasks UAC denial falls back to the Startup folder by design, not failure).
