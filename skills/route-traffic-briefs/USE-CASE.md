# route-traffic-briefs

**Pack:** system-utils

## Use case

'Use when checking road traffic between two places.'

## How to use it

## Procedure

1. **Resolve both ends to coordinates first** — try `search` with progressively broader queries (full street, then landmark plus city) and fall back to the nearest station or district result, since short street names frequently geocode to zero results.
2. **Route by coordinates, not names** — pass `"<lat>, <lon>"` pairs to `distance` and `directions` so a geocode miss cannot silently reroute the trip.
3. **Label the estimate as free-flow** — report distance plus the router duration as the no-traffic baseline and add a peak-hours expectation band, since the router has no live-congestion feed.
4. **Attach tap-to-open live checks** — always include a Google Maps directions link and a Waze link built from the same coordinates, because only those show current jams, closures, and accidents.
5. **Apply the local restriction calendar** — check odd-even schedules, weekday-only windows, and event-day closures for the date in question (e.g. date parity decides which plates run), since the restriction changes the answer more than the route does.

## Pitfalls

- Confirm road-traffic vs website-traffic meaning before routing — the same word selects entirely different tools.
- Never present router steps as turn-by-turn gospel without a live-map cross-check — road names and ramps drift from ground truth.
- Keep the brief airy: baseline, today's rules, live links, then what remains unknown.

## Install

Copy `SKILL.md` into your agent's skills directory (Hermes: `~/.hermes/skills/route-traffic-briefs/`). Video walkthrough ships with the pack.
