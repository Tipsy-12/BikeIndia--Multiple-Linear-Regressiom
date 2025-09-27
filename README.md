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

**Counts often violate linear regression assumptions:**

Normally, count data are small integers (0, 1, 2, …). This causes two issues:
- Non-normal errors → residuals are skewed, violating the normality assumption.
- Heteroskedasticity → variance increases with the mean, so constant variance of errors doesn’t hold.
That’s why counts are usually modelled with Poisson-type regressions.

**Why Poisson regression may fail here:**
The Poisson model assumes that mean = variance. But in our dataset, variance (~3.7M) is orders of magnitude larger than the mean (~4500) — this is called overdispersion. When overdispersion is this severe, Poisson regression underestimates the true variability in the data, leading to incorrect standard errors and misleading inferences.

**Why linear regression is fine for our bike demand data**:

1. At its core, our daily bike count, cnt, is the sum of thousands of individual, independent decisions made by people throughout the day.

cnt = (Decision of person 1) + (Decision of person 2) + ... + (Decision of person N)

2. The CLT states that when you sum up a large number of independent random variables, their sum will be approximately normally distributed, regardless of the original distribution of the individual variables.

3. By CLT, when the average count is very large, the distribution of the counts becomes approximately normal. Our mean count is over 4500. A distribution with such a high average is:
- Effectively Continuous: The difference between 4500 and 4501 is negligible, so the discrete nature of the data is no longer a practical issue.
- Symmetric: Any potential skewness you'd find in a count distribution with a low mean disappears. The distribution will be almost perfectly symmetric and bell-shaped, which aligns with the assumptions of linear regression.

**Normal Regression Easily Handles Overdispersion**
Unlike the Poisson model, a normal distribution does not assume the mean equals the variance. It has a separate, independent parameter for variance. This means it can model the high variance in your data without any issue, accurately capturing the data's true spread.


**One modelling view (Binomial):**  
Each ride decision can be thought of as a Bernoulli trial (ride or no ride). If we imagine summing over all potential riders, then the daily total is like a Binomial random variable. For large numbers (e.g. ~4500 average rides per day, max ~8714), the Binomial is very well-approximated by a Normal distribution.


**Takeaway:**
Our bike sharing demand is best handled with linear regression, since the large counts mitigate the usual problems of treating counts as continuous. Poisson regression, while theoretically suited for count data, breaks down here due to extreme overdispersion. If we want a count model that respects overdispersion, negative binomial regression would be the alternative.
