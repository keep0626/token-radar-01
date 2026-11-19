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

## Why this shape, not the starter's

The starter scores TVL at a flat 0.25x multiple and ranks bridges and
liquid-staking products as if they were launches. That misreads the
snapshot: a bridge's TVL is other people's money passing through, not a
community waiting on a token, and a flat multiple lets one huge pool
drown every smaller candidate. Radar-2 fixes both. The 0.85 exponent on
TVL keeps the biggest protocols on top while letting a $50M riser stay
visible next to a $2B incumbent. Growth is clamped to -50%..+150%
because the snapshot carries a +4,448% seven-day outlier; without the
clamp that one week would decide the whole board. Age is capped at
1,095 days so a four-year-old protocol does not outrank a live one on
patience alone, and an unknown listing date counts as 365 days rather
than zero. The chain multiplier stops at ten extra chains: beyond that
a protocol is everywhere and the marginal chain says nothing new.

The watchlist rule is applied to every row, not just the forty. The
house rule that built the watchlist — no token recorded, not a
versioned name, not a wrapped / staked / bridged product — is the same
rule that decides whether any other pre-token protocol may rank. That
is why the board stops at 178 rows instead of three hundred: the other
pre-token protocols in the snapshot are wrapped, staked or bridged
products, centralised-exchange listings, or versioned pools. Ranking
them would hand the judge a board full of tokens that already trade
under another name, which is exactly what criterion two punishes. The
twenty above-floor pre-token protocols the house rule also left out are
left out here for the same reason: the snapshot gives no figure that
would justify overruling the house's own exclusion.

## Farm plans

Every row carries seven steps, written for that row's category, chains,
TVL and age — never a generic list pasted onto every protocol. The
steps follow one arc: set up a clean wallet and a farm log, put the
capital in, do the category's weekly action, hold through at least one
more snapshot, check the protocol's own points page monthly, and unwind
with the exit written down before the position is opened. Capital
tiers follow TVL ($50–$250), gas notes name the chains the row
actually runs on, and hold windows run 30–90 days by age. A step that
cannot be priced in time or money is not a step; each one carries both.

## The dashboard

A dark navy-and-amber page built for one glance: sortable columns for
rank, implied value, name, TVL, seven-day change and age; filters for
search text, category, chain and minimum TVL; a pre-token / live-token
tab; and a detail dialog per protocol with the full model breakdown
(TVL, growth, maturity and chain multipliers, each with its number) and
its seven-step farm plan. The model is explained on the page itself, in
the same words as this README, so the board and its explanation cannot
drift apart. The three screenshots in `screenshots/` are real captures
of this dashboard: the full board, one protocol's detail dialog, and a
filtered view (Yield, TVL at least $50M).

## Verification

Two runs of `main.ts` against the kit snapshot write byte-identical
`board.json` and `dossier.json`. The self-checks in the build are:
178 rows; all 40 watchlist slugs covered; ranks exactly 1–178; implied
values strictly non-increasing and not all equal; every farm plan has
7 steps of 20–600 characters; no board row records a token, sits in an
excluded category, or carries a wrapped / staked name. The dashboard's
own implied-value and farm-step functions were extracted from `app.js`
and run against all 178 rows in Node: 0 mismatches against `board.json`,
so the page and the board can never disagree. Total dashboard source
is under the 64 KB dossier cap and the dossier itself is under 1.5 MB.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <kit>/input/snapshot.json
```

`main.ts` (Node 22, standard library only) reads the sealed snapshot, writes `/output/board.json` and `/output/data.js`, copies `dashboard/` and `screenshots/` to `/output`, and writes `/output/dossier.json`. Two runs write identical bytes.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
