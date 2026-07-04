# Write-up: Trader Performance vs Market Sentiment

## Methodology
Two datasets were joined at the daily level: 211,224 Hyperliquid trade records (32 accounts, 246
symbols, May 2023–May 2025) and the BTC Fear & Greed Index (daily, 2018–2025). Both files were
clean (no missing values, no duplicate rows) at the raw level, so preparation focused on:
timestamp parsing (`Timestamp IST` → date), a left-join of trades to sentiment on date (only 6
of 211k rows had no matching sentiment date and were dropped), and collapsing the 5-class
sentiment label into a 3-class **Fear / Neutral / Greed** view for the core comparisons while
keeping the 5-class label available for finer cuts.

The raw data has no explicit leverage field. `Start Position` is denominated in tokens (not USD)
and is frequently 0 for new positions, so a naive size/position ratio would be misleading.
Instead, the boolean `Crossed` flag (cross-margin vs isolated-margin) is used as an honest,
available **leverage-risk proxy** — cross-margin puts the whole account balance at risk and is
the more aggressive setting.

Core metrics were built at two grains: **account-day** (daily PnL, win rate, trade count, avg
trade size, long/short ratio, cross-margin share — 2,340 account-days) and **market-day**
(pooled activity across all accounts — for behavior-shift analysis). Three trader segments were
defined by median split: leverage (cross-margin share), frequency (trades per active day), and
consistency (average win rate).

## Key Insights

1. **Fear days are not worse for this cohort — if anything the opposite.** Mean daily account
   PnL is *higher* on Fear days (~$5,185) than Greed days (~$4,144), and a Welch t-test finds no
   statistically significant difference (p = 0.46 for PnL, p = 0.38 for win rate). However,
   average per-account **max drawdown is ~60% deeper on Greed days** (‑$33,124) than Fear days
   (‑$20,800), suggesting Greed periods carry more tail risk even without lower average returns.

2. **Activity roughly triples on Fear days.** Average trades/day jumps from ~294 (Greed) to
   ~793 (Fear), with more distinct accounts active. Cross-margin usage is fairly flat across
   regimes (~73–78%), so traders aren't obviously de-risking their margin mode in Fear — the
   change shows up in frequency, not leverage choice. Long/short bias stays close to 50/50 with
   a slight long tilt on Fear days (51%) and slight short tilt on Greed days (48%).

3. **Leverage doesn't pay off, especially in Fear.** The Low cross-margin-use segment earns
   roughly 2.4x the average daily PnL of the High cross-margin-use segment ($10,057 vs $4,197
   overall), and the gap is starkest on Fear days ($11,683 vs $1,271). Higher leverage mainly
   seems to amplify losses when the market is stressed, not generate extra return.

4. **(Bonus) Three natural trader archetypes** emerge from K-Means on PnL, volatility, win rate,
   frequency, and cross-margin share: a tiny group of **High-Frequency Scalpers** (2 accounts,
   very high volume and PnL but very high volatility), a large group of **High-Leverage
   Risk-Takers** (19 accounts), and **Steady Low-Frequency Traders** (11 accounts, low leverage).
   This matches the median-split segments and reinforces where risk controls should focus.

## Strategy Recommendations

**Rule 1 — Cap leverage for the High cross-margin segment on Fear days.** This segment's average
daily PnL collapses from ~$4,646 (Greed) to ~$1,271 (Fear) — the segment most exposed on the
downside during stress. When the index reads Fear/Extreme Fear, force isolated margin or a lower
max-leverage tier for these accounts.

**Rule 2 — Let low-leverage, frequent traders lean in during Fear.** The Low cross-margin
segment's average daily PnL rises to ~$11,683 on Fear days vs ~$3,010 on Greed days — the
opposite pattern. For traders who are already disciplined on leverage, modestly larger position
sizing during Fear/Extreme-Fear windows appears to have historically captured more upside, while
Rule 1 keeps the riskier segment contained.

*Both rules describe patterns in this historical sample only (32 accounts); they should be
validated with an out-of-sample or walk-forward backtest before being used to size real capital.*

## Limitations
Small account sample (n=32); the cross-margin flag is a proxy, not a true leverage ratio; the
Fear/Greed index is a blunt, market-wide daily signal that misses coin-specific and intraday
sentiment; and the bonus predictive model (AUC ≈ 0.55) is inconclusive — reported honestly rather
than optimized to look better than the data supports.
