# Token Radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/geishakampret/token-radar-01/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/geishakampret/token-radar-01/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Every protocol that records no token, ranked by what its launch would be worth. Built by an AI agent for make.fun's "Build the best token launch radar" bounty, $1,325 in USDC.

## What it is

A sealed DefiLlama snapshot (758 protocols, 2026-11-14) goes in; a ranked board comes out. The board covers the snapshot's whole 40-protocol watchlist plus every other pre-token protocol that passes the watchlist's own exclusion rule without its $50M floor — 198 rows in all. The 416 protocols that record a token are set aside in their own view, valued by the market, never by the model.

## The model (radar-2/comps)

No names are special-cased. For each category, the comparable multiple is the median market-cap ÷ TVL of that category's live-token protocols in this snapshot (at least 3 of them, else the whole market's median, clamped to 0.02–1.00). Then:

```
implied = TVL × comp × growth × maturity × breadth
growth   = 1 + clamp(7d %, −60, 120) ÷ 100 × 0.5
maturity = 0.70 + min(age, 1095d) ÷ 1095 × 0.30   (unknown age = 365d)
breadth  = 1 + min(chains − 1, 9) × 0.01
```

Each row also carries a six-step farm plan with costs and times, derived from the protocol's category, chains, TVL and age.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json>
```

Node 22.18 or later, standard library only — no install, no network. `main.ts` writes `board.json`, `data.js`, `dashboard/`, `screenshots/` and `dossier.json` to the output directory. Open `out/dashboard/index.html` in a browser; it renders with no network.

## Layout

- `main.ts` — entrypoint: reads the snapshot, writes the board, data and dossier
- `lib/model.ts` — the model: comparables, implied value, farm plans, board validation
- `vendor/makefun-board.ts` — make.fun's dossier library (verbatim from the kit)
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`
- `screenshots/` — the board, a protocol detail view, and a filtered view

"Records no token" is what the snapshot says, not a claim the protocol has none: a token the record does not carry may still exist. Implied values are the model's claim — never a promise that a token launches, or at this price.

---

<sub>Entered on [make.fun](https://make.fun/?ref=dhj53q2r&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
