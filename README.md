# ⚽ Soccer Prediction Model

The primary incentive behind this project was financial. I wanted an edge in sports trading. A repeatable, quantifiable advantage over the market.

That edge would not come from intuition and watching games. It would come from an algorithmic model that estimates the true probability of a match outcome, independent of bookmaker pricing.

So I built one.

This repository outlines the current architecture with explicit examples accompanying each component, and how the model performed to the single goal of finding value where the market is wrong.

To read more about how the algortihmic model was built and few examples, go [here](General%20Theory.md).

Now, for the results, I focused on;

- **Expected Goals (xG):**  The model’s primary function is to predict Expected Goals (xG); by comparing its forecast to the actual xG generated in a match, we can validate the accuracy of its foundational building blocks.
- **Probability:** Monte Carlo simulations translate xG into match odds, revealing where the market's pricing diverges from the model's calculated reality.
- **Profitability:** Profitability is the ultimate filter; backtesting against market odds determines whether the edge identified in the probabilities translates into actual returns.

## Expected Goals (xG)

Before diving into the results, here's a quick look at the three metrics I used to evaluate the model's performance;

- **MAE (Mean Absolute Error)**: This tells you the average size of the prediction errors. If the model predicts a team will generate 2.0 expected goals and they actually produce 1.5, that's an error of 0.5. The MAE simply averages all these individual errors across every prediction. Because it's measured in goals, it's fairly intuitive, lower numbers mean better predictions.
- **RMSE (Root Mean Square Error)**: This works similarly to MAE but places extra weight on larger mistakes. By squaring the errors before averaging them, any particularly bad predictions stand out. When RMSE runs noticeably higher than MAE, it suggests the model occasionally produces some really bad predictions that pull down the overall performance.
- **R² Score**: This measures how much of the real-world variation the model actually captures. A score of 1 would mean perfect predictions. A score of 0 means the model adds nothing, you'd be just as well off guessing the average every time. Any positive number indicates the model has identified some genuine patterns that help explain what's happening on the pitch.

### Performance Against Expected Goals

To give you some context, a team's expected goals in a single match usually falls somewhere between **0.5** and **2.5**. That's the range we're working with. The model posted an MAE of **0.598**. In plain terms, when the model projects a team's xG for a match, it's typically off by about six-tenths of a goal. Given the range we're dealing with, that's a reasonable margin.

![Alt text](assets/mae_range.png)

The RMSE came in at **0.786**. What matters most here is the relationship between the two error measures. The gap of **0.188** between RMSE and MAE tells me the model isn't producing those disastrous predictions I mentioned earlier. No single match is throwing everything off, the errors are fairly consistent across the board.

![Alt text](assets/rmse_mae_dif.png)

The R² score of **0.158** means the model explains roughly **16%** of the variance in actual xG. Not overwhelming, but a clear signal that it has picked up on something real.

![Alt text](assets/rscore.png)

### Performance Against Actual Goals

This is the ultimate test. The performance here has inevitably weaker results, as we move from predicting a relatively stable metric (xG) to predicting discrete, low-probability events heavily influenced by finishing ability, goalkeeping, and simply plain luck. By comparing the model's performance against actual goals with how well the post-match xG metric performs at the same task, we can get a clearer picture of both the model's capabilities and football's inherent unpredictability.

Against actual goals, the model records a Mean Absolute Error of **0.847**. For comparison, the actual post-match xG achieves an MAE of **0.785** when predicting the same goal outcomes. The gap between them is just **0.061** goals. To put that in perspective, the model misses the actual scoreline by roughly one additional goal every sixteen matches compared to the industry standard metric. Given that the model makes its predictions before a ball is kicked, while actual xG is calculated after the fact using detailed shot data, landing this close to the benchmark is a genuinely encouraging result.

![Alt text](assets/mea_dif_model_xg.png)

The RMSE tells a similar story. For actual xG, the RMSE against goals sits at **1.010**, with the gap between RMSE and MAE reaching **0.225**. This spread reflects that even the post-match metric produces occasional larger errors. For the model, the RMSE of 1.090 against goals, alongside its MAE, yields a gap of **0.243**. The difference between these two spreads is a modest **0.018**. In other words, the model generates larger errors at almost exactly the same rate as the benchmark. When it misses by a wider margin, it's doing so in precisely the situations where randomness dominates, matches where clear chances go unconverted or speculative shots somehow find the net.

![Alt text](assets/both_gaps.png)

