# dotnet-desktop-ship

**Pack:** ship-deploy

## Use case

'Use when shipping .NET Windows desktop apps.'

## How to use it

## Procedure

1. Build Release, zero warnings. Run the full suite.
2. Publish the shippable shape (loose self-contained folder by default — see single-file rule), then smoke-test the PUBLISHED artifact, not bin: launch it cold, drive it via the `windows-uia-verify` workflow, and keep reusable probe scripts in the repo's own `tools/` directory.
3. Stamp Version/AssemblyVersion/FileVersion in the csproj before the final publish; confirm the stamp on the shipped exe afterwards.
4. Update README (correct publish path, real troubleshooting), CHANGELOG, and a live screenshot of the shipped build on every release.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/dotnet-desktop-ship/`). Video walkthrough ships with the pack.
