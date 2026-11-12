# Indicators

Store chart indicators and visual studies in this directory.

All indicators use Pine Script v6. Unless noted otherwise, lengths count chart
bars. Current-bar values and crossover markers can change before the bar closes;
these scripts do not use future data.

## EMA

[`ema.pine`](ema.pine) plots three exponential moving averages on the price chart.

- Source defaults to close and can be changed.
- Lengths default to 5, 20, and 50 periods; each is adjustable.
- The three lines are orange, blue, and green respectively.

## HMA

[`hma.pine`](hma.pine) plots two Hull moving averages on the price chart.

- Source defaults to close; lengths default to 10 and 20 periods.
- Each dashed line is green when rising and red otherwise.
- An upward green triangle marks HMA 1 crossing above HMA 2; a downward red
  triangle marks HMA 1 crossing below HMA 2.

## ATR Channel

[`atr-channel.pine`](atr-channel.pine) plots an EMA with volatility bands on the
price chart.

- The center line is a 22-period EMA of close by default.
- Bands sit at plus/minus 1, 2, and 3 times ATR around the EMA.
- ATR length defaults to 13 periods. EMA and ATR lengths are adjustable.

## Elder DMI/ADX

[`dmi.pine`](dmi.pine) plots +DI, -DI, and ADX in a separate pane.

- Directional movement is divided by each bar's true range, then smoothed with
  an EMA to calculate +DI and -DI.
- ADX is an EMA of the normalized absolute difference between +DI and -DI.
- DI and ADX EMA lengths both default to 13 periods and are adjustable.
- +DI is green, -DI is red, and ADX is blue. This implementation uses EMA
  smoothing, so its values differ from Wilder-smoothed DMI/ADX.

## KDJ

[`kdj.pine`](kdj.pine) plots K, D, and J in a separate pane.

- RSV measures close within the highest-high/lowest-low range over 9 periods
  by default; a flat range produces an RSV of zero.
- K is a 3-period EMA of RSV; D is a 3-period EMA of K; J is `3 * K - 2 * D`.
  All three lengths are adjustable.
- Reference lines mark 20, 50, and 80. J can extend outside the 0–100 range.

## US 52-Week New Highs - New Lows

[`new-highs-new-lows.pine`](new-highs-new-lows.pine) measures market breadth using
the number of securities making 52-week highs minus those making 52-week lows.
It defaults to NYSE + NASDAQ; either exchange can also be selected individually.
The combined universe covers these two exchanges, rather than every US listing.

| Market | New highs                                                       | New lows                                                        |
| ------ | --------------------------------------------------------------- | --------------------------------------------------------------- |
| NYSE   | [`INDEX:HIGN`](https://www.tradingview.com/symbols/INDEX-HIGN/) | [`INDEX:LOWN`](https://www.tradingview.com/symbols/INDEX-LOWN/) |
| NASDAQ | [`INDEX:HIGQ`](https://www.tradingview.com/symbols/INDEX-HIGQ/) | [`INDEX:LOWQ`](https://www.tradingview.com/symbols/INDEX-LOWQ/) |

- **Calculation:** `net = new highs - new lows`. Combined mode sums both
  exchanges' highs and both exchanges' lows before subtracting.
- **Display:** green histogram bars above zero indicate more new highs; red bars
  below zero indicate more new lows. The optional orange EMA defaults to 10
  trading days. Raw highs and lows are available in the Data Window.
- **Timeframe:** calculation defaults to 1D, including on intraday charts. Keep
  the indicator's calculation timeframe at 1 day; other settings produce an
  explanatory error. Leave **Wait for timeframe closes** enabled to display
  daily values when available. Daily charts are recommended; weekly/monthly
  charts show a sampled daily observation, not a weekly/monthly sum.
- **Data:** the four source symbols can be changed in settings. Availability,
  history and security coverage depend on the provider and TradingView account.
  Missing inputs leave a gap; combined mode requires both exchanges. The EMA
  skips missing observations and is hidden on days with missing net breadth.
- **Repainting:** no lookahead is used. Current daily source counts and the EMA
  can change until their bars close, and provider revisions can affect history.
  Zero crossings describe changes in breadth; no trading alerts are generated.

## US Stocks Above 50-Day MA

[`stocks-above-50-day-ma.pine`](stocks-above-50-day-ma.pine) measures market
breadth using the percentage of stocks trading above their 50-day moving
average, following the "Stocks above 50-Day MA" indicator in Alexander Elder's
*The New Trading for a Living*.

- **Data:** defaults to
  [`INDEX:MMFI`](https://www.tradingview.com/symbols/INDEX-MMFI/), TradingView's
  "Percent of Stocks Above 50-Day Average". The symbol can be changed in
  settings, for example to
  [`INDEX:S5FI`](https://www.tradingview.com/symbols/INDEX-S5FI/) for S&P 500
  members. Security coverage, the moving-average method, and history depend on
  the provider and TradingView account.
- **Display:** a blue line in a separate pane, in percent, with dashed reference
  lines at 75% and 25%. Both levels are adjustable.
- **Signals:** a reversal near a reference line matters, rather than the level
  being reached. A red downward triangle at the top of the pane marks the line
  falling back below the upper level after being at or above it, which suggests
  a top is being completed. A green upward triangle at the bottom of the pane
  marks the line rising back above the lower level after being at or below it,
  which suggests a bottom is being completed. A turn that stops short of a
  level is not marked; move the level to include it. Markers describe breadth,
  rather than standalone buy/sell signals; no alerts are generated.
- **Timeframe:** follows the chart timeframe. Weekly charts are recommended for
  catching intermediate reversals; each weekly bar shows the week's last value.
  Charts below one day show the daily series.
- **Repainting:** no lookahead is used. The current bar's value and markers can
  change until the bar closes, and provider revisions can affect history. On
  charts below one day, historical bars carry the last completed day's value,
  while the realtime bar follows the current day's value.

## SPY Intraday Noise Momentum Signals

[`spy-intraday-noise-momentum.pine`](spy-intraday-noise-momentum.pine) shows the
signals of the
[SPY intraday noise momentum strategy](../strategies/README.md#spy-intraday-noise-momentum)
on the price chart, with the same rules, inputs, and data. Use a 1-minute SPY
chart with regular trading hours.

- **Lines:** the upper and lower bounds that the session's next check compares
  against, and the session VWAP. The bounds step at each 30-minute mark and end
  after the last check.
- **Markers:** green "Long" and red "Short" triangles mark entries and
  reversals; gray "Flat" crosses mark exits at a check, and gray "Close"
  circles mark the end-of-day exit. Orange "Skip long" and "Skip short"
  crosses mark checks where the prior-move filter blocked a new position.
- **Background:** green while long, red while short.
- **Status table:** position, the next check time in exchange time, the
  current long trigger `max(upper, VWAP)` and short trigger `min(lower, VWAP)`
  (the VWAP keeps moving until the check), exposure as a multiple of equity,
  the prior move in daily volatilities with the daily volatility, and which
  side the filter blocks today. It can be hidden.
- **Alerts:** "Go Long", "Go Short", and "Go Flat" (exits at a check and at the
  end of the day). Create them with "Once Per Bar Close".

Signals are final when their 1-minute bar closes and do not repaint; on a
realtime bar the lines and table update with each tick. As in the strategy, a
live ex-date relies on TradingView reporting the dividend by the open. On an
early close missing from TradingView's session data (see the strategy notes),
no "Close" marker or "Go Flat" alert appears for a position still open at
the close; the position resets to flat at the next open. Sessions without
enough 30-minute or daily history show no bounds and no signals.
