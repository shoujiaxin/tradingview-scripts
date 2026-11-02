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

## SPY intraday noise momentum

[`spy-intraday-noise-momentum.pine`](spy-intraday-noise-momentum.pine) trades
intraday momentum in SPY when the price leaves a noise band around the open,
after Zarattini, Aziz & Barbon (2024), "Beat the Market: An Effective Intraday
Momentum Strategy for S&P500 ETF (SPY)", plus a filter on the previous
session's move. Use a 1-minute SPY chart with regular trading hours; other
timeframes and extended hours stop the script with an explanatory error.

- **Checks:** every 30 minutes from the open (10:00, 10:30, ...) through 30
  minutes before the close, on the close of the 1-minute bar ending then. Go
  long when the close is above both the upper bound and the session VWAP,
  short when it is below both the lower bound and the VWAP, and flat
  otherwise. A reversal is one order.
- **Bounds:** upper = `max(open, previous close) * (1 + multiplier * width)`
  and lower = `min(open, previous close) * (1 - multiplier * width)`. The
  width at a check is the mean of `|close / open - 1|` at that time of day over
  the last 14 sessions. The previous close is the official daily close, less
  any dividend going ex today. VWAP weights each 1-minute bar's
  `(high + low + close) / 3` by its volume since the open.
- **Prior-move filter:** the prior move is the previous session's
  close-to-close return divided by the daily volatility. A new long is skipped
  when it is at least 1, and a new short when it is at most -1; the strategy
  stays flat instead, closing any opposite position. Holding a position and
  going flat are never blocked. The filter can be turned off.
- **Sizing:** shares are fixed at the open to
  `floor(equity * min(4, 2% / daily volatility) / open)`, where the daily
  volatility is the sample standard deviation of the last 14 dividend-adjusted
  close-to-close returns.
- **Exit:** any position closes one minute before the close, on the close of
  the 1-minute bar ending at 15:59 (labeled 15:58 on the chart). On 13:00
  early closes the checks run through 12:30 and the exit comes at 12:59.

The rule parameters are inputs: band lookback (14 sessions), band width
multiplier (1), volatility window (14 sessions), daily volatility target (2%),
max leverage (4), and the prior-move filter switch and threshold (on, 1 daily
volatility). The 30-minute check schedule and the exit minute are fixed.

Orders fill at the close of the bar that triggers them. Defaults in Strategy
Properties: initial capital 1,000,000, commission 0.0045 per share (0.0035
commission plus 0.001 slippage, so slippage itself is 0), and 0% margin so
that positions up to 4 times equity are not cut by margin checks. Adjust costs
for the intended broker.

Band widths come from regular-session 30-minute bars, whose closes at the
30-minute marks are the 1-minute closes the rules use. The previous close and
daily volatility come from daily bars, because the last 1-minute close can
differ from the official close by several cents. Both histories reach further
back than the chart's 1-minute bars, so trading starts with the chart's first
session that begins at the open; a session whose history is incomplete is
skipped. Results cover only the 1-minute bars the chart loads, which depend on
the TradingView plan.

Session lengths come from TradingView's session data, which for SPY lists
13:00 early closes from July 2019 on, except those in November and December
2020 (as of November 2026). On an early close it does not list, the script
plans a full session: the bar ending at 13:00 (labeled 12:59) counts as a
check, and a position still open at the close is closed on the next session's
first bar.

Signals use completed bars only: history values are read one 30-minute or
daily bar late, and the strategy calculates on bar closes. Historical bars
apply a dividend from its ex-date's open; on a live ex-date the adjustment
relies on TradingView reporting the dividend by the open.
