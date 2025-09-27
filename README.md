# BikeShare

## Introduction
   
- Problem Statement: BikeIndia, a US-based bike-sharing service provider, experienced a significant drop in revenue due to the COVID-19 pandemic. The company aims to forecast the demand for bike-sharing services post-pandemic to optimize its operations and boost profitability.

- Objective: The project seeks to identify the significant factors affecting bike demand and
develop a Multiple Linear Regression model to predict daily bike rentals based on these factors.

- Business Goal: The model will help BikeIndia understand how various factors influence bike
demand, enabling them to adjust their business strategy to meet customer needs and maximize
revenue in a recovering market.

## Data Understanding

Dataset Description: The dataset contains daily bike rental counts along with various
features that may influence demand, such as:

- Datetime: Date and time of the record.
- Season: Categorical variable (Spring, Summer, Fall, Winter).
- Holiday: Binary variable indicating if the day is a holiday.
- Working day: Binary variable indicating if the day is a working day.
- Weather: Categorical variable representing weather conditions.
- Temperature: The temperature in Celsius.
- Humidity: The relative humidity.
- Windspeed: The wind speed in km/h.
- Count: The target variable representing the number of bikes rented.

## Model Choice:

**Why counts often need special treatment:**
Count data are usually small integers (0, 1, 2, …). This makes them problematic for linear regression:

* **Non-normal errors**: residuals are skewed, violating normality.
* **Heteroskedasticity**: variance typically grows with the mean, violating constant variance.
  That’s why count regressions (Poisson, Negative Binomial) are commonly used.

**Why Poisson regression fails here:**
The Poisson model assumes mean = variance. But in bike share demand data, the variance (~3.7M) is orders of magnitude larger than the mean (~4500). This extreme **overdispersion** means Poisson regression underestimates variability and gives misleading inferences.

**Why linear regression is reasonable**

1. Daily bike counts are the sum of thousands of independent ride decisions.

   * cnt = (Decision of person 1) + (Decision of person 2) + ... + (Decision of person N).
2. By the **Central Limit Theorem**, the sum of many independent random variables tends toward normality, regardless of their individual distributions.
3. With a large mean (~4500):

   * The data behave like a continuous variable (discreteness irrelevant).
   * The distribution is nearly symmetric and bell-shaped.
   * Residuals approximate normality well.
4. Unlike Poisson, a normal regression allows the variance ≠ mean, so it can accommodate the large spread in the data.
5. A Binomial interpretation (many riders, each with ride/no ride) further justifies that for large totals, the Normal approximation works extremely well.
6. Each Day is a Random Variable: As we've discussed, each day's bike count (cnt) is a random variable drawn from an underlying distribution. Let's call the true, unknown average of this distribution 'μ'. This 'μ' is the theoretical "true average daily count" if you could measure it over an infinite number of days.
You calculate a Sample Average: You collect data for a certain number of days, let's say 'n'. You can then calculate the average of your observations. Your summary statistics showed the sample average was about 4508 for your dataset of 730 days. The Law of Large Numbers in Action: The WLLN guarantees that as you increase your sample size 'n' (i.e., collect data for more and more days), your calculated sample average will "converge in probability" to the true mean (μ). This means that the probability of your sample average being far away from the true average becomes smaller and smaller as you add more data. Your sample average becomes a more reliable estimate of the true average.

**Important caveats**

* **Heteroskedasticity**: OLS still assumes constant variance conditional on predictors. In practice, bike demand variance may increase with temperature, season, or holidays. Use robust (heteroskedasticity-consistent) standard errors or check residual plots.
* **Temporal dependence**: Counts are serially correlated (yesterday’s rides affect today’s). Pure cross-sectional OLS can misstate uncertainty. Adding lagged terms or time-series corrections (ARIMA errors, GLS) improves inference.
* **Negative predictions**: Linear regression can, in theory, predict negatives. With means >4500, this is rare but worth noting.
* **Alternatives**: If overdispersion and skew were smaller, Negative Binomial regression would be the natural count-data fix. Here, the Normal approximation is both simpler and effective.

**Takeaway**
For bike share demand with large daily counts, **linear regression is a justified and practical choice**. It avoids the breakdown of Poisson under extreme overdispersion, and the CLT ensures approximate normality. Just remember to:

* Use robust SEs to handle heteroskedasticity,
* Consider time dependence,
* Compare predictive accuracy with Negative Binomial or time-series models.
