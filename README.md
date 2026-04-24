# NAS100 Weather Strategy: A Monte Carlo Investigation

## Project Overview

This project investigates an unconventional hypothesis: **does rainfall in New York City correlate with the daily direction of the NAS100 index?**

Using 9 years of 15-minute NAS100 futures data (2016–2025) and NOAA Central Park weather records, I built a complete analytical pipeline combining Python, SQL, and Power BI to test this hypothesis through Monte Carlo simulation.

## The Hypothesis

A simple trading rule is defined:
- **Rainy day** (precipitation > 0): Short position at 09:30 ORB, exit at 16:30
- **Dry day** (precipitation = 0): Long position at 09:30 ORB, exit at 16:30

**Question:** Does this weather-based signal outperform random selection (a coin flip)?

## Methodology

1. **Data Collection** — NAS100 15-minute OHLC data (~207k rows) + NOAA daily precipitation data for NYC Central Park
2. **Data Engineering** — Filter to 09:30 and 16:30 candles, pivot into daily view, merge with weather
3. **Strategy Logic** — Apply Long/Short direction based on precipitation, calculate PnL
4. **Markov Chain Modeling** — Compute rainfall transition probabilities (rainy→rainy, dry→rainy)
5. **Monte Carlo Simulation** — Generate 10,000 synthetic rainfall histories using the Markov matrix, apply the strategy to each, compare real results against the distribution
6. **Interactive Dashboard** — Power BI visualization connected to SQLite database

## Key Results

| Metric | Value |
|--------|-------|
| Total trading days analyzed | 2,266 |
| Total Weather Strategy PnL | +11,915 points |
| Win rate | 52.1% |
| Always Long benchmark | +1,152 points |
| Monte Carlo percentile | **99.86%** |

## Dashboard Preview

![Dashboard](dashboard_screenshot.png)

**Interpretation:** Out of 10,000 simulated "alternative worlds" (where rainfall follows the same Markov structure but falls on different days), only ~14 produced a higher PnL than the real world. This suggests a statistically significant relationship between NYC rainfall patterns and NAS100 direction (p ≈ 0.001).

## Important Limitations

This analysis is an **academic exploration**, not a tradeable strategy:

- **Look-ahead bias**: Same-day precipitation totals are used, which include rainfall occurring after market close. A production version would use previous-day weather or morning-only precipitation
- **Confounding variables**: The observed correlation may reflect seasonal effects, volatility regimes, or other hidden factors rather than a direct weather→market relationship
- **No out-of-sample validation**: The entire 2016–2025 period was used; a proper backtest would reserve recent years for validation
- **Academic literature notes** similar effects (Saunders 1993, Hirshleifer & Shumway 2003), but the mechanism remains debated

## Tech Stack

- **Python** (pandas, numpy, matplotlib) — data processing, Monte Carlo simulation
- **SQLite** — relational storage of all processed data
- **Power BI** — interactive dashboard
- **Jupyter Notebook** — analytical workflow and documentation

## Project Structure
nq-monte-carlo-analysis/
├── 01_data_exploration.ipynb    # Data loading, cleaning, strategy logic, Monte Carlo
├── 02_sql_setup.ipynb           # SQLite database creation and SQL queries
├── nq_15min.csv                 # Raw NAS100 15-minute data
├── nyc_weather.csv              # Raw NOAA precipitation data
├── nq_project.db                # SQLite database with all processed tables
├── nq_monte_carlo_dashboard.pbix # Power BI interactive dashboard
└── exports/                     # CSV exports for Power BI
## How to Reproduce

1. Clone this repository
2. Install dependencies: `pip install pandas numpy matplotlib`
3. Open `01_data_exploration.ipynb` in Jupyter, run all cells
4. Open `02_sql_setup.ipynb`, run all cells to rebuild the SQLite database
5. Open `nq_monte_carlo_dashboard.pbix` in Power BI Desktop
