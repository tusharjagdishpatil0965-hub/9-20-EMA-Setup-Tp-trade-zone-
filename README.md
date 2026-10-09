# 9-20-EMA-Setup-Tp-trade-zone-
TradingView Pine Script v5 indicator using 9/20 EMA trend, strong-candle filter and support/resistance to give BUY/SELL signals with auto ATR stop-loss and 4R take-profit. Built for crypto and gold.
# TP TRADE ZONE

A TradingView **Pine Script v5** indicator that combines a 9/20 EMA trend filter, strong-candle confirmation and short-term support/resistance to print **BUY / SELL** signals with an automatic **Stop-Loss** and a **4R Take-Profit** level.

Built and used for discretionary trading of **crypto** (BTC, ETH) and **commodities** (Gold / XAUUSD) on intraday to 1-hour timeframes.

![XAUUSD 1m overview](images/xauusd-1m-overview.png)

## Features
- 9 EMA and 20 EMA trend lines (blue / orange)
- Trend filter: only BUYs in an uptrend, only SELLs in a downtrend
- Strong-candle filter (candle body must exceed 0.5 x ATR)
- Two entry types: **EMA retest** and **support/resistance reversal**
- One-signal-per-trend logic to reduce repeated entries
- Automatic ATR-based Stop-Loss and fixed risk-reward Take-Profit lines
- Optional 10-bar support / resistance lines
- Built-in BUY and SELL alerts

## How It Works
1. **Trend** is defined by the EMAs: `EMA9 > EMA20` = uptrend, `EMA9 < EMA20` = downtrend.
2. **Momentum** is confirmed by a strong candle in the trade direction.
3. **Location** must be right: price retests the 9 EMA, or touches the recent support/resistance.
4. A signal is printed only if the **previous signal was in the opposite direction** (or none yet).
5. SL and TP lines are drawn from the signal candle.

## Signal Logic
   # BUY
| Setup | Conditions |
| **EMA Retest** | Uptrend, `low <= EMA9`, `close > EMA9`, strong bullish candle |
| **Support Reversal** | Uptrend, `low <= support x 1.002`, strong bullish candle, `close > EMA9` |

   # SELL
| Setup | Conditions |
| **EMA Retest** | Downtrend, `high >= EMA9`, `close < EMA9`, strong bearish candle |
| **Resistance Reversal** | Downtrend, `high >= resistance x 0.998`, strong bearish candle, `close < EMA9` |

- **Support** = lowest low of the last 10 bars
- **Resistance** = highest high of the last 10 bars
- **Strong candle** = body size > `0.5 x ATR(14)`

### One Signal Per Trend
An internal state (`trendState`) remembers the last signal. A new BUY fires only if the last signal was not a BUY, and a new SELL only if the last was not a SELL. This prevents repeated same-direction signals during one move.

## Risk Management (SL / TP)

| | BUY | SELL |
| **Stop-Loss** | `low - 0.8 x ATR` | `high + 0.8 x ATR` |
| **Take-Profit** | `close + (close - SL) x RR` | `close - (SL - close) x RR` |

Default **RR = 4.0** (risk 1 to make 4). Red line = SL, green line = TP, each drawn 20 bars forward from the signal.

With 4R targets, a strategy needs roughly a **20%+ win rate to break even** (before fees/slippage). Always verify the real win rate on your market and timeframe.

## Inputs

| Input | Default | Description |
| Fast EMA | 9 | Fast moving average length |
| Slow EMA | 20 | Slow moving average length |
| ATR Length | 14 | ATR period used for candle filter and SL |
| Risk Reward Target | 4.0 | TP distance as a multiple of risk |
| Show Support Resistance Zones | true | Toggle the purple S/R lines |

## Installation
1. Open [TradingView](https://www.tradingview.com/) and open any chart.
2. Open the **Pine Editor** (bottom panel).
3. Copy the contents of [`tp-trade-zone.pine`](tp-trade-zone.pine) into the editor.
4. Click **Add to chart**.
5. Open the indicator settings to adjust inputs if needed.

## Alerts
1. Click the **Alert** (clock) icon in TradingView.
2. Condition: select **TP TRADE ZONE**.
3. Choose **BUY ALERT** or **SELL ALERT**.
4. Set trigger to **Once Per Bar Close** (recommended, see limitations).


## Tested Markets & Timeframes
| Market | Timeframes |
|---|---|
| BTCUSD Perpetual (Delta Exchange) | 15m, 1h |
| ETHUSD Perpetual | 1h |
| XAUUSD (OANDA) | 1m |

Should also work on any liquid market that trends well (forex, indices, other crypto).

## Known Limitations
- **Live-bar repaint:** as an indicator, signals on the currently forming candle can appear or disappear until the candle closes. Trade on closed candles and use "Once Per Bar Close" alerts.
- **Not a backtester:** this is an `indicator`, not a `strategy`, so it does not produce win-rate or P/L statistics.
- **Ranging markets:** EMA-based systems produce more false signals in sideways conditions (see the tight ranges in the BTC 1h screenshot).
- **Lower timeframes (e.g. 1m):** many signals and clustered labels; fees and spread matter much more.
- SL/TP lines are visual only; position sizing is up to the trader.

## Roadmap
- [ ] Convert to a `strategy()` for full backtesting
- [ ] Higher-timeframe trend filter
- [ ] Volume / session filter
- [ ] Trailing stop and partial take-profit
- [ ] Dashboard with signal count and win rate

## Disclaimer
This indicator is for **educational and informational purposes only** and is not financial advice. Trading crypto, commodities and leveraged derivatives carries a high risk of loss. Past signals do not guarantee future results. Use at your own risk and always use proper risk management.

## License
MIT License. Feel free to use, modify and share with credit.