The R² score offers the most revealing comparison. Actual xG, despite being derived from the actual shots taken in a match, explains only **20.3%** of the variance in goal scoring. This figure isn't a limitation of the metric; it's the mathematical expression of football's fundamental randomness. Against this ceiling, the model explains **7.2%** of goal variance, capturing approximately one-third of the predictive power of the actual xG metric. In a sport where even the best possible measure of chance quality leaves 80% of outcomes unexplained, explaining any meaningful portion of the variance confirms that the model has successfully identified genuine relationships between match dynamics and actual scoring.

![Alt text](assets/model_xg_r2.png)

## Probability

With the model's core xG predictions validated, the next section is about how well do those predictions translate into match probabilities. After all, the financial edge I'm seeking doesn't come from accurately forecasting expected goals, it comes from identifying when the market's odds diverge from the true likelihood of an outcome. That requires probabilities that are not only accurate in isolation but also well-calibrated enough to act upon when compared against bookmaker pricing.

For this reason, I'm focusing on two specific markets where the model can be tested against real-world odds: the **1X2 market** (home win, draw, away win) and the  **Over/Under 2.5 goals market** . These represent the most liquid and widely available betting markets, making them the natural candidates for a value-based trading strategy.

Before diving into the results, here's a quick look at the metrics I used to evaluate the model's probability predictions;

* **Log Loss (Cross-Entropy):** This measures the accuracy of probability predictions by penalizing confident wrong predictions heavily. Think of this as a penalty system for probability predictions. If you predict an event will happen with 90% confidence and it happens, you get a small penalty. If you predict 90% confidence and it *doesn't* happen, you get a massive penalty. The scale starts at 0 (perfect) and goes up from there. To make sense of the number:
  * **For binary classification** (two choices): a naive model that always predicts 50% for everything would score around 0.693. The further below 0.693, the better the model is at making confident, accurate predictions.
  * **For three-class problems**: a naive model that always predicts 33% probability for each class (since there are three equally likely options) would score around 1.099. If the model scores near 1.099, it's essentially guessing randomly. The further below this number, the more confidently and accurately the model is identifying the correct class out of the three possibilities.
* **Ranked Probability Score (RPS):** This accounts for the natural order of football outcomes (Home > Draw > Away). It measures how close your predicted probabilities are to the actual results, but in a cumulative way, for example, predicting a draw when the actual result is a home win is considered a smaller mistake than predicting an away win, because the ranks are closer. A score of 0 is perfect, 1 is the worst. For context, a naive model that simply guesses 33% for each outcome would score around 0.22.
* **Brier Score**: This measures the accuracy of the predicted probabilities for binary outcomes (like Over 2.5 goals). If you predict something with high confidence and you’re right, that’s a small error. If you predict something with high confidence and you’re wrong, that’s a large error. Again, a score of 0 is perfect, 1 is the worst. A naive model that always predicts 50% for Over would score 0.25, anything below that indicates the model is better than random guessing.
* **Calibration:** This answers the essential question: when the model says an event has a 40% chance of happening, does it actually occur about 40% of the time? The Expected Calibration Error (ECE) measures the average gap between predicted probabilities and actual frequencies. An ECE of 0.03 means that, on average, when the model predicts a certain probability, the actual outcome frequency is within 3 percentage points of that prediction. So if it predicts 40%, you can expect the true rate to be somewhere between 37-43%. For probability-based betting decisions, good calibration is essential—it ensures that any apparent edge against market odds is grounded in reality.

### Performance Against Market Odds

#### 1X2 Market Performance

In the three-outcome match result market, the model posts a log loss of **1.0587**. While the market´s log loss is **1.0342**. Both the model and the market sit below the naive baseline of approximately 1.099, which tells us that both predictions are, on average, more accurate than random chance. The market's score is lower than the model's, and here's why: log loss rewards confident correct predictions. The 1X2 market is dominated by bets on favorites, people put their money on the team expected to win. When those favorites actually win, which they do more often than not, the market's confident predictions are rewarded with a lower log loss. The market is essentially being penalized less because it's confidently backing the outcomes that happen most frequently.

![Alt text](assets/log_loss_1x2.png)

The model's Ranked Probability Score is  **0.2148**. Remember that a naive 33% guess would score around 0.22. The model comes in slightly below that, meaning it's making reasonably ordered predictions. It generally knows that when it assigns higher probability to a home win, home wins tend to happen more often, and it respects the natural ordering of outcomes. The market's RPS of **0.2079** is again lower, following the same pattern: the market's confidence on favorites gives it a small edge in this metric as well.

