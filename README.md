# Analysis of Power Outages Data
## Introduction

Electricy has become a central and critical part of life as society has modernized. Therefore, power outages and the reliability of this electricy have become more detrimental to our life as they disrupt transportation, education, and basic daily activities for thousands of people at once. For these important sectors, it is becoming increasingly important to be able to assess the extent of the outage in order to allocate resources and communicate in time. 

In this project, I analyze a dataset of large power outages in the United States to better understand what factors are associated with longer outages and to build a model that predicts outage duration.

### Dataset

The dataset contains records of major power outages in the continental U.S. from January 2000 to July 2016. Each row represents a single outage event, and the columns describe:

#### Outage Attributes

-   OUTAGE.DURATION - total minutes without power (this is the **target variable**)    
-   CUSTOMERS.AFFECTED
-   DEMAND.LOSS.MW - total demand lost at the peak of outage (in MegaWatts)
    

#### Location Information

-   U.S._STATE
-   POSTAL.CODE
-   CLIMATE.REGION
    
#### Cause Information

-   CAUSE.CATEGORY (e.g., Severe Weather, Equipment Failure, Intentional Attack)
-   CAUSE.CATEGORY.DETAIL - not used for analysis
-   HURRICANE.NAMES (if applicable)

#### Population Information

-   POPDEN_URBAN - Population density of the urban areas (persons per square mile)

### Central Question

Can we predict the duration of a power outage using information available at or near the time the outage begins (such as cause, location, and number of customers affected)?

### Why This Question Matters

Understanding how long outages last — and predicting those durations — is important for several reasons:

 - Customer communication: Utilities can provide more realistic repair-time estimates.

 - Resource allocation: Helps determine which outages require more urgent attention from repair crews.

 - Infrastructure planning: Identifying patterns in long outages can highlight regions or conditions that need investment.

 - Equity: Some outages may disproportionately impact specific groups, raising fairness considerations about resilience and infrastructure quality.

## Data Cleaning and Exploratory Data Analysis

To prepare the dataset for analysis and modeling, I performed several essential cleaning steps to standardize formats, remove invalid entries, and keep only relevant variables.

### Data Cleaning
#### Columns Kept

I selected outage-related, impact-related, and contextual variables such as:

- YEAR, MONTH, U.S._STATE
- OUTAGE.START.DATE, OUTAGE.START.TIME
- OUTAGE.RESTORATION.DATE, OUTAGE.RESTORATION.TIME
- OUTAGE.DURATION, CAUSE.CATEGORY
- CUSTOMERS.AFFECTED, DEMAND.LOSS.MW
- POPULATION, CLIMATE.REGION, and related demographic fields

These columns directly support analyzing outage duration and the factors associated with it.

#### Cleaning Steps Performed

1. Filtered to relevant columns
I created a cleaned DataFrame containing only the selected variables.

2. Converted OUTAGE.DURATION to numeric
This ensures durations can be analyzed and modeled properly. Invalid values were converted to NaN.

3. Standardized CAUSE.CATEGORY
All category values were converted to lowercase for consistency.

4. Converted time columns to timestamps
OUTAGE.START.TIME was converted to a Timestamp object for potential feature extraction later.

5. Removed non-positive outage durations
Rows where OUTAGE.DURATION ≤ 0 were dropped since they do not represent valid outages.

The dataset (first five rows) looks like this after cleaning: 
<iframe 
    src="assets/df_head.html" 
    width="600" 
    height="250" 
    frameborder="0"
></iframe>

### Univariate Analysis
To better understand the structure of the dataset, I examined the distributions of key variables relevant to predicting outage duration. This included exploring both numerical and categorical columns, looking for skew, outliers, and general trends that might inform later modeling decisions.

<iframe 
    src="assets/uni.html"
    width="600" 
    height="400" 
    frameborder="0"
></iframe>

The distribution of OUTAGE.DURATION is heavily right-skewed. Most outages last under a few thousand minutes, but a small number of events extend for tens of thousands of minutes, creating extreme long-tail behavior.
This skew suggests that transformations (e.g., log-scaling) might improve model performance later.

### Bivariate Analysis
Continuing Exploratory Data Analysis, I examined the bivariate distributions of key variables, specifically ones that are relevant to prediction of outage duration.

<iframe 
    src="assets/bi.html"
    width="600" 
    height="400" 
    frameborder="0"
></iframe>

This box plot shows how outage durations vary across different causes of power outages. It suggests that fuel supply emergencies and equipment failures tend to produce the longest outages, with median durations higher than other categories and several extreme long-duration events. In contrast, system operability disruptions, intentional attacks, and islanding outages are generally short, with low spread and fewer extreme values.

Severe weather displays a wide range — while many outages are short, several storms cause very long disruptions, reflecting their unpredictable impact. Overall, this visualization suggests that infrastructure-related issues (like equipment failure and fuel supply problems) lead to more prolonged outages, whereas operational or intentional disruptions tend to be contained more quickly.

