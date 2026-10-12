# Changelog

## 1.1.0 - 2026-10-12

- FVG with POC: the gap that contains the volume point of control of its move is coloured blue (`FVG · POC`) with a red POC line; option to show only those gaps.
- Order blocks: the last opposite-coloured candle before the breaking move (instead of the extreme candle of the look-back window); removed as soon as they are fully filled (new default).
- Order block and FVG boxes are drawn a fixed number of bars to the right of their candle.
- `SFP?` lines stay until price takes the level (no expiry); only the newest confirmed `SFP` marks are kept (default 2).
- Eight EMAs with their own width; defaults EMA 20 light blue 2 px, EMA 50/100 1 px, EMA 200 2 px.
- Daily VWAP teal and weekly VWAP purple, 1 px, drawn without hiding the reset candle; the anchored VWAP has no text label; midline off by default.

## 1.0.0 - 2026-10-12

- First version: market structure (HH/HL/LH/LL), MSS and BOS, order blocks, fair value gaps with 50% midline, untaken highs/lows with SFP marks, trend table on two timeframes, six EMAs, daily/weekly/monthly VWAP with bands, anchored VWAP from the last MSS and from a date, alerts.
