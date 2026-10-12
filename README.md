# Price Action for TradingView

`[ad] Price Action` is a Pine Script v6 indicator that puts the main price action tools on one chart:

- **Market structure** - every confirmed swing is labelled `HH`, `HL`, `LH` or `LL`.
- **Structure breaks** - `MSS` (market structure shift: the trend changes direction, solid line) and `BOS` (break of structure: the trend continues, dashed line).
- **Order blocks (OB)** - the last opposite-coloured candle before the move that broke structure.
- **Fair value gaps (FVG)** - three-candle imbalances. The gap that holds the **volume point of control** of its move is marked blue as `FVG · POC` with a red POC line; other gaps are grey (bullish) or orange (bearish).
- **Untaken highs and lows (`SFP?`)** - dashed lines on swing highs/lows that price has not taken yet, plus an `SFP` mark (only the newest ones) when a candle wicks through such a level and closes back inside (swing failure pattern).
- **Trend table on 2 timeframes** - the trend of the chart timeframe and of a higher one (default: 4x the chart timeframe, e.g. 24m -> 96m) and whether they agree.
- **EMAs** - eight slots, each with its own switch, length, colour and width (defaults: EMA 20 light blue 2 px, EMA 50 and EMA 100 1 px, EMA 200 black 2 px; 9, 800, 21 and 400 off).
- **VWAPs** - daily (teal) and weekly (purple) session VWAPs, monthly optional, optional standard deviation bands, an anchored VWAP from the last MSS and an anchored VWAP from a date. No text labels on the chart.

## How it works

- **Swings**: a swing high needs `Swing strength` lower highs on each side (a swing low: higher lows). A swing is therefore confirmed `Swing strength` bars after the swing candle, and its label appears with that delay.
- **Trend and breaks**: a closed candle beyond the latest swing high is a bullish break, beyond the latest swing low a bearish break. If the trend was down (or unknown), the break is an `MSS` and the trend turns up; if the trend was already up, it is a `BOS`. Bearish breaks mirror this. Each swing can be broken only once.
- **Order blocks**: on every break the indicator walks back from the breaking candle (up to `Look for the base candle` bars) to the last opposite-coloured candle: the last down candle before a bullish break, the last up candle before a bearish break. Its far edge must lie at least `Impulse strength` x ATR(14) from the breaking close. The block is the full range of that candle and is skipped if it overlaps a block that is already drawn. The box is drawn `OB length` bars to the right of its candle. It disappears as soon as price fills it (a wick reaches the far edge; optionally: a close beyond it) or after `OB length` bars.
- **Fair value gaps**: bullish when the low of a candle is above the high of the candle two bars earlier, bearish when the high is below the low two bars earlier; the gap must be at least `Min gap size` x ATR(14). The box is drawn `FVG length` bars to the right and disappears when the gap is fully filled (optionally: closed through) or after `FVG length` bars.
- **FVG with POC**: for every gap on the chart a volume profile is built over the move that created it - from the low the rise started from (the lowest low since the last swing high) to the highest high since the gap; for a bearish gap from the high the drop started from to the lowest low since. Each candle's volume is spread evenly over the price rows its range covers (`Volume profile rows`, default 40). If the point of control (the row with the most volume) lies inside the gap, the gap is coloured `FVG with POC` and gets a POC line. This automates the "fixed range volume profile from the swing low to the current high" check. The profile follows the move, so the mark can move to another gap while the move extends. `Show only FVGs with POC` hides the other gaps.
- **Untaken highs/lows**: every new swing gets a dashed line `Line length` bars long with an `SFP?` tag. A level stays on the chart until price trades through it (only the newest `Max lines per side` are kept). If the candle that took the level closes back on the other side, an `SFP` label marks it; only the newest `Confirmed SFP marks to keep` marks stay.
- **Trend table**: the higher-timeframe trend uses the same rules on higher-timeframe candles and is read from the **last completed** higher-timeframe candle (`request.security` with `lookahead_on` and a one-bar offset), so it never repaints; it changes at most once per higher-timeframe candle. The bottom row says whether both trends agree or whether the chart timeframe is a pullback/bounce inside the higher trend.
- **VWAPs**: session VWAPs reset at the start of each day/week/month (exchange time) and are hidden on chart timeframes of that length or longer. The anchored VWAP from the last MSS starts at the extreme candle (lowest low / highest high) of the leg that made the last MSS and is redrawn when a new MSS happens. The anchored VWAP from a date starts at the first candle at or after the chosen date.

