**Created by: Gor Abaghyan**

# Introduction

This is a project analyzing a dataset of major power outages in the continental US from January 2000 to July 2016. The dataset was accessed from Purdue University's Laboratory for Advancing Sustainable Critical Infrastracture research data.

The dataset consists of information about major power outages across continental US, which are the rows of the dataset. Besides major outages, this data contains information on geographical location of the outages, regional climatic information, land-use characteristics, electricity consumption patterns and economic characteristics of the states affected by the outages, which are represented through columns of the data.

I'm going to perform Data Cleaning & Exploratory Data Analysis, assess the missingness and dependencies of specific groups within data, perform hypothesis test to analyze whether causes of power outages are dependent on seasons of year ( Summer, Winter, etc.).

Next, I will address the research question: **“Can we accurately predict the duration of major power outages using their contextual and event-related features?”**
This research is significant because predicting outage duration could help governments and utility providers better assess urgency, allocate resources, and restore power more efficiently.

The corresponding DataFrame consists of 1534 rows, each representing a power outage incident, and 57 columns representing the features of each outage. I'll focus solely on specific group of features for the purpose of this research.

| Variable name | Description |
|---|---|---|
| 'YEAR' | Year at which the outage happened |
| 'MONTH' | Month at which the outage happened |
| 'U.S._STATE' | State at which the outage occured |
| 'NERC.REGION' | North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| 'CLIMATE.REGION' | U.S. Climate regions as specified by National Centers for Environmental Information |
| 'CLIMATE.CATEGORY' | Represents the climate episodes corresponding to the years. Categories are based on a threshold of ±0.5°C for the Oceanic Niño Index. |
| 'ANOMALY.LEVEL' | Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season |
| 'OUTAGE.START.DATE' | Day of the year when the outage event started |
| 'OUTAGE.START.TIME' | Time of the day when the outage event started |
| 'OUTAGE.RESTORATION.DATE' | Day of the year when power was restored to all the customers |
| 'OUTAGE.RESTORATION.TIME'	| Time of the day when power was restored to all the customers |
| 'CAUSE.CATEGORY' | Categories of all the events causing the major power outages |
| 'OUTAGE.DURATION' | Duration of outage events (in minutes) |
| 'DEMAND.LOSS.MW' | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
| 'CUSTOMERS.AFFECTED' | Number of customers affected by the power outage event |
| 'TOTAL.SALES' | Total electricity consumption in the U.S. state (megawatt-hour) |
| 'TOTAL.CUSTOMERS' | Annual number of total customers served in the U.S. state |
| 'POPULATION' | Population in the U.S. state in a year |
| 'POPPCT_URBAN' | Percentage of the total population of the U.S. state represented by the urban population (in %) |
| 'POPDEN_URBAN' | Population density of the urban areas (persons per square mile) |
| 'AREAPCT_URBAN' | Percentage of the land area of the U.S. state represented by the land area of the urban areas (in %) |

# Data Cleaning & Exploratory Data Analysis

Below are introduced the steps of data cleaning followed with exploratory data analysis, showing graphical representations of insights from the Dataset.

## Data Cleaning
1. I begin with combining 'OUTAGE.START.DATE' and 'OUTAGE.START.TIME' into a single column called 'OUTAGE.START', similarly for 'OUTAGE.RESTORATION.DATE' and 'OUTAGE.RESTORATION.TIME'. Additionally, I transform them into 'DateTime' variables. Finally, I drop 'OUTAGE.START.DATE', 'OUTAGE.START.TIME', 'OUTAGE.RESTORATION.DATE', 'OUTAGE.RESTORATION.TIME', as they are no longer needed.
2. I keep only the necessary columns for this project, specifically ['YEAR', 'MONTH', 'U.S._STATE','NERC.REGION', 'CLIMATE.REGION', 'CLIMATE.CATEGORY','ANOMALY.LEVEL', 'OUTAGE.START', 'OUTAGE.RESTORATION',
'CAUSE.CATEGORY', 'OUTAGE.DURATION','DEMAND.LOSS.MW', 'CUSTOMERS.AFFECTED', 'TOTAL.SALES', 'TOTAL.CUSTOMERS', 'POPULATION','POPPCT_URBAN','POPDEN_URBAN', 'AREAPCT_URBAN'], dropping other columns.
3. I transform the remaining columns into their correct datatypes.
4. After transforming columns, I examine the number of missing values, and start imputation. Specifically, I used grouped conditional mode imputation by 'U.S._STATE' for categorical columns ['CLIMATE.CATEGORY', 'OUTAGE.START', 'OUTAGE.RESTORATION'], and grouped conditional median imputation by 'CLIMATE.REGION' for numerical columns ['MONTH', 'ANOMALY.LEVEL']. For following columns with low number of missing values ['CLIMATE.REGION', 'TOTAL.SALES'], I use conditional mode & median imputation, respectively.
5. I scale columns representing percentages ['POPPCT_URBAN', 'AREAPCT_URBAN'] to proportions for suitibality with machine learning models later.

