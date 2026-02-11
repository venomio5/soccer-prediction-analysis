# ⚽ Soccer Prediction

## Why

One of the main reasons I started with this porject was with a financial incentive, which is haivng an edge on the sports trading world.

And to accomlpish that I created an algorthimic model that gets the truest probabilisties for a match for then to be used as odds and trade.

For **betting, trading, and risk management**, the goal is to **make profitable, rational decisions when the market is wrong**. It’s not about predicting every result correctly, but about consistently identifying when the bookmakers' odds (the market price) don't reflect your more accurate assessment of the true chance. When you have a superior probability model, you can:

* **Find "Value Bets"**: Place bets only when your calculated probability suggests the odds are in your favor long-term.
* **Act Like a Market Maker**: Professional bettors and trading firms use these models to set their own accurate odds, trade positions, and manage risk like a financial instrument.

On a fundamental level, this pursuit is about **separating the signal from the noise** to see the sport's mathematical skeleton. It helps answer:

* **What truly drives wins?** Is it possession, shot quality, or defensive pressure? A robust model quantifies the actual impact of these factors.
* **How much of soccer is really skill vs. luck?** By measuring the predictability of outcomes, you understand the sport's inherent chaos.
* **What is a team's "true" strength?** It provides a stable rating, less swayed by random, lucky wins or unfortunate losses.

Ultimately, the **"why"** is about **mastery**. It’s the desire to see the hidden game within the game, make superior decisions under uncertainty, and gain a tangible edge—whether that edge is measured in profit, trophies, or pure understanding.

## Results

### For xG

- MAE
- RMSE
- R2 Score

#### HOW TO INTERPRET THE METRICS

MAE (Mean Absolute Error):

- The average absolute difference between predicted xG and actual goals
- Example: MAE of 0.35 means predictions are off by 0.35 goals on average
- Lower is better

RMSE (Root Mean Square Error):

- Similar to MAE but gives more weight to larger errors
- Example: RMSE of 0.45 means typical error magnitude
- Lower is better, always higher than or equal to MAE

R² Score (Coefficient of Determination):

- Measures how well predictions explain variance in actual goals
- Range: Can be negative (worse than predicting the mean) to 1.0 (perfect)
- Example: R² of 0.15 means 15% of goal variance is explained by your model
- Higher is better

### For probability

- RPS
- Log Loss
- Calibration

#### HOW TO INTERPRET THE RESULTS

LOG LOSS (Cross-Entropy):

- Measures accuracy of probability predictions
- Range: 0 (perfect) to infinity
- Good football models: 0.85-1.05
- Excellent models: <0.85
- Penalizes confident wrong predictions heavily

RANKED PROBABILITY SCORE (RPS):

- Accounts for ordinal nature of outcomes (Home > Draw > Away)
- Range: 0 (perfect) to 1 (worst)
- Good football models: 0.15-0.25
- Excellent models: <0.15
- Lower is always better

CALIBRATION:

- Perfect calibration: predicted probability = actual frequency
- ECE < 0.02: Excellent calibration
- ECE 0.02-0.05: Good calibration
- ECE > 0.05: Needs improvement
- Well-calibrated models make probability bets profitable

BRIER SCORE:

- Mean squared error of probabilities
- Range: 0 (perfect) to 1 (worst)
- Good football models: 0.15-0.25
- Lower is better

KEY INSIGHTS:

1. A model can have good log loss but poor calibration
2. RPS is better for football than accuracy alone
3. Calibration is crucial for making probability-based decisions

### For profitability

- ROI