### Interesting Aggregates
Now, I aggreagate the mean duration of outages based on different combinations of climate regions and cause category.

<iframe 
    src="assets/table.html"
    width="800" 
    height="350" 
    frameborder="0"
></iframe>

Across climate regions, outage durations vary widely by cause. Fuel supply emergencies consistently lead to the longest outages, often far exceeding other categories, while equipment failures also produce long disruptions in certain regions. In contrast, causes like intentional attacks, islanding, and public appeal tend to result in much shorter outages overall. Severe weather shows moderate but variable durations depending on the region. These patterns suggest that outages linked to infrastructure or supply chain issues are significantly more time-intensive to resolve than those caused by human or operational factors.

## Assessment of Missingness

### NMAR dependancy

One column in the dataset that may be Not Missing At Random (NMAR) is HURRICANE.NAMES. These values are missing not because of properties of the outage itself, but because most outages simply did not involve a hurricane. In other words, the reason the value is missing is inherent to the value that would have been recorded—if no hurricane occurred, there is no hurricane name to report. This means the missingness depends on the unobserved true value (“no hurricane”), which fits the definition of NMAR. Because this missingness is tied to the underlying data-generating process rather than other observed columns, it is appropriate to treat HURRICANE.NAMES as NMAR and avoid attempting to impute or use it in analyses that assume randomness in missingness.

### Missingness Dependency Analysis

To investigate whether missing values in DEMAND.LOSS.MW depend on other variables in the dataset, I performed permutation tests comparing the mean of a second variable between rows where DEMAND.LOSS.MW is missing and not missing.

#### Dependency on OUTAGE.DURATION

The first test examined whether missingness in DEMAND.LOSS.MW depends on OUTAGE.DURATION. The observed difference in mean outage duration between missing and non-missing groups was 91.76. The permutation test produced a p-value of approximately 0.386, meaning that differences this large or larger occur frequently under the null hypothesis of independence.

<iframe 
    src="assets/missing1.html"
    width="600" 
    height="400" 
    frameborder="0"
></iframe>

This shows that there is no statistical evidence that missingness in DEMAND.LOSS.MW depends on outage duration. The position of the observed statistic near the center of the null distribution supports the conclusion that this relationship is likely not meaningful.

#### Dependency on YEAR

Next, I tested whether missingness in DEMAND.LOSS.MW depends on YEAR, since missing data may occur more frequently in earlier years depending on how and when data collection occured. The observed difference in mean YEAR between missing and non-missing rows was 2.102, which is a large positive differece suggesting that missing DEMAND.LOSS.MW values tend to occur in later years (Since the order of subtraction was missing - not missing). The permutation test yielded a p-value of 0.0, indicating that such a large difference almost never occurs under the null hypothesis.

<iframe 
    src="assets/missing2.html"
    width="600" 
    height="400" 
    frameborder="0"
></iframe>

This suggests that missingness in DEMAND.LOSS.MW is strongly dependent on the YEAR of the outage. This suggests that more recent records are more likely to have missing demand-loss information, likely due to incomplete data collection practices in the more recent years. This pattern is consistent with Missing At Random (MAR) - where the missingness depends on another observed variable (YEAR), not on DEMAND.LOSS.MW itself.

## Hypothesis Testing
### Question
Do Severe Weather Outages Last Longer? Since most extreme outages occur when storms disrupt the distribution of electicity (by damaging electrical lines, etc.), I am trying to understand if our data follows this trend or not.

To investigate whether severe weather events lead to longer power outages, I performed a permutation test comparing the mean outage duration for severe weather outages versus all other causes.

### Hypotheses

- Null Hypothesis (H₀):
The mean outage duration for severe weather outages is the same as for non–severe-weather outages. Any observed difference is due to random chance.
- Alternative Hypothesis (H₁):
Severe weather outages have longer mean outage durations than non-severe-weather outages.

### Test Statistic and Significance level

The difference in mean OUTAGE.DURATION: Mean Duration of Storm-caused Outages - Mean Duration of all other outages. For my permutation test, I am going to use the significance level of 0.05.

### Results

- Observed difference: 2399.86 minutes (Extremely large - 40 hour difference in the duration of outages)
- p-value: 0.0

### Conclusion

At my chosen significance level (α = 0.05), we reject the null hypothesis.
There is strong statistical evidence that Severe weather (storm-related) outages tend to last significantly longer than outages caused by other factors. This result makes practical sense — storms often damage physical infrastructure (e.g., power lines, transformers), which requires more time to repair compared to causes like equipment failure or operational disruption.

## Framing a Prediction Problem

The column I am planning to try and predict is 'OUTAGE.DURATION' (or the duration of an outage in minutes). This is a regression problem since our target variable is continuous. At the time of prediction, I would try and use the cause of the outage, location, time of year, and some other variables that indicate severity e.g, customers affected and the hour that the outage started. From these potential columns, categorical columns such as cause might be used with One-hot encoding in order to make regression possible. Predictions for outage durations are incredibly useful to power companies and governments as it allows for better communication to the affected population as well as better allocation of resources. 

