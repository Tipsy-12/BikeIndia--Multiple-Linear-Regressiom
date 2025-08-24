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

### Counts often violate linear regression assumptions:

Normally, count data are small integers (0, 1, 2, …). This causes two issues:
- Non-normal errors → residuals are skewed, violating the normality assumption.
- Heteroskedasticity → variance increases with the mean, so constant variance of errors doesn’t hold.
That’s why counts are usually modelled with Poisson-type regressions.

### Why linear regression is fine for our bike demand data:
In our case, counts are large (22 to 8714, mean ≈ 4500). At this scale, counts behave almost like continuous data, and by the Central Limit Theorem, the errors are close to normal. This makes the linear regression assumptions much less problematic. Even if variance grows with demand, OLS is fairly robust here, and predictions remain interpretable and practical. 

The daily total count is not just one “event,” but the sum of thousands of individual ride decisions made by users.  
The CLT says: if you take the sample mean of n independent draws from some distribution with finite mean/variance, then as n→∞, the distribution of that sample mean tends to normal. In our case, each day’s bike demand is like one draw from the (unknown) population distribution of daily demand. Since we actually have 731 days of observations, the sample size is large enough for CLT to apply.


\[
\sum_{i=1}^{n} X_i = n \bar{X}.
\]

After centring and scaling, the normalised sum converges in distribution to a standard Normal:  

\[
\frac{\sum_{i=1}^{n} X_i - n\mu}{\sqrt{n}\sigma} \;\xrightarrow{d}\; N(0,1).
\]




**One modelling view (Binomial):**  
Each ride decision can be thought of as a Bernoulli trial (ride or no ride). If we imagine summing over all potential riders, then the daily total is like a Binomial random variable. For large numbers (e.g. ~4500 average rides per day, max ~8714), the Binomial is very well-approximated by a Normal distribution.


### Why Poisson regression may fail here:
The Poisson model assumes that mean = variance. But in our dataset, variance (~3.7M) is orders of magnitude larger than the mean (~4500) — this is called overdispersion. When overdispersion is this severe, Poisson regression underestimates uncertainty, produces biased standard errors, and can lead to misleading inferences.

### Takeaway:
Our bike sharing demand is best handled with linear regression, since the large counts mitigate the usual problems of treating counts as continuous. Poisson regression, while theoretically suited for count data, breaks down here due to extreme overdispersion. If we want a count model that respects overdispersion, negative binomial regression would be the alternative.
