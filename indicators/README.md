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

| Market | New highs | New lows |
| --- | --- | --- |
| NYSE | [`INDEX:HIGN`](https://www.tradingview.com/symbols/INDEX-HIGN/) | [`INDEX:LOWN`](https://www.tradingview.com/symbols/INDEX-LOWN/) |
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

