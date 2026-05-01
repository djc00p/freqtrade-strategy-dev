# 🧠 Freqtrade Strategy Development Handbook

[![ClawHub Skill](https://img.shields.io/badge/ClawHub-Skill-blue)](https://clawhub.ai/djc00p/freqtrade-strategy-dev) [![Agent Skill](https://img.shields.io/badge/Agent-Skill-blue)](#) [![Freqtrade Crypto](https://img.shields.io/badge/Freqtrade-Crypto-green)](https://github.com/freqtrade/freqtrade)

**A systematic framework for designing, backtesting, and deploying profitable algorithmic trading strategies.**

The **Freqtrade Strategy Development** skill provides a disciplined engineering approach to quantitative trading. Rather than "guessing" at indicators, this handbook guides developers through a rigorous, data-driven lifecycle: from defining technical anatomy to executing multi-stage backtesting and protecting capital through strict risk management.

---

## 🧬 Strategy Anatomy

Every robust Freqtrade strategy is built upon three foundational methods. Success depends on how these methods interact to filter market noise.

| Method | Responsibility | Logic Goal |
| :--- | :--- | :--- |
| 📈 `populate_indicators` | **Data Engineering** | Compute RSI, MACD, Bollinger Bands, etc., and attach them to the DataFrame. |
| 🚀 `populate_entry_trend` | **Signal Generation** | Define the "Golden Setup": A confluence of multiple indicators all signaling "Buy." |
| 📉 `populate_exit_trend` | **Risk Mitigation** | Define the "Exit Trigger": Profit targets, trend reversals, or trailing stops. |

---

## 🛡️ The Golden Rules of Risk Management

In algorithmic trading, **survival is the only metric that matters.** A high win rate is useless if a single "black swan" event wipes out your account.

### 1. The "Tight Stop" Mandate

Never allow a trade to run without a hard stop-loss.

* **Recommended:** Start with a strict `stoploss = -0.03` (3%).
* **The Logic:** It is easier to recover from a 3% loss than a 10% drawdown.

### 2. Optimized ROI (Return on Investment)

Use a tiered `minimal_roi` to capture profits during momentum and lock them in during stagnation.

```python
minimal_roi = {
    "0": 0.04,      # 4% profit target immediately
    "30": 0.02,     # 2% profit target after 30 candles
    "60": 0.01,     # 1% profit target after 60 candles
}
```

### 3. The "Confluence" Principle

Never enter a trade on a single indicator. A single RSI signal is "noise." An RSI(30) + CCI(-100) + Bollinger Band touch is a **signal**.

---

## 🔄 The Iteration Workflow (The Quant Cycle)

Avoid the "Strategy Trap"—constantly changing code without measuring results. Follow this 8-step loop:

1. **Baseline Creation:** Write a simple strategy with core entry/exit logic.
2. **Historical Backtesting:** Run `ftback` on 90–120 days of historical data.
3.**Error Analysis:** Analyze exits. Are you exiting winners too early (cutting profit) or exiting losers too late (expanding loss)?
3. **Single-Variable Tuning:** Change **one** parameter (e.    g., RSI threshold) at a time.
4. **Comparative Backtesting:** Re-run the same period against your baseline.
5. **Performance Validation:** If the new version outperforms the baseline without increasing drawdown, **keep it.**
6. **Stress Testing:** Test the strategy in "Extreme" markets (Bear, Bull, and Sideways/Chop).
7. **Dry-Run Deployment:** Deploy to a live data feed using `dry_run = True` for at least one week before committing real capital.

---

## 🛠️ Essential Indicator Library

| Indicator | Use Case | Signal Logic |
| :--- | :--- | :--- |
| **RSI (14)** | Momentum/Overbought | `< 30` (Oversold) $\rightarrow$ Potential Buy |
| **CCI** | Trend/Momentum | `< -100` (Deep Oversold) $\rightarrow$ Potential Buy |
| **Bollinger Bands** | Volatility/Mean Reversion | Price touching `lower_band` $\rightarrow$ Reversal potential |
| **EMA (200)** | Trend Filter | Price `> EMA` $\rightarrow$ Only take Long trades |
| **MFI** | Volume-Weighted Momentum | High `MFI` $\rightarrow$ Strong buying pressure |

---

## ⚠️ Lessons from the Trenches

* **Avoid "Overfitting":** If your strategy only works on a specific 30-day window, it is "curve-fitted" and will fail in live trading.
* **Volume is Conviction:** An entry signal on low volume is a trap. Always use a Volume SMA filter.
* **The "Survivability" Test:** If your strategy cannot survive a 20% market crash in backtesting, it does not belong in production.
* **Version Control is Mandatory:** Use `MyStrategy_v1.py`, `v2.py`, etc. Never overwrite a working strategy with a "new" one without a documented comparison.

---
**Original implementation by [@djc00p](https://github.com/djc00p)**