## Settings

- **General** - swing strength (default 5).
- **What to show** - switch each feature on or off (structure, SFP lines, MSS, BOS, OB, FVG, table, EMAs, VWAPs).
- **Untaken highs / lows** - line length (100 bars), max lines per side (5), confirmed SFP marks to keep (2, 0 = off).
- **Order blocks** - impulse strength (1.5 ATR), look-back for the base candle (15 bars), max OBs per side (5), length in bars (150), removal rule (fully filled / close through).
- **Fair value gaps** - minimum size (0.1 ATR), max FVGs per side (5), length in bars (150), removal rule (fully filled / close through), 50% midline (off), mark the FVG with the volume POC (on), show only FVGs with POC (off), volume profile rows (40).
- **Trend table** - higher timeframe (empty = 4x the chart timeframe), position, text size.
- **EMAs** - source, and for each of eight EMAs: on/off, length, colour, width.
- **VWAP** - source, daily/weekly/monthly VWAP with colours, bands and band multiplier, anchored VWAP from the last MSS, anchored VWAP from a date.
- **Style** - colours for bullish/bearish structure and OBs, bullish/bearish FVGs, FVG with POC, POC line, SFP lines, structure labels, zone text, and the zone fill transparency.

## Alerts

- One `alert()` call reports every event of a closed candle in one message (`Bullish MSS`, `Bearish MSS`, `Bullish BOS`, `Bearish BOS`, `SFP of a swing high/low`, `New bullish/bearish OB`), for example `XRPUSDT 24: Bearish MSS; New bearish OB; close 1.4003`. Create it with the condition `[ad] Price Action` -> `Any alert() function call`.
- Separate conditions are also available for `Bullish MSS`, `Bearish MSS`, `Bullish BOS`, `Bearish BOS`, `SFP high` and `SFP low`.
- Alerts keep a snapshot of the script and its settings: recreate them after changing settings or updating the script.

## Where to get it

- TradingView: [[ad] Price Action](https://www.tradingview.com/script/KvepE35r-ad-Price-Action/) (open-source publication, add it with "Add to favorites" / "Use on chart").
- Source code: this repository.

## Installation

1. Open the Pine Editor in TradingView.
2. Create a new indicator and paste the contents of [`price-action.pine`](price-action.pine).
3. Click **Add to chart**.

## Good to know

- Structure labels appear `Swing strength` bars after the swing candle. Breaks, order blocks, gaps and SFP marks are drawn only on closed candles. The untaken-level lines and the zones update live on the current candle (a line can disappear intrabar when price takes the level).
- The POC check uses the chart candles' volume (no lower-timeframe data), so it is an approximation of a fixed range volume profile; it is recalculated on the last bar.
- The anchored VWAPs and the session VWAPs need volume data; on symbols without volume they are not drawn.
- The anchored VWAP from the last MSS and the break lines are limited to about 4900 bars back (a TradingView drawing limit).
- TradingView keeps at most 500 lines and 500 labels per script, so structure labels and break lines far back in history are removed automatically.
- Order blocks, gaps and levels are reference zones, not trade signals or predictions.

## Version

1.1.0 - see [CHANGELOG.md](CHANGELOG.md).

## License

Mozilla Public License 2.0, see [LICENSE](LICENSE).

This is an open-source charting tool, not financial advice.