Below are the 5 first rows of cleaned dataset with a group of columns selected.

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     | CLIMATE.CATEGORY   |
|-------:|--------:|:-------------|:--------------|:-------------------|:-------------------|
|   2011 |       7 | Minnesota    | MRO           | East North Central | normal             |
|   2014 |       5 | Minnesota    | MRO           | East North Central | normal             |
|   2010 |      10 | Minnesota    | MRO           | East North Central | cold               |
|   2012 |       6 | Minnesota    | MRO           | East North Central | normal             |
|   2015 |       7 | Minnesota    | MRO           | East North Central | warm               |

## Univariate Analysis

In this part of analysis, I have created several graphs to examine the distribution of variables in the dataset, namely distribution of 'OUTAGE.DURATION', 'MONTH', and 'CAUSE.CATEGORY'. Below are presented the graphs of these distributions with concise descriptions.

### Distribution of Outage Duration

I have created a graph showcasing distribution of original outage duration values, and log transformed values side-by-side to demonstrate the right-skeweness of the variable. As we can see in the original graph, vast majority of values are gathered on the rightmost side between 0-5k, while x-axis stretches to 100k with barely any values there. This is significant, as we are going to predict for outage duration, so we will have to log transform it to avoid skeweness.


<iframe
  src="assets/duration_distribution.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


### Distribution of Month

Below is the graph showing distribution of month values, I have created it to examine whether specific months contribute to most of, or higher number of power outages compared to others. As we can see, it appears that during summer season (June, July, August) the number of power outages is noticably higher than during other months, which is essential for our hypothesis test later on.


<iframe
  src="assets/month_distribution.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


### Distribution of Cause Category

This graph represents the distribution of causes of power outages, its importance is in showcasing whether certain causes contribute to the majority of outages. Based on the graph, we can see significant proportion of power outages is caused by severe weather followed with a notable number of outages caused by intentational attacks.


<iframe
  src="assets/cause_category_distribution.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

## Bivariate Analysis

I conducted two main bivariate analyses, both examined through scatter plots demonstrating the relationship between groups of variables.

### Cause Category vs Month

This scatter plot shows the distribution of outage durations (log-transformed) across each month, colored by cause category. Severe weather dominates across all months with generally higher durations, while causes like intentional attacks tend to cluster at lower durations. This suggests that both the time of year and the cause of an outage are meaningful predictors of how long an outage lasts, justifying their inclusion as features in our model.


<iframe
  src="assets/cause_vs_month.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


### Cause Category vs Customers Affected

This scatter plot shows the relationship between the number of customers affected (log-transformed) and outage duration (log-transformed), colored by cause category. Severe weather outages tend to affect more customers and last longer, clustering in the upper right. The spread across cause categories suggests that customers affected and cause type together carry predictive signal for outage duration, making them important features for our regression models.


<iframe
  src="assets/cause_vs_customers.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

## Interesting Aggregates

I have created two main groups to aggregate and examine their statistics for analysis, mainly grouping by Cause Category and NERC Region.

### Cause Category Aggregation

Grouping by cause category reveals that fuel supply emergencies have by far the highest mean duration (13,484 min) despite only 38 occurrences, while severe weather dominates in frequency (741 outages) with a high mean duration of 3,900 min. Intentional attacks are the most frequent cause but resolve relatively quickly (median 92.5 min), suggesting cause type is a strong signal for predicting duration.


| CAUSE.CATEGORY        |   mean_duration |   median_duration |   total_customers |   mean_demand_loss |   outage_count |
|:----------------------|----------------:|------------------:|------------------:|-------------------:|---------------:|
| equipment failure     |        1850.56  |             224   |       3.05807e+06 |           380      |             54 |
| fuel supply emergency |       13484     |            3960   |       1           |           634.174  |             38 |
| intentional attack    |         521.934 |              92.5 |  356315           |            94.0556 |            332 |
| islanding             |         200.545 |              77.5 |  209749           |           441.886  |             44 |
| public appeal         |        1468.45  |             455   |  159994           |          2818.32   |             69 |


### NERC Region Aggregation

Grouping by grid region shows significant geographic variation — ECAR and RFC have the highest mean durations (5,603 and 3,767 min respectively) while PR and islanding-prone regions resolve much faster. ASCC shows NaN for duration despite having customers affected, indicating data quality issues for that region. This variability confirms that grid region is an important feature for our model.


