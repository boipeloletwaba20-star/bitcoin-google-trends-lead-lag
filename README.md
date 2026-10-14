Lead–Lag Relationships Between Bitcoin and Internet Attention
Does Google search interest move ahead of Bitcoin's price, and can it help predict returns?
EDAB 6808 Assignment 1 · B.Com Business Analytics (Hons), University of the Free State · Pair project, 2026
---
Problem
This project tests whether public attention to Bitcoin, measured with Google Trends, leads or lags Bitcoin's weekly returns and trading volume, and whether that attention is a useful predictor of them.
Hypotheses (tested with Granger causality at α = 0.05)
H₀: Google Trends attention does not Granger-cause Bitcoin weekly log returns, and returns do not Granger-cause attention.
H₁: Attention and returns do Granger-cause one another, in at least one direction.
Data
Source	What	Period	Frequency
Yahoo Finance via `yfinance`	BTC-USD open, high, low, close and volume	1 Jan – 31 Dec 2025	Daily, summed to weekly
Google Trends via `pytrends`	Search interest index (0–100), US	1 Jan – 31 Dec 2025	Weekly
Keywords and why they were chosen:
Keyword	Captures
Bitcoin	Broad public awareness
BTC price	Price-focused attention
BTC USD	Valuation searches against the dollar
Bitcoin price prediction	Speculative, forward-looking sentiment
BTC volatility	Risk and uncertainty perception
After aligning everything on the weekly Friday close, the final dataset has 53 weekly observations. Both sources are downloaded live when the notebook runs, so no data files are stored in this repo.
Tools
Python · Google Colab · `yfinance` · `pytrends` · `pandas` · `statsmodels` (ADF, KPSS, STL, Granger causality) · `scikit-learn` (Random Forest) · `arch` (GARCH) · `matplotlib`
Method
Returns: log returns, rₜ = ln(Pₜ / Pₜ₋₁), chosen because they add up across time and are closer to stationary. Daily returns and volume were summed to weekly totals, and volume was log-transformed.
Cleaning: a DatetimeIndex, forward and backward filling of gaps, z-score normalisation for comparing the series, and a merge on the weekly date.
Stationarity: ADF and KPSS tests. Returns and the Trends series were stationary. Price and volume were first-differenced.
Decomposition: STL with a 13-week season, applied to each Trends series.
Lead–lag: cross-correlation from −12 to +12 weeks (a positive lag means searches lead returns), and Granger causality in both directions at lag 1.
Machine learning: a Random Forest Regressor predicting weekly returns from the Trends features.
Volatility: a GARCH(1,1) model of Bitcoin returns.
Key findings
Search interest appears to lead returns by about 1–4 weeks. "Bitcoin" and "BTC price" showed the clearest moderate positive cross-correlations at positive lags.
The relationship is not statistically significant. Granger causality was not significant in either direction (p ≥ 0.05), so H₀ could not be rejected. The evidence supports a soft lead, not a causal one.
"BTC volatility" searches move at the same time as the market, not ahead of it.
The Random Forest has weak predictive power: MSE = 0.0019 and R² = 0.23. "Bitcoin" and "BTC price" were the most important features.
GARCH(1,1) confirms volatility clustering in Bitcoin returns, but attention variables add little explanatory power.
Main limitation: about 52 weekly observations give low statistical power. Three to five years of data (150–260 weeks) would be needed for firm conclusions.
Test	Result	Interpretation
Cross-correlation (Bitcoin, BTC price)	Peak at lag +1 to +3	Searches tend to lead returns
Cross-correlation (BTC volatility)	Peak near lag 0	Contemporaneous
Granger: Trends → Returns	p ≥ 0.05	Not significant
Granger: Returns → Trends	p ≥ 0.05	Not significant
Random Forest	R² = 0.23, MSE = 0.0019	Broad direction only
GARCH(1,1)	Volatility clustering present	Attention adds little
![Cross-correlation of Google Trends keywords with Bitcoin weekly returns](images/cross_correlation.png)
How to run
Open the notebook in Google Colab: Open in Colab
Run all cells (Runtime → Run all). The first cell installs `yfinance`, `pytrends` and `arch` if they're missing.
The notebook downloads the Bitcoin and Google Trends data itself. Google Trends sometimes rate-limits requests. If you get a `429` error, wait a minute and re-run the data-collection cell.
Repository structure
```
bitcoin-google-trends-lead-lag/
├── README.md
├── bitcoin_google_trends_lead_lag.ipynb
└── images/
    ├── normalised_series.png
    ├── cross_correlation.png
    ├── rf_actual_vs_predicted.png
    └── garch_volatility.png
```
Report and presentation
Full report (view-only)
Presentation slides (view-only)
Portfolio page
References
Barber, B. M., & Odean, T. (2008). All that glitters: The effect of attention and news on the buying behavior of individual and institutional investors. The Review of Financial Studies, 21(2), 785–818.
Bollerslev, T. (1986). Generalized autoregressive conditional heteroskedasticity. Journal of Econometrics, 31(3), 307–327.
Cont, R. (2001). Empirical properties of asset returns: Stylized facts and statistical issues. Quantitative Finance, 1(2), 223–236.
Da, Z., Engelberg, J., & Gao, P. (2011). In search of attention. Journal of Finance, 66(5), 1461–1499.
Preis, T., Moat, H. S., & Stanley, H. E. (2013). Quantifying trading behavior in financial markets using Google Trends. Scientific Reports, 
