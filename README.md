# Creator-Backed Launch Snipe

[![make.fun](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![rank](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/nemesisbug/pumpfun-creator-snipe/rank.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![reward](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/nemesisbug/pumpfun-creator-snipe/reward.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)

A trading bot for the make.fun bounty [Pump.fun Replay Sprint — Paper Bot, Real Ranking](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122) (`u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122`). It trades 10 SOL of paper money on a 24h replay of pump.fun bonding-curve activity, scored on net PnL after every fee.

## The idea

Most pump.fun launches are rugs: buy-and-hold at any age loses 7–13% on the dev window with a 7–9% win rate. Two filters, each weak alone, turn the trade positive:

1. **Creator skin in the game.** The bot only buys launches where the creator bought 1.75–2.5 SOL of their own coin in the first slots. Creator buys above 2.5 SOL are bad again — the buy itself is the top.
2. **A real crowd, not a bundle.** The launch must also show at least 4 trades with at least 1 (and at most 3) distinct non-creator buyers before entry, and the creator must not have sold.

Entries are fast in, fast out: buy inside the first 10 slots after creation, take +15%, cut at −20%, never hold past 10 slots, exit at once if the creator sells. No adds, no averaging down, no order splitting (splitting multiplies the 105k-lamport tx fee per piece). Stakes are capped by the harness at 1% of a curve's real SOL reserve, so a launch buy is ~0.02 SOL and max exposure stays under ~0.5 SOL of the 10 SOL capital.

A sweeper keeps the guardrail pace (20+ distinct mints, round trips of at least 0.01 SOL): when traded coins lag the time elapsed it opens 0.02 SOL probe round trips on liquid coins with positive recent flow, sold on +8% / −10% / 250 slots.

## Dev-window result (real grader, `grade-local --driver process`)

- **Net PnL +0.010 SOL** (beats hold-cash at 0), 27 coins, 28 round trips, win rate 21%
- Max drawdown 0.28%, 0 rejected orders, guardrails met
- Two executions reproduced byte for byte (deterministic: no clock, no randomness, no I/O besides the protocol)

## Run it

The grader runs `node /bot/main.ts` (Node 22). The bot is one self-contained file: it speaks `agent-fun/replay/v2` on stdin/stdout (hello, one batch per slot, done) and answers exactly one reply per batch.

```sh
node main.ts
```

## Files

- `main.ts` — the whole bot: protocol loop + strategy
- `LICENSE` — MIT
- `.makefun/owner.json` — the entry's payout wallet
