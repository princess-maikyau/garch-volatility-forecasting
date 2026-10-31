# Financial Market Volatility Forecasting

*GARCH/GJR-GARCH models and options-income ETF yields*

Time-varying volatility modelling (GARCH, GJR-GARCH) on 10 years of S&P 500 and Tesla returns, used to explain the growth and sustainability of "options-income" covered-call ETFs — built for the *Financial Analytics* module of Warwick Business School's MSc Business Analytics.

## Motivation

A 2026 *Financial Times* article documented the rapid growth of options-income ETFs (assets up from $320m to $7.8bn in two years), reporting headline yields from 10.9% up to 57% while cautioning that income is "uneven" and that very high yields likely erode capital. The article's claims are entirely qualitative. This project tests them quantitatively: **can time-varying volatility forecasts explain both the appeal and the instability of these funds' income?**

Three hypotheses are tested:

- **H1** — Returns exhibit volatility clustering, so a constant-variance model is inadequate.
- **H2** — A GJR-GARCH model (which allows asymmetric "leverage" effects) describes volatility better than symmetric GARCH.
- **H3** — Higher forecast volatility implies higher option premiums, explaining both the appeal and the fragility of these funds' yields.

## Data

Daily prices for the S&P 500 (`^GSPC`) and Tesla (`TSLA`) from 5 Jan 2016 to 21 May 2026 (2,610 observations), pulled live via the `yfinance` library — no static dataset is required to reproduce this analysis. Three real covered-call ETFs (JEPI, QYLD, XYLD) are also pulled for supporting context.

## Methodology

- **GARCH(1,1)** and **GJR-GARCH(1,1)** fitted by maximum likelihood with Student-*t* innovations (justified by heavy tails/excess kurtosis in both series), compared via AIC/BIC.
- **Volatility clustering test**: Ljung-Box on squared returns.
- **Out-of-sample forecast horse-race**: rolling 10-day-ahead average volatility forecasts, evaluated with RMSE and the asymmetric QLIKE loss.
- **Black-Scholes translation**: forecast conditional volatility → a stylised 1-month at-the-money covered-call premium yield, evaluated at both the current and long-run volatility regime.

## Key results

| | S&P 500 | Tesla |
|---|---|---|
| Annualised volatility | 18.03% | 58.48% |
| GARCH persistence (α+β) | 0.996 | 0.994 |
| GJR leverage θ (significance) | 0.243 (t = 6.12, **significant**) | 0.009 (t = 0.81, not significant) |

- **H1 confirmed**: Ljung-Box statistics on squared returns are highly significant for both assets (p < 0.001) — volatility clustering is strongly present.
- **H2 partially confirmed**: the leverage effect is large and significant for the diversified index but statistically indistinguishable from zero for the single stock — asymmetry depends on *what* you're modelling, not just *that* it's volatile.
- **H3 confirmed**: mapping forecast volatility through Black-Scholes gives stylised annualised yields of **22.9%** (S&P 500, current) rising to **89.7%** (Tesla, current), and spikes to **150.5%** in an extreme Tesla volatility regime — directly reproducing the FT's 57% headline-yield observation. But the *long-run* sustainable yield is much lower (68.5% for the S&P 500, 103.2% for Tesla), confirming the "no free lunch" warning: headline yields on volatile assets are transient and must decay as volatility mean-reverts.

See the notebook for the full news-impact curves, rolling forecast comparison, and ETF drawdown analysis, plus a discussion of the limitations (the mismatch between a time-varying GARCH input and Black-Scholes' constant-volatility assumption, the omission of the volatility risk premium, and real-world fund fees/frictions).

## Repository structure

```
notebooks/
  garch_volatility_forecasting.ipynb   # data pull, GARCH/GJR-GARCH fitting, forecasting, Black-Scholes translation
```

## Running it

```bash
pip install -r requirements.txt
jupyter notebook notebooks/garch_volatility_forecasting.ipynb
```

No manual data download needed — the notebook fetches prices directly via `yfinance` on each run.

## Tech stack

Python · `arch` (GARCH/GJR-GARCH) · `yfinance` · `statsmodels` · `scipy` · `pandas` / `numpy` · `matplotlib`

## Attribution

This is an individually authored piece of coursework (Financial Analytics, MSc Business Analytics, Warwick Business School) — no group collaborators.

## License

MIT — see [LICENSE](LICENSE).
