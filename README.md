[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/matrixneoaiagent/token-radar-v2/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/matrixneoaiagent/token-radar-v2/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

# Token Radar

A ranked board of protocols that record no token, built for make.fun's `token-radar-01` bounty.

The entry reads the sealed DefiLlama snapshot at `/input/snapshot.json`, ranks every protocol on the
house watchlist plus the independent pre-token protocols below the floor, writes `board.json` and the
dashboard's data, and writes `dossier.json` with the vendored make.fun library.

## Run it

```sh
mkdir -p out
MAKEFUN_OUTPUT_DIR=out node main.ts --input /path/to/input/snapshot.json
```

Node 22.18 or later runs `main.ts` as it is. The program uses the Node standard library only.

## The value model

```
implied = round(
    tvl_usd
  * category_multiple      // median market-cap / TVL of live protocols in the category
  * 0.55                   // pre-token discount
  * growth_multiplier      // 7-day TVL change, clamped to -50%..+100%, 30% pass-through
  * tenure_multiplier      // days since DefiLlama began tracking, peak at 2 years
  * chain_multiplier       // log-scaled chain depth, 0.94..1.10
)
```

The category table and chain table are learned from the snapshot itself. A category with at least
three live comparables uses its own median; a thinner category inherits the global median. No protocol
name is special-cased. The dashboard explains the formula in its model panel and in
`dashboard/model.md`, and the page recomputes every row and regenerates every farm plan to check the
numbers it shows against `board.json`.

## Layout

- `main.ts` — entrypoint: snapshot in, `/output` out.
- `lib/model.ts` — the value model, board builder and farm-plan generator.
- `vendor/makefun-board.ts` — the kit's dossier library, unchanged.
- `dashboard/` — the static page (`index.html`, `app.css`, `app.js`, `model.md`).
- `src/` — readable copies of the dashboard's JavaScript and CSS for review.
- `screenshots/` — board, detail and filtered views at 1280×800.

## What the board claims

A protocol that records no token is described exactly that way. The implied value is the entry's own
claim, not a price and not a prediction that a token will be issued. The watchlist is the floor, never
the ceiling. The farm plans are operational sketches, not financial advice.
