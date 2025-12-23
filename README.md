# finra-daily-shortExempt-vol-notebook
# FINRA Short Sale Data Analyzer

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

A comprehensive Google Colab notebook for scraping, analyzing, and visualizing FINRA daily short sale data with a focus on detecting anomalies in short exempt volume patterns.

![Dashboard Preview](https://img.shields.io/badge/Status-Active-brightgreen)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Analysis Modules](#analysis-modules)
- [Understanding Short Exempt](#understanding-short-exempt)
- [Output Files](#output-files)
- [Interpreting Results](#interpreting-results)
- [FAQ](#faq)
- [Contributing](#contributing)
- [Disclaimer](#disclaimer)
- [License](#license)

---

## 🎯 Overview

This notebook automates the collection and analysis of FINRA's daily short sale volume data, specifically designed for investigating short selling patterns in securities like **MSOS** (AdvisorShares Pure US Cannabis ETF) and **MSOX** (AdvisorShares MSOS 2X Daily ETF).

### What This Tool Does

1. **Scrapes** daily short sale data directly from FINRA's CDN
2. **Calculates** derived metrics (short %, exempt ratios, etc.)
3. **Identifies** anomalies using statistical and ML methods
4. **Visualizes** patterns through comprehensive dashboards
5. **Generates** exportable reports for further analysis

### Data Source

All data is sourced from FINRA's official Reg SHO daily short sale files:
```
https://cdn.finra.org/equity/regsho/daily/CNMSshvol{YYYYMMDD}.txt
```

---

## ✨ Features

### Core Functionality
- ✅ Automatic trading day detection (skips weekends/holidays)
- ✅ Configurable date range (by months)
- ✅ Multi-symbol support
- ✅ Rate-limited requests to respect FINRA servers
- ✅ Progress tracking during data collection

### Analysis Capabilities

| Category | Features |
|----------|----------|
| **Summary Statistics** | Mean, median, std dev, percentiles for all metrics |
| **Time Analysis** | Daily, weekly, monthly aggregations |
| **Anomaly Detection** | Z-score, Modified Z-score, Isolation Forest ML |
| **Pattern Recognition** | Clustering, regime detection, trend analysis |
| **Event Studies** | Before/after analysis of high exempt days |
| **Visualization** | 15+ chart types including heatmaps and dashboards |

### Advanced Features
- 🤖 Machine Learning anomaly detection (Isolation Forest)
- 📊 K-Means clustering of exempt patterns
- 📈 Rolling statistics with Bollinger Bands
- 🔍 Correlation analysis with lag features
- 📅 Calendar heatmaps for visual pattern spotting

---

## 🚀 Quick Start

### Option 1: Google Colab (Recommended)

1. Open the notebook in Google Colab
2. Run all cells in order (`Runtime` → `Run all`)
3. Modify configuration in **Cell 3** as needed
4. Download exported CSV files when complete

### Option 2: Local Jupyter

```bash
# Clone the repository
git clone https://github.com/yourusername/finra-short-analysis.git
cd finra-short-analysis

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook finra_short_sale_analysis.ipynb
```

### Dependencies

```
pandas>=1.3.0
numpy>=1.21.0
matplotlib>=3.4.0
seaborn>=0.11.0
requests>=2.26.0
pandas_market_calendars>=3.0
scikit-learn>=0.24.0
scipy>=1.7.0
```

---

## ⚙️ Configuration

All configuration is done in **Cell 3** of the notebook:

```python
# =============================================================================
# CONFIGURATION - Modify these values as needed
# =============================================================================

# Number of months to scrape (going back from today)
MONTHS_TO_SCRAPE = 3

# Symbols to analyze
TARGET_SYMBOLS = ['MSOS', 'MSOX']

# FINRA URL template (do not modify unless FINRA changes their URL structure)
FINRA_URL_TEMPLATE = "https://cdn.finra.org/equity/regsho/daily/CNMSshvol{date}.txt"

# Request delay (seconds) - be respectful to FINRA's servers
REQUEST_DELAY = 0.25
```

### Configuration Options

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `MONTHS_TO_SCRAPE` | int | 3 | Number of months of historical data to collect |
| `TARGET_SYMBOLS` | list | ['MSOS', 'MSOX'] | Stock symbols to analyze |
| `REQUEST_DELAY` | float | 0.25 | Delay between API requests in seconds |

### Adding Custom Symbols

```python
# Single symbol
TARGET_SYMBOLS = ['GME']

# Multiple symbols
TARGET_SYMBOLS = ['AMC', 'GME', 'BBBY', 'MSOS']

# ETFs and stocks mixed
TARGET_SYMBOLS = ['SPY', 'AAPL', 'MSOS', 'MSOX']
```

---

## 📊 Analysis Modules

### 1. Data Collection & Preprocessing

**Cells 4-5**

- Generates list of valid trading days using NYSE calendar
- Scrapes FINRA data files with progress tracking
- Handles missing data and failed requests gracefully

**Calculated Metrics:**
| Metric | Formula | Description |
|--------|---------|-------------|
| `ShortPercent` | (ShortVolume / TotalVolume) × 100 | Percentage of volume that was short |
| `ShortExemptPercent` | (ShortExemptVolume / TotalVolume) × 100 | Exempt volume as % of total |
| `ShortExemptRatio` | (ShortExemptVolume / ShortVolume) × 100 | Exempt as % of short volume |
| `NonShortVolume` | TotalVolume - ShortVolume | Long/neutral volume |

---

### 2. Summary Statistics

**Cell 6-7**

Generates comprehensive statistics for each symbol:
- Date range and trading day count
- Volume totals and averages
- Short percentage distribution
- Short exempt analysis

---

### 3. Top Short Volume Days

**Cell 8-9**

Identifies:
- Top 20 days by **short volume**
- Top 20 days by **short exempt volume**

Useful for identifying dates requiring further investigation.

---

### 4. Time-Based Analysis

**Cells 10-12**

#### By Day of Week
Reveals if certain days show elevated short/exempt activity.

#### By Week
Weekly aggregations to spot trends.

#### By Month
Monthly totals and averages for longer-term pattern recognition.

---

### 5. Short Exempt Deep Analysis

**Cell 13**

Detailed examination of short exempt patterns:
- Statistical distribution of exempt volume
- Anomaly detection (>2σ and >3σ)
- Exempt ratio analysis
- Days with unusually high exempt percentages

---

### 6. Discrepancy Detection

**Cell 14**

Data integrity and anomaly checks:
- ⚠️ Days where ShortVolume > TotalVolume (data error)
- ⚠️ Days where ShortExempt > ShortVolume (data error)
- 📊 Short percentage outliers (±2σ)
- 📈 Large day-over-day volume swings (>200%)
- 🔗 Correlation analysis between exempt and total volume

---

### 7. Visualizations

**Cells 15-16**

Per-symbol dashboard with 8 charts:
1. Daily Short Volume (bar chart)
2. Short % Over Time (line chart with mean)
3. Daily Short Exempt Volume
4. Short Exempt Ratio Timeline
5. Stacked Volume Breakdown
6. Short % Distribution Histogram
7. Average by Day of Week
8. Monthly Comparison

**Comparison Charts** (if multiple symbols):
- Side-by-side short % comparison
- Exempt ratio comparison
- Monthly volume by symbol
- Average short % with error bars

---

### 8. Advanced Analysis Cells (Additional)

#### Calendar Heatmap
Visual calendar showing exempt volume intensity by day.

#### Event Study Analysis
Examines market behavior 5 days before and after high exempt events.

#### ML Anomaly Detection
- **Isolation Forest**: Unsupervised anomaly detection
- **Z-Score**: Statistical outlier identification
- **Modified Z-Score**: Robust to outliers
- **Consensus Method**: Flags days identified by multiple methods

#### Clustering Analysis
- K-Means clustering of exempt day patterns
- PCA visualization
- Cluster profiling (HIGH/MODERATE/LOW exempt)

#### Rolling Statistics & Regime Detection
- Short and long-term moving averages
- Bollinger Bands for exempt volume
- MA crossover signals
- Regime change detection

#### Correlation Analysis
- Full correlation matrix
- Lagged correlations (autocorrelation)
- Feature relationship scatter plots

---

## 📚 Understanding Short Exempt

### What is Short Exempt Volume?

**Short exempt** volume represents short sales that are exempt from the standard locate requirement under Regulation SHO. Common exemptions include:

| Exemption Type | Description |
|----------------|-------------|
| **Market Maker** | Bona fide market making activities |
| **Odd-Lot** | Transactions of fewer than 100 shares |
| **Domestic Arbitrage** | Certain arbitrage transactions |
| **International Arbitrage** | Cross-border arbitrage |
| **Over-Allotment** | Underwriter covering positions |
| **Riskless Principal** | Specific broker-dealer transactions |

### Why Monitor Short Exempt?

Elevated short exempt volume may indicate:
- 🔴 Potential naked short selling abuse
- 🟡 Unusual market maker activity
- 🟢 Legitimate increased market making (during volatility)

### Red Flags to Watch

| Indicator | Threshold | Concern Level |
|-----------|-----------|---------------|
| Exempt Ratio | >1% of short volume | Elevated |
| Exempt Ratio | >2% of short volume | High |
| Exempt Ratio | >5% of short volume | Investigate |
| Exempt Persistence | Multiple consecutive high days | Pattern |
| Exempt Spikes | >3σ from mean | Anomaly |

---

## 📁 Output Files

### Automatic Exports

| File | Description |
|------|-------------|
| `finra_short_data_{SYMBOL}_{N}mo.csv` | Complete dataset with all calculated metrics |
| `finra_summary_{SYMBOL}_{N}mo.csv` | Summary statistics report |

### Additional Report (from advanced cells)

| File | Description |
|------|-------------|
| `short_exempt_report_{timestamp}.txt` | Comprehensive text analysis report |

### CSV Column Definitions

```
Date                 - Trading date
Symbol               - Stock ticker
ShortVolume          - Total short sale volume
ShortExemptVolume    - Short exempt volume
TotalVolume          - Total trading volume
Market               - Market identifiers (B=BATS, Q=NASDAQ, N=NYSE)
ShortPercent         - Short volume / Total volume × 100
ShortExemptPercent   - Exempt volume / Total volume × 100
ShortExemptRatio     - Exempt volume / Short volume × 100
NonShortVolume       - Total - Short volume
DayOfWeek            - Monday through Friday
YearWeek             - Year and week number (YYYY-WNN)
YearMonth            - Year and month (YYYY-MM)
```

---

## 🔍 Interpreting Results

### Short Percentage Benchmarks

| Range | Interpretation |
|-------|----------------|
| 20-40% | Normal range for most securities |
| 40-50% | Elevated short interest |
| 50-60% | High short activity |
| >60% | Unusually high - investigate |

### Anomaly Severity Guide

| Detection Method | Confidence |
|------------------|------------|
| Single method flags | Low - may be noise |
| Two methods agree | Medium - worth noting |
| Three methods agree | High - investigate |
| Consensus + high volume | Critical - prioritize |

### What to Look For

1. **Clustering of High Exempt Days**
   - Multiple high exempt days in sequence
   - Coinciding with price movements

2. **Divergence Patterns**
   - Exempt volume increasing while total volume decreasing
   - Exempt ratio spiking without corresponding market events

3. **Day-of-Week Patterns**
   - Consistent elevated exempt on specific days
   - May indicate systematic activity

4. **Event Correlation**
   - High exempt around earnings
   - Spikes before/after significant news

---

## ❓ FAQ

### Q: How far back can I pull data?

FINRA maintains approximately 2 years of historical data. Setting `MONTHS_TO_SCRAPE` beyond available data will simply skip missing dates.

### Q: Why are some dates missing?

Data is only available for trading days. Weekends, market holidays, and any days the market was closed will not have data.

### Q: The notebook is running slowly. What can I do?

- Reduce `MONTHS_TO_SCRAPE`
- Analyze fewer symbols
- The ML cells can be skipped if not needed

### Q: Can I use this for any stock?

Yes! Simply change `TARGET_SYMBOLS` to include any ticker traded on NASDAQ, NYSE, or BATS exchanges.

### Q: How often is FINRA data updated?

FINRA publishes data with a T+1 delay. Today's data will be available tomorrow.

### Q: What does "Market: B,Q,N" mean?

These are the exchange codes where the security was traded:
- **B** = BATS Exchange
- **Q** = NASDAQ
- **N** = NYSE

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Areas for Improvement

- [ ] Additional visualization types
- [ ] More ML algorithms for anomaly detection
- [ ] Integration with price data for correlation
- [ ] Automated alerting system
- [ ] Historical comparison features

### Development Setup

```bash
git clone https://github.com/shinjimisato/finra-short-analysis.git
cd finra-short-analysis
pip install -r requirements.txt
pip install -r requirements-dev.txt  # For testing
```

---

## ⚠️ Disclaimer

**This tool is for educational and research purposes only.**

- This is not financial advice
- Past patterns do not guarantee future results
- High short exempt volume alone is not evidence of wrongdoing
- Always consult with qualified professionals before making investment decisions
- The authors are not responsible for any financial decisions made based on this analysis

### Data Accuracy

While this tool pulls directly from FINRA's official data source, users should:
- Verify critical findings through multiple sources
- Understand that data may contain errors at the source
- Not rely solely on automated analysis for important decisions

---



---

## 🙏 Acknowledgments

- [FINRA](https://www.finra.org/) for providing public short sale data
- [pandas_market_calendars](https://github.com/rsheftel/pandas_market_calendars) for trading day calculations
- The open-source community for the amazing tools that made this possible

---


<p align="center">
  <b>If you find this tool useful, please consider giving it a ⭐️</b>
</p>
