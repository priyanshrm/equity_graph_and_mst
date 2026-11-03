# Mapping the NYSE with Minimum Spanning Trees

## The idea

**Research question:** How does the stock market's correlation network change between normal, crisis and booming periods?

Stocks do not move independently. This project models each stock as a **node** and the similarity of its daily returns to other stocks as **weighted connections**. A **Minimum Spanning Tree (MST)** reduces the network to the smallest set of connections that links every stock without cycles and minimizes total financial distance.

For 1,540 stocks, a complete network has **1,185,030 possible connections**, while an MST uses just **1,539**. This makes market structure much easier to visualize and compare.

## Data and workflow

The initial dataset contains **3,384 US NYSE listings**, with ticker, company and industry information. Approximately three years of adjusted daily closing prices were downloaded using `yfinance`. Filtering out stocks missing a price on the first trading date left **1,540 candidates**. Stocks with undefined correlations are excluded before building the full tree, so the final node count can be smaller.

1. Calculate daily simple stock returns.
2. Estimate **Pearson correlations** between returns.
3. Convert correlations into **Mantegna financial distances**:

   $$d_{ij} = \sqrt{2(1 − \rho_{ij})}$$

   A distance of `0` indicates perfectly correlated returns; `2` indicates perfectly opposite returns.
4. Construct the complete weighted graph and calculate its MST using `NetworkX` and `SciPy`.
5. Visualize the full NYSE tree and compare a smaller set of **15 major NYSE stocks** across market conditions.

## Market regime experiment

The same 15 stocks are used in all three periods: `JPM`, `BAC`, `WMT`, `XOM`, `JNJ`, `V`, `MA`, `UNH`, `HD`, `PG`, `KO`, `DIS`, `CAT`, `GS`, `CVX`.

1. **Normal, May to June 2024:** 41 daily observations; average MST distance **0.957**; 3 of 14 links within the same industry (**21.43%**); leading hubs **PG, BAC, XOM**.
2. **Crisis, March to April 2025:** 42 observations; average distance **0.628**; 2 of 14 same industry links (**14.29%**); leading hubs **GS, V, MA**.
3. **Boom, May to June 2025:** 41 observations; average distance **0.805**; 3 of 14 same industry links (**21.43%**); leading hubs **HD, JPM, JNJ**.

These are illustrative, event based period labels rather than statistically validated market regimes.

## Main findings

**Crisis connections were closer.** The average MST edge distance fell from `0.957` in the normal period to `0.628` during the crisis. This is consistent with stronger correlations **among the connections selected by the tree**.

**Network hubs changed.** Different stocks had the most direct MST connections in each period, suggesting that the selected correlation structure is dynamic.

**Industry patterns appeared.** Examples from the 15 stock analysis include `JPM` with `BAC`, `XOM` with `CVX`, and `V` with `MA`.

## Interpretation and limitations

The MST is a **descriptive tool for visualizing relationships**, not a stock forecasting model. High degree centrality does not establish market influence or causation. A smaller mean MST distance does not prove that the **average correlation of all stock pairs** rose.

Results are exploratory because each comparison period has only about 41 trading observations and uses just 15 selected stocks. Filtering on the first available date does not guarantee complete later data. The universe may also exhibit survivorship bias. A stronger follow up would compare average correlations across **all pairs**, test more market windows and validate the regime definitions with volatility measures.

## Reproduce the analysis

**Python libraries:** `pandas`, `numpy`, `yfinance`, `tqdm`, `scipy`, `networkx`, `matplotlib`.

```bash
pip install pandas numpy yfinance tqdm scipy networkx matplotlib
```

Workflow: **adjusted prices → daily returns → correlation matrix → distance matrix → MST → regime comparison**.

Generated data files include `nyse_stock_prices.csv`, `nyse_correlation_matrix.csv`, and `nyse_distance_matrix.csv`.