![Alt text](assets/rps_1x2.png)

Now, the calibration story. The model's Expected Calibration Error for 1X2 is **0.0292.** This means that when you look across all matches where the model predicted, say, a 40% chance of a home win, the actual frequency of home wins in those matches lands within about **2.92%** of that 40% figure. The model's probability scale is trustworthy, a 60% chance really means something much closer to a 60% long-run frequency than a 50% or 70% frequency.

The market's ECE is **0.0391**. This is higher. This difference matters because log loss and RPS measure something slightly different from calibration. Log loss cares about both the ranking of predictions and the sharpness of confidence. It rewards you for being confidently correct. The market gets that reward regularly by backing favorites. But calibration asks a different question: when you say something has X% probability, does it actually happen X% of the time? Here, the model outperforms the market. Its probabilities are more reliable as statements of true likelihood, even if the market edges it out on picking the exact winner in individual matches.

For a value-based trading strategy, this combination is actually ideal. The model's probabilities are trustworthy, when it identifies a divergence from market odds, that divergence rests on probability estimates that have proven themselves accurate across hundreds of matches. The market may be slightly sharper at calling the winner, but the model is better at telling you what the true odds really are.

![Alt text](assets/1x2_model_cal.png)

![Alt text](assets/1x2_market_cal.png)

#### Over/Under 2.5 Goals Performance

In the binary Over/Under market, the model's log loss is **0.6789**. The naive baseline here, always predicting 50%, is 0.693. The model comes in below that, meaning it's making genuinely informative predictions about whether matches will exceed 2.5 goals. The market's log loss is **0.6820**, which is also below the baseline but slightly higher than the model's. Here, the model actually has the edge in log loss, meaning its confident predictions are slightly more accurate on average.

![Alt text](assets/log_loss_ou.png)

The Brier Score tells a similar story. The model scores  **0.2424** , below the 0.25 naive benchmark. The market scores **0.2442**. Both are slighlty better than random guessing.

![Alt text](assets/bs_ou.png)

For calibration in this market, the model's ECE is **0.0349** and the market's is **0.0392**. The pattern holds: the model's probability estimates align more closely with actual outcome frequencies. When the model says there's a 70% chance of Over 2.5 goals, that event occurs at a rate closer to 70% than the market's equivalent predictions do.

![Alt text](assets/ou_model_cal.png)

![Alt text](assets/ou_market_cal.png)

## Profitability

The transition from *accurate probabilities* to *profitable trading* is where most models fail. A model can be statistically sound but still lose money if it cannot overcome the *vig* (bookmaker margin) or if its staking strategy is poorly aligned with its edge.

To test the model's financial viability, I ran a comprehensive backtest starting with a  **$10,000 initial bankroll**. The goal was to see if the divergence between the model's reality and the market's pricing was large enough to generate meaningful returns after **5,000+ bets** in approximately 6 months.

To manage size, I used **Kelly Criterion,** which is a formula used to determine the optimal size of a series of bets to maximize growth while minimizing risk, and added a custom **Confidence Multiplier** based on the data the model had on the game.

To ensure this simulated growth reflected real-world constraints, two critical rules were enforced:

* **Liquidity Cap:** I capped the effective bankroll used in the Kelly calculation at $10,000, even if account equity grew higher. This simulates the real-world limitation of market liquidity, where large wagers cannot be placed without moving the odds.
* **Delayed P&L Realization:** Profit and loss were only realized *after* the game concluded. This simulates the lack of instant liquidity; I could not reinvest money that was tied up in active bets until those funds were actually returned.

### The Growth Curve

Looking at the bankroll's progression, the model generated a Total Profit of **$129k**, resulting in a final balance of **$139k**. Representing a **Bankroll Growth of 1,294%.**

The progression of the bankroll reveals three distinct phases of market interaction:

1. **The Initial Spike:** The model immediately surged from $10k to around **$40k**. This early success was driven by a high-concentration period where several high-EV bets landed simultaneously.
2. **The 92.68% Drawdown:**  This is the most critical stress test in the data. A *drawdown* measures the peak-to-trough decline in a bankroll. Here, the balance plummeted from its $40k peak back down to approximately  **$3k**, falling well below the initial starting capital. This illustrates the high volatility of the strategy and the necessity of the 1/7th Kelly buffer to survive such a collapse without reaching total ruin.
3. **Linear Growth and Volume:** Following the drawdown, the bankroll entered a disciplined, sustained upward trajectory.

![Alt text](assets/balance_ot.png)

### Key Performance Metrics
