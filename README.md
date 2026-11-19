# token-radar-01

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/keep0626/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/keep0626/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Build the best token launch radar. A dark dashboard that ranks pre-token protocols by implied value and shows how to farm each one.

## The model (radar-2)

Implied value follows only from the snapshot's own figures — no protocol name is ever special-cased:

```
implied = round( $200,000 x (tvl_usd / 1,000,000)^0.85
  x (1 + clamp(change_7d, -50, 150) / 100 x 0.30)
  x (0.75 + min(age_days, 1095) / 1095 x 0.25)
  x (1 + 0.04 x min(chains - 1, 10)) )
```

TVL enters with diminishing returns, seven-day growth is winsorised so one wild week cannot dominate, age is a gentle maturity curve, and each extra chain adds 4% for distribution breadth.

## What it ranks

The board covers all 40 watchlist slugs, plus every other rankable pre-token protocol below the $50M floor — 178 rows in total. A protocol is rankable only when it records no token **and** is not a wrapped, staked or bridged product (no Bridge / Liquid Staking / Liquid Restaking / Restaked BTC / CEX category, no versioned pool name, no wrapped / staked / staking in the name). Live-token protocols are set aside in their own view with their market caps, never mixed into the ranking. Each row carries a seven-step farm plan generated from its category, chains, TVL and age, with the capital, gas cost and time of every step.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <kit>/input/snapshot.json
```

`main.ts` (Node 22, standard library only) reads the sealed snapshot, writes `/output/board.json` and `/output/data.js`, copies `dashboard/` and `screenshots/` to `/output`, and writes `/output/dossier.json`. Two runs write identical bytes.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
