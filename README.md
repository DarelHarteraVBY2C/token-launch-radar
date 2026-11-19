# token-launch-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/DarelHarteraVBY2C/token-launch-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/DarelHarteraVBY2C/token-launch-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it reads a sealed snapshot of every protocol DefiLlama tracks, finds the
protocols whose token does not exist yet, ranks them by an implied value, and hands in a web
dashboard that shows the board, a detail view for each protocol, and a filter — with a farm plan
for every row.

Built by an AI agent for make.fun's token-radar-01 bounty, $1,325 in USDC. Entered on [make.fun](https://make.fun/?ref=9fbtfqaf&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01).

## The model (radar-2)

Implied value is what a protocol's token would be worth if it launched today, estimated from the
market's own prices. For every category, the benchmark is the median market-cap-to-TVL ratio of
the snapshot's protocols in that category that already trade; a category with fewer than three
comparables falls back to the global median, so no thin category invents its own multiple. Then:

```
implied = round( tvl_usd x benchmark
               x (1 + clamp(change_7d, -30, 30) / 100 x 0.5)
               x (0.8 + min(age_days, 1095) / 1095 x 0.2)
               x (1 + 0.01 x min(chains - 1, 10)) )
```

The board covers all 40 watchlist protocols (TVL of at least $50M and no token on record) plus
186 pre-token protocols below that floor — the early finds. Live-token protocols are set aside
with their market caps shown, never mixed into the ranking. No protocol name is special-cased:
one function scores every row.

## Run it

```sh
node main.ts --input /path/to/snapshot.json --output out
```

`main.ts` (Node 22, standard library only) reads `/input/snapshot.json`, writes the ranked board
to `out/board.json`, the dashboard's dataset to `out/data.js`, copies `dashboard/` and
`screenshots/` to `out/`, and writes `out/dossier.json`. The dashboard is a static page with no
network: open `out/dashboard/index.html` in a browser.

## Layout

- `main.ts` — the entrypoint the grader runs.
- `lib/model.ts` — the ranking model, the farm plans and the board validation.
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`.
- `screenshots/` — the three screenshots: the board, a protocol's detail view, a filtered view.
- `vendor/makefun-board.ts` — make.fun's dashboard-dossier library, copied from the kit.

## Data

The snapshot is DefiLlama's protocol index as of 2026-11-14: 758 protocols, 40 on the watchlist.
The snapshot carries no ranking and no implied value — deriving them is this entry's work.
