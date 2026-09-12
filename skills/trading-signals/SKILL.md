---
name: trading-signals
description: 'Use when delivering forex or crypto trading signals.'
---

# Trading Signals

Deliver verified-live, compact trading signals for forex/crypto to THIS user's specifications. Educational only — never guarantee profit, never fabricate fills or win totals.

## Procedure

1. Fetch live prices before every call: BTC via CoinGecko `simple/price?ids=bitcoin&vs_currencies=usd`; EURUSD by inverting the EUR rate from `open.er-api.com/v6/latest/USD`; event context via web search (Forex.com pair-of-week for ECB/CPI/oil regime). Quote only numbers actually returned. For real candles use `data-api.binance.vision/api/v3/klines?symbol=BTCUSDT&interval=15m&limit=N` — `api.binance.com` fails from this host with an SSL hostname mismatch, so never offer it as the path; CoinGecko `/coins/bitcoin/ohlc` is the fallback. State plainly when you cannot see the user's broker chart (no screenshots unless they send them). On re-sends, re-fetch first; if price hasn't moved, say the zone stands and why instead of inventing a new level.
2. Set bias from structure + events: state resistance/support, the event guard (skip forex buys into 200-day/trendline resistance ahead of ECB/CPI), and an explicit invalidation (e.g. H1 close through the level = hands off).
3. Send the signal in compact Telegram rows — one message, emoji labels, one field per line, no walls:
   🟢/🔴 BIAS + timeframe / 💰 Entry zone / 🛑 SL / 🎯 single TP / ⚠️ risk / ✅ breakeven rule.
   Default is ONE TP — a second TP goes out only when the user explicitly asks for TP2 (TP1/TP2 splits were rejected as clutter).
   Never mid-range chase (e.g. no BTC entries mid 78–80k range); fade edges only.
4. Enforce risk on every signal: max 1% (0.5% on 1m), TP1 take half and move SL to breakeven, 2-loss stop on 1m days.
5. Follow the timeframe ladder: 1H bias / 15m entry / 5m trigger; 1m only for scalps into higher-timeframe levels. Default recommendation is 15m — push back respectfully when the user demands 1m (max noise, fees, blowup speed).
6. Log each signal run same-turn to the governing Multica issue via `multica issue comment add` (see multica-workspace live-sync); unverified user win claims stay labeled claimed-not-verified until receipts (pair + entry/exit + USD) arrive.
7. When asked to automate on Exness MT5: scaffold a demo-first MQL5 EA (fixed micro lot, max trades/day, single position, minutes-per-trade cap, daily-loss kill switch, timeframe guard), write it with write_file, verify byte count on disk, deliver via the MEDIA: path plus a 5-row install card (folder → compile → attach → inputs → AlgoTrading ON). State up front that MT5 Android cannot run EAs — desktop/VPS only, phone is monitor-only. Daily level changes go through EA Inputs, never re-upload/recompile. Default to a semi-auto approval gate (your call plus user GO per trigger, full-auto OFF) whenever the user wants a hand on every fill. Never plug live without explicit approval and never accept credentials in chat.

## Rules

- An unlogged win did not happen — require receipts before counting any P/L toward the record.
- Hold the standing size discipline when the user demands escalation — restate the same signal at standing size and name the dollar risk of the requested size; never endorse the escalation.
- Never present a signal without SL, TP, invalidation, and risk size in the same message.
- Never echo the user's own numbers back as your call — label whose level each entry is (yours vs theirs) and correct to the live price when the market has moved.
- Mentor tone in the money channel: direct, no hype, no gambling encouragement; demo-first for unproven edges.