| NERC.REGION   |   mean_duration |   median_duration |   total_customers |   mean_demand_loss |   outage_count |
|:--------------|----------------:|------------------:|------------------:|-------------------:|---------------:|
| ASCC          |         nan     |               nan |   14273           |             35     |              0 |
| ECAR          |        5603.31  |              5475 |       8.20333e+06 |           1314.48  |             32 |
| FRCC          |        4271.12  |              1419 |       1.27502e+07 |           1072.6   |             43 |
| FRCC, SERC    |         372     |               372 |       0           |            nan     |              1 |
| HECO          |         895.333 |               543 |  380186           |            466.667 |              3 |


# Assessment of Missingness

In this section, I am going to analyze the missingness mechanisms and dependencies of specific columns within dataset that had substantial number of missing values, and/or are important for our prediction models.

## MNAR Analysis

I believe DEMAND.LOSS.MW is likely **MNAR (Missing Not At Random)**. The missingness in this column is plausibly related to its own value, utilities may be less likely to report demand loss figures when the loss is either negligibly small or embarrassingly large. This is a data generating process question: the decision to record demand loss depends on the significance of the loss itself, which we cannot observe when it's missing. Additional data that could explain this missingness and potentially make it MAR would be utility company reporting compliance records or NERC filing requirements by region.

## Missigness Dependency

I analyzed the missingness of CUSTOMERS.AFFECTED (655 missing values) using permutation tests. I have found that its missingness does depend on CAUSE.CATEGORY (TVD=0.756, p=0.000) — certain cause types are systematically more likely to have missing customer counts, making this MAR. However, the missingness does not depend on POPPCT_URBAN (difference in means=0.005, p=0.443) — the urban population percentage of a state has no relationship with whether customer data is reported.

To further demonstrate the missigness analysis performed for this section, I have attached two graphs below, one representing the distribution of CAUSE.CATEGORY by CUSTOMERS.AFFECTED missingness, and empirical distribution for both permutation tests of CAUSE.CATEGORY and POPPCT_URBAN side by side.

### Distribution plot (CAUSE.CATEGORY)


<iframe
  src="assets/missingness_cause.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


### Empirical permutation distribution for both tests side by side


<iframe
  src="assets/permutation_tests.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


# Hypothesis Testing

Null Hypothesis: Cause category distribution is independent of season (summer vs. non-summer).
Alternative Hypothesis: Cause category distribution differs between summer and non-summer months.

TVD is the natural choice here since we are comparing two categorical distributions (cause category across seasons), and a 0.03 significance level gives us a slightly stricter threshold than the standard 0.05, reducing the chance of falsely concluding that season affects cause category when it does not.

I ran 10,000 permutations and obtained an observed TVD of 0.1493 with p=0.0000. Since p < 0.03, we reject the null hypothesis — cause category distributions differ significantly between summer and non-summer outages, suggesting season is a meaningful factor in what drives power outages. 

### Distribution of TVD under Null Hypothesis

<iframe
  src="assets/tvd_distribution.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


# Framing a Prediction Problem

Prediction Problem: **Regression — predicting OUTAGE.DURATION** (continuous, in minutes).

Response Variable: OUTAGE.DURATION. Outage duration is one of the most critical aspect of a power outage — knowing how long an outage will last helps utilities allocate repair resources, prioritize response urgency, and inform the public. It also connects directly to my hypothesis testing in Step 4, where I have found that season significantly affects cause category, which in turn affects duration.

Evaluation Metrics: RMSE and MAE. RMSE penalizes large prediction errors more heavily, which is appropriate here since severely underestimating a long outage has real consequences. MAE complements this by being easily interpretable in real units — an MAE of 500 means our predictions are off by 500 minutes on average. I report both since RMSE alone can be dominated by a few extreme outliers given the skewed nature of outage durations.

Features at Time of Prediction: I only use information known at the moment an outage begins — CAUSE.CATEGORY, NERC.REGION, U.S._STATE, CLIMATE.REGION, CLIMATE.CATEGORY, ANOMALY.LEVEL, MONTH, SEASON, TOTAL.CUSTOMERS, POPULATION, POPPCT_URBAN, POPDEN_URBAN, and AREAPCT_URBAN. I explicitly exclude OUTAGE.RESTORATION, OUTAGE.START, DEMAND.LOSS.MW, and CUSTOMERS.AFFECTED as these are only fully known after or during the outage, not at prediction time.

# Baseline Model

