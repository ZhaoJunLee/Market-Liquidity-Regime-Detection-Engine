# Market Liquidity Regime Detection
Data-driven framework for detecting liquidity regime shifts in financial markets
using **trade-level microstructure signals, SQL pipelines (DuckDB), and statistical analysis.**

Case study: cryptocurrency markets (Binance trade data)

Applied here to digital asset markets, though the methodology is applicable to equity and other financial markets.

**Author:** Lee Zhao Jun  
**Repository:** [crypto-liquidity-regime-shift-detection-engine](https://github.com/ZhaoJunLee/Crypto-Liquidity-Regime-Shift-Detection-Engine/tree/main)  
**Last Updated:** March 2026

---

## Project Overview

This project builds a data pipeline and analytical framework to detect liquidity
regime shifts in cryptocurrency markets using trade-level market microstructure data.

Using DuckDB-powered SQL pipelines and Python analytics, the system processes [raw
Binance trade data](https://data.binance.vision/?prefix=data/spot/daily/aggTrades/) to construct market structure indicators such as directional
volume imbalance, volume bursts, and liquidity efficiency.

The objective is to identify periods of abnormal liquidity pressure that may
precede short-term price movements.

While implemented on digital assets, the methodology is inspired by institutional
market microstructure research. It incorporates metrics such as the Amihud
Illiquidity Proxy and volume imbalance, widely used to assess price impact and
liquidity conditions in equity markets, particularly in less liquid segments
such as mid-cap utilities and emerging market stocks.

Key objectives:
- Build **OHLCV bars** from raw trade data.
- Compute **liquidity features**:
  - Buy/Sell Imbalance (`imbalance`)
  - Relative Volume (`vol_burst`)
  - Liquidity Efficiency (`illiquidity` – Amihud proxy)
- Detect **buy/sell pressure events** based on cumulative imbalance and volume pressure.
- Visualize events alongside price to validate signals.

---

## Example Insights from Exploratory Analysis

Preliminary analysis of trade-level Binance data reveals several
interesting liquidity dynamics:

• **Directional imbalance clusters** often precede short-term price
momentum, suggesting sustained aggressive order flow can signal
liquidity-driven price discovery.

• **Volume bursts without directional imbalance** frequently coincide
with absorption events, where large liquidity providers offset
incoming market orders.

• **Signal reliability varies by liquidity regime**, with stronger
predictive power during volatility expansions compared to
low-liquidity consolidation periods.

These findings highlight how trade-level market microstructure signals
can help analysts identify structural liquidity shifts rather than
random volatility.

---

## Installation


```bash
git clone https://github.com/<your-username>/crypto-liquidity-regime-detection.git
cd crypto-liquidity-regime-detection
python -m venv venv
source venv/bin/activate      # Linux/macOS
# venv\Scripts\activate      # Windows
pip install -r requirements.txt
```
---

## Repository Structure
```text
├── data/
│   ├── raw/         # Place .zip files here (BTC, BNB, AXS)
│   └── processed/   # DuckDB-generated Parquet files
├── notebooks/       # Main analysis (.ipynb)
├── sql/             # Aggregation logic
└── figures/         # Saved pressure event charts
```
- **[notebooks/](https://github.com/ZhaoJunLee/Crypto-Liquidity-Regime-Shift-Detection-Engine/tree/main/notebooks)**: Contains the main notebook implementing the full pipeline: data ingestion → feature engineering → regime detection → visualization.  
- **[sql/](https://github.com/ZhaoJunLee/Crypto-Liquidity-Regime-Shift-Detection-Engine/tree/main/sql)**: Optional SQL scripts for CSV → Parquet conversion and OHLCV aggregation.  
- **[figures/](https://github.com/ZhaoJunLee/Crypto-Liquidity-Regime-Shift-Detection-Engine/tree/main/figures)**: Contains example charts exported via `matplotlib.pyplot.savefig()`.

---

## Usage
1. Place raw trade data .zip files in: data/raw/<SYMBOL>/ as in Repository Structure above.
2. Run the notebook crypto_liquidity_regime.ipynb:
- Unzips and processes raw CSV files to Parquet.
- Builds OHLCV bars and calculates liquidity features.
- Detects buy/sell pressure events.
- Produces plots of price and detected events (saved in figures/).
3. Optional: Execute SQL scripts in sql/ to reproduce steps in DuckDB.

---
## Methodology Overview

Liquidity regimes are detected through a combination of:

1. **Directional Imbalance**
- Buy vs sell aggressor volume normalized by rolling Z-scores
2. **Imbalance Density**
- Persistence of aggressive flows over short horizons
3. **Volume Pressure**
- Cumulative excess volume relative to recent baselines
4. **Regime Confirmation**
- Signals trigger only when both directional imbalance and volume pressure are statistically significant

This multi-layer approach helps to filter:
- Low-volume fake breakouts
- High-volume absorptions without directional intent

---

## Configuration

| Category | Parameter | Default Value | Description |
| :--- | :--- | :--- | :--- |
| **Asset** | `SYMBOL` | "AXS" | Target ticker for analysis. |
| **Timeframe** | `BAR_INTERVAL_MIN` | 5 | Timeframe for OHLCV aggregation. |
| | `BARS_PER_DAY` | 288 | Total bars in 24h (calculated from interval). |
| **Windows** | `FWD_RETURN_HORIZON` | 288 | Look-ahead window for return labeling (~1 day). |
| | `Z_WINDOW` | 288 | Rolling window for Z-score baselines. |
| | `DENSITY_WINDOW` | 12 | Window for imbalance density (~1 hour). |
| | `VOL_PRESSURE_WINDOW`| 288 | Window for cumulative volume pressure. |
| **Thresholds**| `IMBALANCE_DENSITY_THRESHOLD` | 0.5 | Min proportion of bars with statistically significant imbalance required to confirm pressure. |
| | `Z_SCORE_THRESHOLD` | 1.0 | Statistical significance required for "Pressure." |

---

## Output
- OHLCV Bars: open, high, low, close, volume, buy_volume, sell_volume.
- Liquidity Features: imbalance, vol_burst, illiquidity.
- Regime Signals: pressure_signal (buy, sell, none) with pressure_strength.
- Visualization Example: ![BTC Liquidity Pressure](figures/liquidity_pressure_example.png)
Green (buy) and red (sell) markers indicate periods of statistically significant liquidity pressure, 
with color intensity reflecting the strength of the detected regime.


---

## Contribution

This repository is a personal project and can be extended by:
- Adding more symbols or multiple market support.
- Integrating real-time data feeds.
- Experimenting with alternative liquidity proxies or EWMA-based z-scores.
- Improving visualization and reporting features.

---

## License
This project is open-source under the MIT License.

---

## Reference
1. Amihud, Y. (2002). Illiquidity and stock returns: cross-section and time-series effects.
2. Market Microstructure research: directional liquidity, volume imbalance, and price impact.
3. DuckDB Documentation: https://duckdb.org/

---

## Notes
- This project focuses on exploratory liquidity regime detection, not trading advice.
- Illiquidity (Amihud proxy) is computed but not directly used in the event detection; can be added for further research.
