# FCN Risk Simulator

**How does a worst-of Fixed Coupon Note (FCN) behave in different markets, and where does its risk come from?**

A Monte Carlo model in Python that prices a worst-of FCN on **TSMC (TSM) and NVIDIA (NVDA)**, compares its outcomes with simply holding the stocks in bull, sideways and sell-off markets, measures how strike, volatility and correlation drive the coupon and the risk, and back-tests the note on ten years of real prices.

> Status: core model complete; extensions in progress (see Roadmap).

## What is an FCN?

A Fixed Coupon Note pays a fixed monthly coupon. Each month, if **all** underlying stocks are at or above their starting price, the note ends early ("knocks out") and the investor gets their money back. If it never knocks out and the **worst** stock finishes below the strike (e.g. 85% of its starting price), the investor receives that stock at the strike price instead of cash.

In option terms, the investor is **selling a put option on the worst of several stocks**. The high coupon is the premium for that risk.

![Payoff](01_payoff.png)

## Product terms modelled

| Term | Value |
|---|---|
| Underlyings | TSM, NVDA (worst-of) |
| Tenor | 12 months, monthly observations |
| Knock-out | 100% of initial (all stocks) |
| Strike | 85% of initial, physical delivery |
| Coupon | Set at fair value minus a 2% p.a. issuer margin |

## Method

1. **Market data** – daily adjusted prices from Yahoo Finance; volatility and correlation estimated from 3 years of daily log returns.
2. **Simulation** – 20,000 correlated geometric Brownian motion paths (Cholesky decomposition of the correlation matrix).
3. **Pricing** – the fair coupon is found under the risk-neutral measure, so that the present value of coupons plus capital returned equals 100%.
4. **Scenarios** – bull (+20% drift), sideways (0% drift, lower volatility) and sell-off (−20% drift, higher volatility), compared with holding an equal-weight basket of the stocks.
5. **Sensitivity** – fair coupon and delivery probability across strike, volatility and correlation.
6. **Back-test** – the same note bought every week over the past decade, using actual prices.

## Key findings

RESULTS_PLACEHOLDER

![Scenarios](03_scenarios.png)
![Sensitivity](04_sensitivity.png)
![Back-test](05_backtest.png)

## Limitations

- GBM assumes constant volatility and normally distributed returns; real markets have volatility clusters and jumps, so crash risk is likely **understated**.
- Dividends, funding costs and the issuer's credit risk are ignored.
- Scenario drifts and volatility levels are illustrative assumptions, not forecasts.
- Back-test windows overlap, and use today's coupon rather than the coupon available at each start date.

## Roadmap

- [ ] Replace constant volatility with a **GARCH(1,1)** forecast
- [ ] Train a **neural network (TensorFlow)** as a fast surrogate model that estimates the fair coupon and delivery risk directly from product terms
- [ ] Add Hong Kong underlyings and features such as a knock-in barrier or step-down knock-out

## How to run

```bash
pip install -r requirements.txt
jupyter notebook fcn_risk_simulator.ipynb
```
Run all cells. Tickers and product terms can be changed in the **Settings** section.

---
*For educational purposes only. Uses public market data only; not investment advice.*