Baseline Model: Linear Regression with 10 features — CAUSE.CATEGORY (nominal, one-hot encoded), NERC.REGION (nominal, one-hot encoded), SEASON (nominal, one-hot encoded), CLIMATE.CATEGORY (ordinal, encoded as Cold=0/Normal=1/Warm=2), and six quantitative features (ANOMALY.LEVEL, TOTAL.CUSTOMERS, POPULATION, POPPCT_URBAN, POPDEN_URBAN, AREAPCT_URBAN) standardized with StandardScaler.The target OUTAGE.DURATION was log-transformed before fitting and predictions were inverted with expm1 before computing metrics.

Performance:
Train RMSE: 5,775.78 min, MAE: 2,131.38 min, R^2: 0.4304. 
Test RMSE: 6,878.90 min, MAE: 2,368.16 min, R^2: 0.4157.

I believe this is not entirely a 'good' model. An MAE of ~2,368 minutes (~39 hours) on the test set is too large to be practically useful for predicting outage duration. The R^2 of 0.4157 means the model explains only about 42% of the variance in outage duration on unseen data. The gap between train R^2 (0.4304) and test R^2 (0.4157) is small, suggesting the model is not overfitting — it is simply underfitting, meaning Linear Regression lacks the capacity to capture the non-linear relationships in this data. This motivates upgrading to Random Forest and XGBoost in Step 7, which can model complex interactions between cause, region, and climate conditions without assuming linearity.


# Final Model

New Features Added:

log(POPULATION) and log(TOTAL.CUSTOMERS) — both columns are heavily right-skewed (similar to outage duration itself), so log-transforming them compresses extreme values and makes their relationship with duration more linear and learnable by the model.
ANOMALY.LEVEL^2 — climate anomaly likely has a non-linear effect on outage duration; mild anomalies may have little impact while extreme anomalies cause disproportionately longer outages, so squaring captures this accelerating effect.
MONTH_sin and MONTH_cos (cyclical encoding) — encoding month as two continuous cyclical features preserves the fact that December and January are adjacent seasons, which a raw integer encoding of 1–12 would miss entirely.

Hyperparameters Tuned: max_depth, n_estimators, and min_samples_split for Random Forest; max_depth, n_estimators, and learning_rate for XGBoost. Both were tuned using 5-fold GridSearchCV scored on RMSE.

Final Model: Random Forest with max_depth=10, min_samples_split=5, n_estimators=100.

Performance:

| Model | Test RMSE | Test MAE | Test R^2 |
| Baseline (Linear Regression) | 6,878.90 min | 2,368.16 min | 0.4157 |
| XGBoost | 6,918.50 min | 2,271.28 min | 0.5083 |
**| Random Forest (Final) | 6,593.77 min | 2,143.86 min | 0.5129 |**

Random Forest outperforms both the baseline and XGBoost on all three test metrics. The R^2 improvement from 0.4157 to 0.5129 means the final model explains about 10 percentage points more variance in outage duration on unseen data. The train R^2 of 0.7838 vs test R^2 of 0.5129 indicates some overfitting, but the test performance improvement over baseline confirms the engineered features and hyperparameter tuning meaningfully helped generalization. Note that the large RMSE relative to MAE across all models reflects the influence of a small number of extremely long outages.

A test R^2 of 0.5129 is a meaningful improvement over the baseline but reflects the difficulty of predicting outage duration purely from pre-outage information. Duration is heavily influenced by post-outage factors such as repair crew response time and infrastructure damage extent, which are unavailable at prediction time. Given the dataset size (~1,400 rows) and these constraints, the model captures a reasonable portion of the explainable variance.

<iframe
  src="assets/model_performance.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


# Fairness Analysis 

Groups: High population states (population ≥ 9,309,449) vs Low population states (population < 9,309,449), split at the median.

Evaluation Metric: RMSE — appropriate for regression tasks and directly comparable across groups.

Null Hypothesis: The model is fair. Any difference in RMSE between high and low population states is due to random chance.
Alternative Hypothesis: The model is unfair. It performs significantly differently for high population states compared to low population states.
Test Statistic: |RMSE(High Population) − RMSE(Low Population)| — the absolute difference in RMSE between the two groups.
Significance Level: 0.05

Results:

RMSE (High Population): 8,385.53 min
RMSE (Low Population): 3,627.06 min
Observed |RMSE diff|: 4,758.47 min
P-value: 0.3620

Conclusion: We fail to reject the null hypothesis (p=0.3620 > 0.05). Despite the raw RMSE difference of 4,758 minutes appearing large, the permutation test shows this difference is well within what we'd expect by random chance alone. The model does not appear to be  unfair across population groups. The higher RMSE for high population states likely indicates the presence of more extreme, large-scale outages in densely populated areas rather than a modeling bias.


<iframe
  src="assets/fairness_permutation.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>
