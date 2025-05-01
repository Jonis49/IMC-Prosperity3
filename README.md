# WHAT'S THE IMPACT

Team:
- Jonatan Svensson
- Jack Sherry
- Adam Leddy
- Conor Duddy

This repository outlines our submission to the 2025 IMC Prosperity 3 algorithmic trading competition — a global event that attracted over 12,000 teams. The challenge unfolded over five rounds, with new products and complexities introduced every three days. In each round, teams submitted autonomous trading scripts designed to maximize PnL in a variety of synthetic markets that mirrored real-world dynamics such as market making, ETF and options trading, cross-exchange trading, and counterparty-based trading.

After a slow start in the early rounds, we made a strong comeback and ultimately placed **6th globally**. We attribute this result to a combination of tight collaboration, extensive data analysis, and disciplined quantitative modeling. While the specifics varied across products, our overall approach was grounded in mathematical rigor and empirical validation.

What follows is a breakdown of that process — asset by asset.

<p align="center">
  <img src="images/prosperity_overview.png" width="600"/>
</p>


---

<details>
<summary>RAINFOREST RESIN - a DP solution to optimal market making in a simplistic setting </summary>

Under mild assumptions the problem becomes optimally solvable.

</details>

---

<details>
<summary>SQUID INK - mean reversion </summary>

Under mild assumptions the problem becomes optimally solvable.

</details>

---

<details>
<summary>BASKETS (ETFs) - statistical arbitrage </summary>

dont include olivia here

</details>

---

<details>
<summary>VOLCANIC ROCK VOUCHERS (options) - volatility smile</summary>

WHAT'S THE IMPACT

</details>

---

<details>
<summary>MACARONS - cross-exchange arbitrage</summary>

WHAT'S THE IMPACT

</details>

---

<details>
<summary>LABELED COUNTERPARTIES -  a DP solution to optimal use of insider information </summary>

In the final round counterparty-information was added to the historical data. This meant that for all trades the identities of both buyer and seller were disclosed. Moreover, this information was going to be available during the run of the final submission. There were around 20 market participants all trading their own set of produts. 

We began by plotting PnL for each counterparty, split by product and decomposed into execution and holding components. Some counterparties made a fortune through execution, consistently trading at favorable prices. However, this insight offered little value for refining our algorithms, as we were already aggressively pursuing those opportunities through market making. Only one counterparty stood out in terms of strong holding-pnl and upon further investigation it was clear that they always bought at the daily minimum and sold at the daily maximum and never took on any other trades. This counterparty only traded Kelp, Squid Ink and Croissants.

Due to the stable nature of Kelp's price putting on delta bets with this min/max information would not generate nearly enough pnl to make up for the market making we would have to give up. 

Squid Ink was more fruitful. With big daily swings the historical pnl from buying the min and selling the max netted profits similar to what our current strategy was giving. Moreover, as our strategy for Squid Ink did not rely on market making we could incorporate the min/max based directional trading without giving up the profits from the original strategy. The only change we had to make was to shorten the liquidation time for the mean reversion whenever we had on a delta bet.   

</details>
