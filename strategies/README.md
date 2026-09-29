# Strategies

Store backtestable TradingView strategies in this directory.

## EFI zero cross

`efi-zero-cross.pine` is a long-only strategy using
`EMA(volume * change(close), 13)`. EFI length is adjustable; use a daily
chart for day-based periods. Entries require available volume and ATR data.

- Enter when EFI crosses above zero on a confirmed bar while flat.
- Close the long position when EFI crosses below zero on a confirmed bar.
- Attach a protective stop to every entry, using 2 times ATR(13) by default.
  ATR length and multiplier are adjustable. Signal-bar ATR is frozen and
  rounded up to whole ticks; the distance is measured from the actual entry
  fill. The stop does not trail or widen after entry.
- After a stop exit, wait for a new upward zero crossing before re-entering.

Market orders fill on the next available tick (normally the next bar's open).
The stop is submitted with the entry and can execute intrabar, including on
the entry bar. Gaps can produce fills beyond the stop price. Chart triangles
mark signals; TradingView's strategy markers show simulated order fills.
The plotted stop appears on bars where the strategy observes an open position;
an entry and stop exit within one bar may therefore have no plotted stop line.

Defaults in Strategy Properties: initial capital 100,000, position size 10%
of equity, no pyramiding, commission 0.1% per fill, and slippage 1 tick.
Adjust costs and position size for the intended market. EFI appears in a
separate pane; the protective stop and signal markers appear on the price chart.

Signals are calculated at bar close without future data. Use standard candles
for backtests; historical intrabar order sequencing depends on the broker
emulator's assumptions (or lower-timeframe data with Bar Magnifier).
