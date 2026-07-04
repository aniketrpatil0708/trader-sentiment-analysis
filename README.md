# Trader Performance vs Market Sentiment — Primetrade.ai Round-0 Assignment

Analysis of how Bitcoin Fear & Greed sentiment relates to Hyperliquid trader behavior and
performance, using 211k trade-level records across 32 accounts (May 2023 – May 2025).

## Repo structure
```
.
├── README.md              <- this file
├── WRITEUP.md              <- 1-page methodology / insights / strategy summary
├── requirements.txt
├── data/
│   ├── fear_greed_index.csv      <- raw BTC Fear & Greed Index (provided) 
│   └── historical_data.csv       <- raw Hyperliquid trade data (Not provided)

├── notebook/
│   └── analysis.ipynb            <- full analysis, executed with outputs/charts inline
└── charts/
    ├── 01_pnl_winrate_by_sentiment.png
    ├── 02_behavior_by_sentiment.png
    ├── 03_segment_leverage_pnl.png
    ├── 04_segment_frequency_pnl.png
    ├── 05_trader_archetypes_scatter.png
    ├── 06_prediction_coefficients.png
    ├── 07_trader_clusters.png
    ├── account_segments.csv          <- per-account summary + segment labels
    └── trader_clusters.csv           <- per-account cluster assignment (bonus)
```
## Note on data
Due to GitHub's file-size upload limits via the web interface, the raw `historical_data.csv`
(~47MB) is not included directly in this repo. It is available on request, or can be re-fetched
from the original assignment source. The smaller `fear_greed_index.csv`, along with all derived
outputs (`charts/account_segments.csv`, `charts/trader_clusters.csv`, and the fully executed
notebook with all charts/tables inline), are included so the analysis can be reviewed without
needing to re-run it.
## How to run
1. Create an environment and install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
2. Launch Jupyter and open `notebook/analysis.ipynb`:
   ```bash
   jupyter notebook notebook/analysis.ipynb
   ```
3. Run all cells top to bottom (Kernel → Restart & Run All). The notebook reads the CSVs from
   `../data/`, so keep the folder structure intact, or run it in place — outputs are already
   saved in the shipped notebook so you can also just read it without re-running.

## What's inside the notebook
- **Part A — Data preparation:** row/column counts, missing-value and duplicate checks,
  timestamp parsing, date-level alignment of trades with the sentiment index, and construction
  of the core metrics (daily PnL per account, win rate, average trade size, trades/day,
  long/short ratio, and a cross-margin usage share as an available leverage-risk proxy).
- **Part B — Analysis:** Fear-vs-Greed performance comparison (with a Welch t-test and a
  max-drawdown proxy), behavior-shift analysis (trade frequency, margin mode, position sizing,
  long/short bias), and three trader segmentations (leverage, frequency, consistency) with
  sentiment-conditioned performance breakdowns.
- **Part C — Actionable output:** two concrete, evidence-backed strategy rules.
- **Bonus:** a next-day-profitability logistic regression (reported honestly — it doesn't
  beat baseline, which is itself an informative result) and a K-Means clustering of traders
  into three behavioral archetypes.

See `WRITEUP.md` for the condensed summary of methodology, insights, and recommendations.
