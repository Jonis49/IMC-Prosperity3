<h1 align="center">WHAT'S THE IMPACT</h1>

<p align="center">
  <strong>🏅 Placed <span style="font-size:1.3em;">6<sup>th</sup></span> Globally in IMC Prosperity 3 (2025)</strong><br>
  <em>Among 12,000+ competing teams worldwide</em>
</p>

**Team:**
- Jonatan Svensson  
- Jack Sherry  
- Adam Leddy  
- Conor Duddy  


This repository outlines our submission to the 2025 **IMC Prosperity 3** algorithmic trading competition — a global event that attracted over 12,000 teams. The challenge spanned five rounds, with new products and market complexities introduced every three days. In each round, teams submitted autonomous trading scripts designed to maximize PnL across synthetic markets that emulate real-world dynamics — including market making, ETF and options trading, cross-exchange arbitrage, and counterparty-driven strategies. Each round also included a game-theoretic problem, though these played a minor role relative to the algorithmic component.

After making no profit in the first round due to technical issues, we made a strong comeback and ultimately placed **6th globally**. We attribute this performance to a mix of curiosity, collaboration, and genuine enthusiasm for the kinds of problems that arise in the pursuit of optimal trading strategies. Despite juggling **three theses**, **six exams**, and even the **Irish Open Main Event** during the 15-day competition, we maintained a highly collaborative environment that allowed us to develop and iterate on sophisticated strategies across a wide range of products.

From the outset, we approached the competition not as a race to the top, but as an opportunity to gain hands-on experience with algorithmic trading. Our focus was on building strategies grounded in sound reasoning and mathematical insight — not exploiting leaked/recycled data[^1]. While the specifics varied by product, our overarching approach was to maximize expected value where possible, hedge intelligently when cost-effective, and favor robustness over the high-risk tactics incentivized by the competition’s payout structure.

What follows is a product-by-product breakdown of the algorithmic strategies we developed. We’ve omitted the game-theoretic components, as we don’t believe they are particularly interesting or representative of our approach.

<p align="center">
  <img src="images/prosperity_overview.png" width="600"/>
</p>

---

[^1]: The first couple of rounds were plagued with teams hardcoding strategies as IMC was reusing a lot of publicaly available data from previous runs of the competition.



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

This summarizes what we managed to implement before the final submission. After climbing from #98 to #6 in Round 4, we knew our algorithm was strong. Still cautious from the technical issues in Round 1, we chose to focus on delivering a robust and reliable script rather than pushing for further complexity. That said, some of the ideas we explored were promising enough to deserve a place in this write-up. Here is one of them.

---

The information contained in the global minimum and maximum of a random price walk can be leveraged more effectively than simply buying the minimum and selling the maximum. Once both extrema have occurred (noting that we only observe them as they happen), we gain a powerful constraint: the remainder of the walk must remain within the established min-max range. This fundamentally alters the distribution of future prices.
To illustrate this, consider the following edge case: the global minimum occurs, and we initiate a long position. Later, the global maximum is reached, and we switch to a short. If the price subsequently falls back to the level of the previously announced minimum, we now know with certainty that it cannot drop any further — doing so would contradict the declared minimum. This allows us to confidently enter a long position, exploiting the structural boundary created by the known extrema.


## Summary of 


## Price walk model
To develop this idea into a trading strategy, we first need a model for the price walk. Squid Ink was excluded from consideration due to its intentionally erratic behavior — designed to exhibit large, irregular jumps followed by reversion. Cracking the logic behind its generation would likely yield more generalizable edge elsewhere. Kelp, on the other hand, lacked sufficient volatility to make the min/max-based strategy actionable.
We therefore focus exclusively on Croissants. As discussed earlier, using the midpoint of the largest quantities in the order book gives a strong approximation of fair value. In Croissants, the spread between the two largest levels is typically one or two ticks, resulting in fair values that are either integers or half-integers. By multiplying all prices by 2, we obtain clean integer-valued price paths.
A reasonable model assumes that the price evolves as an i.i.d. random walk with steps drawn from a symmetric distribution. The figure below shows the empirical step distribution alongside a simplified model in which rare outliers are removed and symmetry is enforced.

<p align="center">
  <img src="images/olivia/step_distributions_and_paths.png" width="600"/>
</p>


## Modified step distribution
Given the assumption of an i.i.d. random walk, we can derive an optimal trading strategy using dynamic programming (DP). The key insight is that once both the global minimum and maximum have occurred, the remaining price path must stay within these bounds for the rest of the day. This constraint allows us to condition the step distribution at each point in the remaining walk, effectively updating the dynamics based on available information.

We work in a grid of shape `(remaining time steps) × (current price)`, and at each point on this grid we aim to adjust the step distribution based on how likely it is for future paths to remain within the given bounds. Far from the bounds or with few steps remaining, the step distribution remains essentially unchanged. In contrast, points near the upper or lower bound are subject to sharply skewed distributions: for instance, if the price is near the upper bound, downward steps become significantly more likely under the constraint that the walk must stay inside the range.

This conditioning can be formalized in a Bayesian framework. Knowing the bounds removes all future paths that would exit the allowed region. From any given point, we discard such violating paths and reweight the remaining ones to compute the updated distribution for the next step. Naively computing this requires evaluating all possible paths — an intractable problem, as the number of walks scales exponentially (e.g., 13^t with t ~ 10,000).

To make this computation feasible, we instead begin by computing the **survival probability**: the probability that a walk starting from each grid point remains within the bounds until the end. This can be done recursively in time. We initialize at t = 1, where almost all points have survival probability 1 — except those close enough to reach outside the boundaries in one step. From there, we propagate survival probabilities backward using a convolution with the step distribution at each slice in time. This recursive approach allows us to efficiently estimate, for any point, what proportion of paths from there end up inside and outside the bounds.

The figure below shows the resulting survival probabilities under the bounds [0, 10], assuming the following step distribution:

`step-prob = {0: 0.6, ±1: 0.17, ±2: 0.03}`

As expected, points near the bounds and with a lot of time left in the session are likely to walk outside the bounds, whereas points far from the bounds and close to the end show high survival probability.

<p align="center">
  <img src="images/olivia/survival_probability.png" width="600"/>
</p>

From the survival grid, the step distributions are updated by reweighting each possible step according to the survival probability of the resulting state (state reached by taking that step), and then normalizing to form a valid probability distribution. With these skewed step distributions in place, we are ready to formulate a DP algorithm that yields the optimal trading strategy.


## DP

**Note:** This DP formulation can be applied even without any bounding constraints on the price. However, in the case of a symmetric step distribution, the expected value of any trade becomes negative — since price changes have zero expected drift, and each trade incurs a cost from crossing the spread. It is precisely the bounds on the future price path that introduce asymmetry into the step distribution, making trading directionally profitable and the problem worth solving.



Problem formulation:
Given a realization of an i.i.d. random walk with a known step distribution 

Variables:
 * t - time left in session. After this time the lower and upper bounds no longer hold.
 * L - position limits.
 * s(t) - stock price with t iterations left in session.
 * BL, BU - upper and lower price bounds.
 * Spread - Cost/unit of crossing spread
 * x - position



Note that we need to wait for the second extrema before there will be enough information to trade. After the first we will enter a position one way. The bound we are given will only ever alter the rest of the walk in favour of our position and we will therefore not trade until the second extrema.



</details>

