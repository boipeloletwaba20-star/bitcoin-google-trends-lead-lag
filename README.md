# Lead–Lag Relationships Between Bitcoin and Internet Attention

**Does Google search interest move ahead of Bitcoin's price, and can it help predict returns?**



---

## Problem
Investigate whether internet attention, measured with Google Trends, **leads or lags** Bitcoin market activity (weekly returns and trading volume), and whether that attention is a useful **predictor** of Bitcoin returns and volatility.

**Hypotheses (Granger causality, α = 0.05)**
- **H₀:** Google Trends attention does not Granger-cause Bitcoin weekly log returns, and returns do not Granger-cause attention.
- **H₁:** Attention and returns do Granger-cause one another, in at least one direction.

## Data
| Source | What | Period | Frequency |
|---|---|---|---|
| [Yahoo Finance](https://finance.yahoo.com/quote/BTC-USD/) via `yfinance` | BTC-USD open, high, low, close and volume | 1 Jan – 31 Dec 2025 | Daily, summed to weekly |
| [Google Trends](https://trends.google.com/) via `pytrends` | Search interest index (0–100), US | 1 Jan – 31 Dec 2025 | Weekly |

**Keywords and why they were chosen**
| Keyword | Captures |
|---|---|
| Bitcoin | Broad public awareness |
| BTC price | Price-focused attention |
| BTC USD | Valuation searches against the US dollar |
| Bitcoin price prediction | Speculative, forward-looking sentiment |
| BTC volatility | Risk and uncertainty perception |

After aligning on the weekly Friday close, the final dataset has **53 weekly observations**. Both sources are downloaded live when the notebook runs, so no data files are stored in this repo.

## Tools
Python · Google Colab · `yfinance` · `pytrends` · `pandas` · `numpy` · `statsmodels` (ADF, KPSS, STL, cross-correlation, Granger causality) · `scikit-learn` (Random Forest) · `arch` (GARCH) · `matplotlib`

## Method

### Data preparation
1. **Returns:** log returns, rₜ = ln(Pₜ / Pₜ₋₁), chosen over simple returns because they add up across time and are closer to stationary. Daily log returns were summed to weekly returns.
2. **Volume:** daily volume was summed to weekly totals and log-transformed to stabilise variance.
3. **Cleaning:** a DatetimeIndex, forward and backward filling of gaps, z-score normalisation for comparing the series, and a merge on the weekly date.

### Exploratory and stationarity analysis
4. **Stationarity:** ADF and KPSS tests. Weekly returns and the Trends series were stationary, while price and volume were not and were first-differenced.
5. **Decomposition:** STL with a 13-week seasonal period, applied to each Trends series.

### Lead–lag analysis
6. **Cross-correlation:** returns against each keyword for lags from −12 to +12 weeks. A positive lag means searches lead returns.
7. **Granger causality:** tested in both directions (Trends → Returns and Returns → Trends) at lag 1, on the stationary series.

### Modelling
8. **Machine learning:** a Random Forest Regressor predicting weekly returns from the Google Trends features.
9. **Volatility:** a GARCH(1,1) model of Bitcoin returns, used to examine whether attention relates to periods of heightened volatility.

## Key findings

### Lead–lag by keyword
| Keyword | Best lag (weeks) | Correlation | Direction |
|---|---|---|---|
| Bitcoin | +1 to +3 | Moderate positive | Trends lead returns |
| BTC price | +1 to +3 | Moderate positive | Trends lead returns |
| BTC USD | 0 to +1 | Weak positive | Near-contemporaneous |
| Bitcoin price prediction | ±0 | Weak / mixed | Ambiguous |
| BTC volatility | ~0 | Near zero | No clear lead |

### Statistical tests and models
| Test / model | Result | Interpretation |
|---|---|---|
| Granger: Trends → Returns | p ≥ 0.05 (lag 1) | Not significant |
| Granger: Returns → Trends | p ≥ 0.05 (lag 1) | Not significant |
| Random Forest Regressor | MSE = 0.0019, R² = 0.23 | Captures broad direction only |
| GARCH(1,1) | Volatility clustering present | Attention adds little explanatory power |

- **Search interest appears to lead returns by about 1–4 weeks.** "Bitcoin" and "BTC price" showed the clearest moderate positive cross-correlations at positive lags.
- **The relationship is not statistically significant.** Granger causality was not significant in either direction, so H₀ cannot be rejected. The evidence supports a *soft* lead, not a causal one.
- **"BTC volatility" searches move at the same time as the market**, not ahead of it.
- **The Random Forest explains only about 23% of the variance** in weekly returns. "Bitcoin" and "BTC price" were the most important features, and "BTC volatility" the least.
- **The main limitation is sample size:** about 52 weekly observations give low statistical power. Three to five years of weekly data (150–260 observations) would be needed for firm conclusions.

![Cross-correlation of Google Trends keywords with Bitcoin weekly returns](images/cross_correlation.png)

## How to run
1. Open the notebook in Google Colab: [Open in Colab]([COLAB_LINK_HERE):(https://colab.research.google.com/drive/1oiGeJnbj2zrPMKoG8BHNUk7DsN9tFHdZ?usp=sharing)
2. **Runtime → Run all.** The first cell installs `yfinance`, `pytrends` and `arch` if they're missing.
3. The notebook downloads the Bitcoin and Google Trends data itself. Google Trends sometimes rate-limits requests. If you get a `429` error, wait a minute and re-run the data-collection cell.

## Repository structure
```
bitcoin-google-trends-lead-lag/
├── README.md
├── bitcoin_lead_lag.ipynb/py
