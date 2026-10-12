# Price Action for TradingView

`[ad] Price Action` is a Pine Script v6 indicator that puts the main price action tools on one chart:

- **Market structure** - every confirmed swing is labelled `HH`, `HL`, `LH` or `LL`.
- **Structure breaks** - `MSS` (market structure shift: the trend changes direction, solid line) and `BOS` (break of structure: the trend continues, dashed line).
- **Order blocks (OB)** - the base candle of the move that broke structure.
- **Fair value gaps (FVG)** - three-candle imbalances, bullish (blue) and bearish (orange), with an optional 50% midline.
- **Untaken highs and lows (`SFP?`)** - dashed lines on swing highs/lows that price has not taken yet, plus an `SFP` mark when a candle wicks through such a level and closes back inside (swing failure pattern).
- **Trend table on 2 timeframes** - the trend of the chart timeframe and of a higher one (default: 4x the chart timeframe, e.g. 24m -> 96m) and whether they agree.
- **EMAs** - six slots (defaults 50, 100 and 200 on; 9, 21 and 800 off).
- **VWAPs** - daily and weekly (monthly optional) session VWAPs with optional standard deviation bands, an anchored VWAP from the last MSS and an anchored VWAP from a date.

## How it works

- **Swings**: a swing high needs `Swing strength` lower highs on each side (a swing low: higher lows). A swing is therefore confirmed `Swing strength` bars after the swing candle, and its label appears with that delay.
- **Trend and breaks**: a closed candle beyond the latest swing high is a bullish break, beyond the latest swing low a bearish break. If the trend was down (or unknown), the break is an `MSS` and the trend turns up; if the trend was already up, it is a `BOS`. Bearish breaks mirror this. Each swing can be broken only once.
- **Order blocks**: on every break the indicator looks back `Look for the base candle` bars for the extreme candle of the move (lowest low for a bullish break) and takes the closest opposite-coloured candle at most 3 bars before it (the last down candle before an up move). The block is the full range of that candle. It is kept only if the breaking candle closed at least `Impulse strength` x ATR(14) beyond it and it does not overlap a block that is already drawn. A block is removed when price closes (or wicks) through its far edge, or when it is older than `OB life`.
- **Fair value gaps**: bullish when the low of a candle is above the high of the candle two bars earlier, bearish when the high is below the low two bars earlier; the gap must be at least `Min gap size` x ATR(14). A gap is removed when it is fully filled (or closed through) or older than `FVG life`.
- **Untaken highs/lows**: every new swing gets a dashed line `Line length` bars long with an `SFP?` tag. The line disappears when price trades through the level or when it expires. If the candle that took the level closes back on the other side, an `SFP` label marks it.
- **Trend table**: the higher-timeframe trend uses the same rules on higher-timeframe candles and is read from the **last completed** higher-timeframe candle (`request.security` with `lookahead_on` and a one-bar offset), so it never repaints; it changes at most once per higher-timeframe candle. The bottom row says whether both trends agree or whether the chart timeframe is a pullback/bounce inside the higher trend.
- **VWAPs**: session VWAPs reset at the start of each day/week/month (exchange time) and are hidden on chart timeframes of that length or longer. The anchored VWAP from the last MSS starts at the extreme candle (lowest low / highest high) of the leg that made the last MSS and is redrawn when a new MSS happens. The anchored VWAP from a date starts at the first candle at or after the chosen date.

## Settings

- **General** - swing strength (default 5).
- **What to show** - switch each feature on or off (structure, SFP lines, MSS, BOS, OB, FVG, table, EMAs, VWAPs).
- **Untaken highs / lows** - line length (100 bars), max lines per side (5), mark confirmed SFP.
- **Order blocks** - impulse strength (1.5 ATR), look-back for the base candle (15 bars), max OBs per side (5), life in bars (150), removal rule (close through / wick through).
- **Fair value gaps** - minimum size (0.1 ATR), max FVGs per side (5), life in bars (150), removal rule (wick through / close through), 50% midline.
- **Trend table** - higher timeframe (empty = 4x the chart timeframe), position, text size.
- **EMAs** - source, line width, and for each of six EMAs: on/off, length, colour.
- **VWAP** - source, daily/weekly/monthly VWAP with colours, bands and band multiplier, anchored VWAP from the last MSS, anchored VWAP from a date.
- **Style** - colours for bullish/bearish structure and OBs, bullish/bearish FVGs, SFP lines, structure labels, zone text, and the zone fill transparency.

## Alerts

- One `alert()` call reports every event of a closed candle in one message (`Bullish MSS`, `Bearish MSS`, `Bullish BOS`, `Bearish BOS`, `SFP of a swing high/low`, `New bullish/bearish OB`), for example `XRPUSDT 24: Bearish MSS; New bearish OB; close 1.4003`. Create it with the condition `[ad] Price Action` -> `Any alert() function call`.
- Separate conditions are also available for `Bullish MSS`, `Bearish MSS`, `Bullish BOS`, `Bearish BOS`, `SFP high` and `SFP low`.
- Alerts keep a snapshot of the script and its settings: recreate them after changing settings or updating the script.

## Installation

1. Open the Pine Editor in TradingView.
2. Create a new indicator and paste the contents of [`price-action.pine`](price-action.pine).
3. Click **Add to chart**.

## Good to know

- Structure labels appear `Swing strength` bars after the swing candle. Breaks, order blocks, gaps and SFP marks are drawn only on closed candles. The untaken-level lines and the zones update live on the current candle (a line can disappear intrabar when price takes the level).
- The anchored VWAPs and the session VWAPs need volume data; on symbols without volume they are not drawn.
- The anchored VWAP from the last MSS and the break lines are limited to about 4900 bars back (a TradingView drawing limit).
- TradingView keeps at most 500 lines and 500 labels per script, so structure labels and break lines far back in history are removed automatically.
- Order blocks, gaps and levels are reference zones, not trade signals or predictions.

## Version

1.0.0 - see [CHANGELOG.md](CHANGELOG.md).

## License

Mozilla Public License 2.0, see [LICENSE](LICENSE).

This is an open-source charting tool, not financial advice.
