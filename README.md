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
<summary>LABELED COUNTERPARTIES – a DP solution to optimal use of insider information</summary>
<br>
  
In the final round, counterparty identifiers were added to the historical data. This meant that for every trade, the identities of both the buyer and seller were disclosed — and this information would also be available in real time during the final submission. There were around 20 market participants, each focused on their own subset of products.

We began by plotting PnL for each counterparty, split by product and decomposed into execution and holding components. Some counterparties generated substantial execution PnL by consistently trading at favorable prices. However, this insight offered limited value for refining our algorithms, as we were already fully exploiting those opportunities through aggressive market making.

Only one counterparty stood out in terms of holding PnL. Upon further inspection, it became clear that they exclusively bought at the daily minimum and sold at the daily maximum — without taking any other trades. This behavior was observed only in Kelp, Squid Ink, and Croissants.

Due to Kelp’s relatively stable price dynamics, directional bets based on this min/max behavior were unlikely to generate enough profit to justify displacing our existing market-making strategy.

Squid Ink, on the other hand, was more promising. With large daily swings, the historical PnL from a simple min/max strategy matched the performance of our current approach. Since our existing Squid Ink strategy didn’t rely on market making, we could layer in directional trades without cannibalizing our core PnL. The only modification required was to shorten the liquidation horizon for mean-reverting trades whenever a directional bet was active.

Trading the min/max for Croissants proved highly profitable, thanks to large daily price swings and generous position limits. Since Croissants had previously been used only to hedge basket exposure, this new directional strategy introduced no conflicts with our existing setup.
We quickly realized that Croissant exposure could be scaled up indirectly through the baskets. To evaluate this, we used historical data to compare the PnL of a Croissant-driven basket strategy against the statistical arbitrage approach we had relied on in earlier rounds. On average, the two performed similarly, though daily results could diverge significantly.
To capture the strengths of both, we adopted a hybrid approach: we introduced a Croissant-based term into the basket's target position (see section on basket trading). The weight of this term was calibrated through backtesting on historical data.

This summarizes what we managed to implement before the final submission. After climbing from #98 to #6 in Round 4, we knew our algorithm was strong. Still cautious from the technical issues in Round 1, we chose to focus on delivering a robust and reliable script rather than pushing for further complexity. That said, some of the ideas we explored were promising enough to deserve a place in this write-up. What follows is one of them.

---

The information contained in the global minimum and maximum of a random price walk can be leveraged more effectively than simply buying the minimum and selling the maximum. Once both extrema have occurred (noting that we only observe them as they happen), we gain a powerful constraint: the remainder of the walk must remain within the established min-max range. This fundamentally alters the distribution of future prices.
To illustrate this, consider the following edge case: the global minimum occurs, and we initiate a long position. Later, the global maximum is reached, and we switch to a short. If the price subsequently falls back to the level of the previously announced minimum, we now know with certainty that it cannot drop any further — doing so would contradict the declared minimum. This allows us to confidently enter a long position, exploiting the structural boundary created by the known extrema.

To develop this idea into a trading strategy we need a model for the price walk. The dynamics of Squid Ink price is hard to model as it's intended to ocassionally have massive jumps followed by reverting behaviour; if we could figure out the secret behind the generation of that price series we would be in a position to make plenty of money in other ways. As Kelp did not show large enough moves this to be actionable we focus soley on Croissants. As mentioned before, using the mid of the largest quantities on the order book as fair gives a strong approximation of the true fair. The Croissant market is constantly showing a one or two tick spread between the largest quantities giving resulting in either integer or .5 fairs. Multiplying everything by 2 we get nice integers to work with. A reasonable model is obtained by assuming that the steps are i.i.d variabels from a symmetrical distribution. 

<p align="center">
  <img src="images/olivia/step:distributions_and_paths.png" width="600"/>
</p>



Note that we need to wait for the second extrema before there will be enough information to trade. After the first we will enter a position one way. The bound we are given will only ever alter the rest of the walk in favour of our position and we will therefore not trade until the second extrema.






</details>

