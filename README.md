# zenpub

Public, read-only EOD (end-of-day) price data for the Nifty 100, published
daily by [zenback](https://github.com/qxakshat/zenback)'s
`publish-eod-prices` GitHub Actions workflow after NSE market hours. The
Zenfolio app reads this repo directly (over a CDN) for holding-level price
history — no other third-party price source is used client-side.

## Layout

```
manifest.json                          -- current Nifty 100 symbol list,
                                           schema, last-updated date
stock/prices/<YYYY_MM>/<SYMBOL>.json   -- one month of daily bars for one
                                           symbol, e.g. stock/prices/2026_08/RELIANCE.json
stock/prices/latest/<SYMBOL>.json      -- the same symbol's trailing ~5
                                           years, rewritten daily
```

Each bar:

```json
{"date": "2026-08-03", "open": 1315.2, "high": 1319.0, "low": 1306.0, "close": 1319.0, "volume": 7508023.0}
```

A monthly file is a JSON array of bars for that month, sorted by date. The
`latest/<SYMBOL>.json` file is the same shape but holds a rolling window —
it exists so a client can fetch **one file per holding** regardless of
which range (1M/6M/1Y/5Y) the user picks, slicing the range client-side
instead of stitching together up to 60 monthly files.

## Why this repo exists

Prices are published here, as flat files, so the mobile app can fetch them
directly from a CDN (`cdn.jsdelivr.net/gh/qxakshat/zenpub@main/...` or
`raw.githubusercontent.com`) instead of routing every price-history request
through zenback's own free-tier VM — cheaper, faster (CDN-cached), and
keeps that VM's load to the things that actually need compute (the
optimiser, portfolio performance).

## Updating

Nothing here is meant to be edited by hand. Every commit comes from
zenback's `publish-eod-prices.yml` workflow (daily) or, rarely, its
`backfill_eod` job (initial/extended history). See
`zenback/serve/src/jobs/publish_eod.py` and `.../backfill_eod.py`.

## Source data

- Primary: NSE's own daily bhavcopy (`sec_bhavdata_full_*.csv`), equity
  (`SERIES == 'EQ'`) rows only.
- Fallback: Yahoo Finance (`yfinance`), used only for symbols bhavcopy
  didn't cover on a given day.

This repo carries no license file — it is generated data, not source code
someone else's build is expected to depend on.
