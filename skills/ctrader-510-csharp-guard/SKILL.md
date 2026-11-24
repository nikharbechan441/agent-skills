---
name: ctrader-510-csharp-guard
description: Pure C# engineering invariants for cTrader Desktop 5.10.x (Embedded compiler). Enforces double-gate risk sizing (VolumeForFixedRisk + AmountRisked), live AssetConverter conversions, Cloud-safe state via Position.Comment/History, and backtest-to-demo validation rules.
---

# cTrader 5.10 C# Engineering Guardrails (`ctrader-510-csharp-guard`)

Apply these hard compiler, risk-sizing, and execution safety rules when building or auditing **pure C# cBots** on **cTrader Desktop 5.10.x (Embedded compiler)**.

---

## Mandatory Compiler & Risk Invariants

1. **Required Directives & Compiler Mode:**
   - Always include:
     ```csharp
     using cAlgo.API;
     using cAlgo.API.Indicators;
     using cAlgo.API.Internals;
     using System;
     using System.Collections.Generic;
     using System.Globalization;
     using System.Linq;
     ```
   - Use pure C# `[Parameter(...)]` attributes cleanly—never mix external `config.json` boilerplate into Embedded cBot sources.

2. **Double-Gate Position Sizing (Prevent Min-Lot Oversizing):**
   - Size positions with `sym.VolumeForFixedRisk(targetRisk, slPips, RoundingMode.Down)`.
   - **Explicitly verify** `sym.AmountRisked(vol, slPips) <= hardMaxRiskAmt` *before* submitting an order.
   - **Why:** Calling `NormalizeVolumeInUnits` alone can silently clamp sub-minimum sizes **UP** to `VolumeInUnitsMin`, risking far more than `targetRisk` on smaller accounts or high-tick instruments.

3. **Live Currency Conversion & Margin (Never Stale `PipValue`):**
   - Do **not** rely on frozen `Symbol.PipValue` for cross-currency risk math.
   - Always use live `AssetConverter.Convert(amount, baseCurrency, Account.Asset.Name)` and check `sym.GetEstimatedMargin(side, vol)` before entry.

4. **cTrader Cloud-Safe State Persistence:**
   - Do **not** depend on `LocalStorage` for critical circuit breakers or SL state (it is RAM-only on cTrader Cloud restarts).
   - Encode immutable entry risk/SL metadata in `Position.Comment` (`<= 50` chars) and rebuild daily/weekly drawdown circuit breakers directly from `History` + open `Positions`.

5. **Enum, Timeframe & Lifecycle Hygiene:**
   - `SymbolMinDistanceType` only contains **`Pips`** and **`Percentage`** (never `Points`).
   - Count elapsed duration using closed bars in `Bars.OpenTimes` (never wall-clock hours across weekend market closes).
   - Cache period PnL instead of scanning `History` on every `OnTick()`.
   - Unsubscribe `BarOpened` event handlers cleanly inside `OnStop()`.

---

## Validation Workflow Before Real Capital
1. **Fast Screening:** Iterate ideas on `M1 bars from server (open prices)` to rule out bad logic quickly.
2. **Tick Verification:** Always re-run passing candidates on `Tick data from server (accurate)` over >= 12 months (`Profit Factor > 1.30`, sufficient trade sample, controlled max drawdown).
3. **Demo Gate:** Run >= 4 weeks forward testing on a live Demo account / cTrader Cloud to verify slippage, spread widening, and circuit breakers before deploying live funds.