For evaluating my regression model, I will use R² (coefficient of determination) as my primary test statistic. R² is appropriate here because it measures how much of the variation in outage duration my model can explain, making it easy to compare model performance against simple baselines. Additionally, I may reference RMSE (root mean squared error) to understand the typical magnitude of prediction errors in minutes.

## Baseline Model

For my baseline model, I built a simple linear regression model using 4 features that would realistically be known at the start of an outage: CUSTOMERS.AFFECTED (a quantitative feature), CAUSE.CATEGORY (a nominal categorical feature), POPDEN_URBAN (the urban population density of where the outage occured), 'HOUR' (the hour of day when the outage started). The numerical feature was left unchanged, while the categorical feature was encoded using one-hot encoding so that it could be used in a regression model. All preprocessing steps and the model itself were implemented together in a single sklearn Pipeline, ensuring that the same transformations are applied consistently during both training and testing.

After fitting the baseline model, the performance was evaluated using R², my chosen test statistic for regression tasks. The baseline model achieved an R² of 0.22 on the training set and 0.15 on the test set, indicating that the model explains very little of the variation in outage duration and does not generalize well to unseen data. This is expected for a baseline model, as it gives me a minimal benchmark rather than aiming for high performance. While the model is not “good” in a predictive sense, it is useful as a starting point for comparison when developing more complex models later.

## Final Model

For my final model, I aimed to improve upon the performance of my baseline linear regression model by incorporating additional feature engineering and a more flexible learning algorithm. I chose a Random Forest Regressor, since tree-based methods can naturally model nonlinear relationships and interactions, which are common in outage behavior.

### Features Added & Rationale

In addition to the categorical encodings used in the baseline model, I engineered two new quantitative transformations:

- Square-root transformation of CUSTOMERS.AFFECTED: This reduces the influence of extreme outliers (very large outages), helping the model better capture typical outage behavior. This transformation was chosen based on their distribution - which showcased any possible non-linear relation between CUSTOMERS.AFFECTED and OUTAGE.DURATION.
- Quantile transformation of POPDEN_URBAN: Urban population density is heavily skewed, so mapping it to a more uniform distribution helps tree splits behave more consistently across values. This transformation was chosen based on it's ability to counter any skew seen in the distribution.

### Modeling Algorithm & Hyperparameter Search

I used a RandomForestRegressor inside a single sklearn Pipeline with the preprocessing described above. Before tuning, my model performed with an R² of 0.87 for training data and -0.14 for testing data (suggesting overfitting).
To improve performance by tuning my hyperparameters, I performed a GridSearchCV over the following: n_estimators, max_depth min_samples_split, max_features. The grid search used 5-fold cross-validation and selected parameters that maximized R².

### Final Performance

After tuning, the model achieved:
- Train R²: 0.5079
- Test R²: 0.2269

This performance represents a clear improvement over the baseline model and improves substantially over the untuned Random Forest, which originally yielded a negative test R². Although the model still struggles to generalize strongly, which is likely because outage duration is highly noisy and influenced by unobserved factors, hyperparameter tuning successfully reduced overfitting and improved predictive accuracy on unseen data. The tuned Random Forest is therefore a more reliable predictor than the baseline.

## Fairness Analysis

For my fairness analysis, I examined whether my final model performs differently for outages caused by severe weather compared to outages caused by non-severe weather. This choice is meaningful because storm-related outages tend to be longer and more variable, which may cause prediction models to systematically perform worse for this group.

- Group X: Outages where CAUSE.CATEGORY = "severe weather"
- Group Y: Outages with all other causes

I evaluated fairness using RMSE (root mean squared error), since this metric reflects how large the model's prediction errors are in the original units (minutes).

### Null and Alternative Hypotheses

- Null Hypothesis: The model performs equally for the two groups; the difference in RMSE between severe-weather (storm) and non-severe-weather outages is due to chance. (Order: Storm - Non-storm)
- Alternate Hypothesis: The model performs differently between the groups; the RMSE difference is larger than we would expect by chance.

### Results and Conclusion

After performing a permutation test with 10000 repititions of shuffling the labels and setting the significance level at 0.05: 

- Observed Test Stat: 226.79 minutes - suggesting the model performs worse on storm-caused outages (that group has a higher RMSE)
- P-value: 0.5824 - This suggests that our observed stat is really likely to occur under the null hypothesis.

Because the p-value (0.58) is far above our, we fail to reject the null hypothesis. This means there is no statistically significant evidence that the model performs differently for severe-weather outages compared to non-severe-weather outages.
Although the observed RMSE is somewhat higher for severe-weather outages, this difference is well within what could occur by random chance. Overall, the model likely remains fair to both groups as demonstrated through the fairness analysis.
