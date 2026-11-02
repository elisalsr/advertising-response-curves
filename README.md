# Advertising response curves and budget allocation

How should an advertiser split a fixed media budget across channels? This project builds a simple marketing mix model in Python to measure the carryover and diminishing returns of five media channels, including search, then simulates a budget reallocation at constant spend.

## Data

Public simulated dataset from Meta's open source [Robyn](https://github.com/facebookexperimental/Robyn) project: 208 weeks (November 2015 to November 2019) of revenue, spend on five channels (TV, out of home, print, Facebook, search) and control variables (competitor sales, events, newsletter). The data is simulated, so the results illustrate the method rather than a real brand.

## What the data shows

- Spend is highly concentrated: out of home takes 62% of the budget, TV 21%, search only 8.5%.
- TV, out of home, print and Facebook run in bursts. Search is always on and follows the yearly cycle.
- Revenue is strongly seasonal, with a peak in winter and a low in summer. Seasonality has to be controlled for, otherwise its effect would be wrongly attributed to search.

## Method

1. **Adstock**: the effect of an ad carries over to the following weeks. Longer carryover is allowed for TV, out of home and print (rate up to 0.8) than for Facebook and search (up to 0.3), since search mostly captures existing demand.
2. **Saturation**: each extra euro brings less revenue than the previous one, modelled with a Hill curve.
3. **Regression**: weekly revenue is explained by the five transformed channels, plus seasonality, trend, competitor sales, events and newsletter. Media coefficients are constrained to be non negative.
4. **Parameter search**: the 15 media parameters are found with differential evolution, by minimising the prediction error.
5. **Response curves**: revenue generated as a function of weekly spend, for each channel, with the marginal ROI at the current spend level.
6. **Budget optimisation**: the total budget is kept constant, and each channel can move by at most 50% from its current level, to stay within the range observed in the data.

## Results

The model explains 92% of the weekly revenue variance (in-sample R²).

| Channel | Share of budget | Average ROI | Marginal ROI |
|---|---|---|---|
| Facebook | 3.1% | 16.7 | 32.7 |
| Search | 8.5% | 6.7 | 11.8 |
| TV | 21.3% | 6.3 | 7.6 |
| Print | 5.3% | 6.2 | 7.8 |
| Out of home | 61.9% | 1.3 | 1.2 |

Out of home takes most of the budget but returns the least, both on average and on the next euro spent.

**Recommended allocation at constant budget**

| Channel | Current share | Recommended share | Change |
|---|---|---|---|
| TV | 21.3% | 31.9% | +50% |
| Out of home | 61.9% | 42.8% | -31% |
| Print | 5.3% | 8.0% | +50% |
| Facebook | 3.1% | 4.6% | +50% |
| Search | 8.5% | 12.7% | +50% |

Moving about 30% of the out of home budget to the other channels would increase weekly revenue by **6.9%** (about 126k per week) without spending more.

## Limitations and next steps

- The data is simulated, and the fit was measured on the same data used for training. A next step would be to hold out the last weeks to test the model out of sample.
- Response curves assume a steady weekly spend, while real campaigns run in bursts.
- Four channels reach the +50% limit, so the optimiser would go further. Larger shifts should first be validated with an incrementality test, for example a geo experiment, before scaling.
- Search is correlated with seasonal demand. Seasonality is controlled for, but part of the search effect may still reflect demand it captures rather than creates.

## Tools

Python (pandas, NumPy, SciPy, Matplotlib), Google Colab.
