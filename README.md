# TradingView MTF Signal Indicator

**Pine Script v5 — Multi-Timeframe Options Signal Confirmation**  
1-minute chart | SPX · QQQ · SPY · Individual Stocks

---

## Overview

A professional signal confirmation tool for discretionary options traders. Designed for the opening session (9:30–11:00 AM EST). Signals fire only when every condition aligns across multiple timeframes — quality over quantity.

Delivered as two scripts:
- **`MTF_Signal_Indicator_v1.pine`** — Live chart use
- **`MTF_Signal_Strategy_v1.pine`** — Strategy Tester / backtesting

---

## What's Included

| Feature | Details |
|---|---|
| Key Levels | Previous day H/L, pre-market H/L, 5-min ORB, 15-min ORB |
| MTF Trend Table | 5m / 15m / Daily trend direction (EMA slope method) |
| Indicators | 9 EMA, 20 EMA, session VWAP |
| Pivot Zones | Shaded zones where daily price has clustered 3+ times (90-day lookback) |
| Signal Grades | A-grade (zone confluence) and B-grade (trend only) |
| Exit Guidance | T1 target line (+0.5% default), EMA cross Exit Warning label |
| Alerts | Four alert conditions — A/B grade × Call/Put |

---

## How to Load in TradingView

1. Open TradingView and go to a **1-minute chart**
2. Open the **Pine Script Editor** (bottom panel)
3. Delete the default code, paste the contents of the `.pine` file
4. Click **Save**, give it a name, then click **Add to chart**
5. In chart settings (gear icon on the price scale), enable **Extended Hours** to populate pre-market levels

Load the **Indicator** version for live trading. Load the **Strategy** version separately when you want to run the Strategy Tester.

---

## Signal Logic

### CALL Signal — all must be true
- 5-min, 15-min, and Daily trend all Bullish (EMA sloping up)
- Price above VWAP
- 9 EMA above 20 EMA
- Price has broken above the 5-min or 15-min ORB high (first confirmed close above)

### PUT Signal — all must be true
- 5-min, 15-min, and Daily trend all Bearish (EMA sloping down)
- Price below VWAP
- 9 EMA below 20 EMA
- Price has broken below the 5-min or 15-min ORB low (first confirmed close below)

### Signal Grades
| Grade | Condition | Display |
|---|---|---|
| **A-Grade** | All conditions + price within 0.3% of a major pivot zone | Large arrow + diamond, bright color |
| **B-Grade** | All conditions, no major zone nearby | Standard arrow |

### ORB Break Rule
The signal fires on the **first candle that closes above/below the ORB level**. It will not repeat while price holds above/below. If price reverts through the ORB and breaks again, a new signal fires.

---

## Exit Logic

| Exit | Trigger |
|---|---|
| **T1 Target** | +0.5% move on the underlying from signal close (visual line only in indicator; hard exit in strategy) |
| **EMA Cross Exit** | 9 EMA crosses back against trade direction → "Exit Warning" label appears |
| **Stop Loss** | 1.5% adverse move (strategy version only) |
| **EOD Close** | All positions closed at end of RTH session (strategy version only) |

---

## Inputs Reference

### EMAs
| Input | Default | Notes |
|---|---|---|
| EMA Fast Length | 9 | Used for 1m, 5m, 15m, and Daily trend |
| EMA Slow Length | 20 | Cross confirmation on 1m chart |

### Opening Range
| Input | Default | Notes |
|---|---|---|
| Require ORB Break | On | Toggle off to allow signals before ORB forms |

### Pivot Zones
| Input | Default | Notes |
|---|---|---|
| Lookback (days) | 90 | Max 252 (1 trading year) |
| Minimum Touches | 3 | Lower to 2 for more zones, raise to 4 for fewer |
| Zone Cluster Tolerance | 0.5% | Raise to 0.7–1.0% for SPX/QQQ |

### Signals
| Input | Default | Notes |
|---|---|---|
| A-Grade Zone Proximity | 0.3% | Widen to 0.5% if seeing too few A-grades |
| T1 Target | 0.5% | ~25–50% options gain on ATM delta ~0.5 |
| Stop Loss % *(strategy only)* | 1.5% | Adjust per volatility environment |

### Key Levels
| Input | Default | Notes |
|---|---|---|
| Pre-market Source Override | *(blank)* | SPX users: enter `ES1!` for overnight futures levels |

### Display Toggles
All key level types, zones, and the trend table can be individually shown/hidden to keep the chart clean.

### Session
| Input | Default | Notes |
|---|---|---|
| Signal Window | 09:30–11:00 | Adjust to extend coverage beyond the open |

---

## Running the Strategy Tester

1. Add `MTF_Signal_Strategy_v1.pine` to your 1-minute chart
2. Click the **Strategy Tester** tab at the bottom
3. In the **Properties** panel (gear icon), configure:
   - Commission to match your broker (default 0.05% per side)
   - Slippage in ticks per instrument
4. Key metrics to review: Win Rate, Profit Factor, Average Win / Average Loss, Max Drawdown

Recommended minimum backtest window: **6 months** before drawing conclusions on any single ticker.

---

## Instrument Notes

### SPX
- No native pre-market data (cash index). Leave the Pre-market Override blank (lines will not appear) or enter `ES1!` to use overnight S&P 500 futures levels.
- Raise Zone Cluster Tolerance to 0.7–1.0% to account for SPX's wider daily ranges.

### SPY / QQQ
- Pre-market data populates automatically (ETFs trade extended hours).
- Default settings work well out of the box.

### Individual Stocks
- Extended hours availability varies by broker and TradingView data plan.
- Consider widening Zone Cluster Tolerance on high-beta names.

---

## Repainting Disclosure

| Component | Repaints? | Notes |
|---|---|---|
| 1-min signals | **No** | Fire on confirmed bar close |
| HTF trend (5m/15m/D) | **Yes — within current HTF bar** | Inherent to multi-timeframe analysis; historical bars stable after close |
| ORB levels during formation | **Yes — expected** | Levels are incomplete until the window closes at 9:35 / 9:45 |
| Pivot zones (historical bars) | **No** | Stable on closed bars |
| Pivot zones (today's session) | **Until 4 PM** | Current day's daily bar contribution shifts until market close |

**For backtesting:** HTF trend repainting means historical win rates will be slightly optimistic vs. live performance. Use backtest results for directional guidance and relative comparison, not as precise predictors.

---

## First Tuning Steps

Start here when adapting to a new instrument:

1. **Zone Cluster Tolerance** — raise for indices (0.7–1.0%), keep tight for stocks (0.3–0.5%)
2. **T1 Target %** — 0.5% default; raise to 0.75% if you see too many premature exits
3. **A-Grade Zone Proximity** — widen to 0.5% if very few A-grades appear in a session
4. **Signal Window** — extend beyond 11:00 AM if you want afternoon setups

---

## Files

```
tradingview-mtf-signal/
├── MTF_Signal_Indicator_v1.pine   ← Add to chart for live use
├── MTF_Signal_Strategy_v1.pine    ← Add to chart for backtesting
└── README.md
```

---

*Built for discretionary confirmation only. Not financial advice. No auto-trading.*
