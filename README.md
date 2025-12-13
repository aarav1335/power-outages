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
    width="100%" 
    height="100%" 
    frameborder="0"
></iframe>

### Univariate Analysis
To better understand the structure of the dataset, I examined the distributions of key variables relevant to predicting outage duration. This included exploring both numerical and categorical columns, looking for skew, outliers, and general trends that might inform later modeling decisions.

<iframe 
    src="assets/uni.html"
    width="100%" 
    height="100%" 
    frameborder="0"
></iframe>

The distribution of OUTAGE.DURATION is heavily right-skewed. Most outages last under a few thousand minutes, but a small number of events extend for tens of thousands of minutes, creating extreme long-tail behavior.
This skew suggests that transformations (e.g., log-scaling) might improve model performance later.

### Bivariate Analysis
Continuing Exploratory Data Analysis, I examined the bivariate distributions of key variables, specifically ones that are relevant to prediction of outage duration.

<iframe 
    src="assets/bi.html"
    width="100%" 
    height="100%" 
    frameborder="0"
></iframe>

This box plot shows how outage durations vary across different causes of power outages. It suggests that fuel supply emergencies and equipment failures tend to produce the longest outages, with median durations higher than other categories and several extreme long-duration events. In contrast, system operability disruptions, intentional attacks, and islanding outages are generally short, with low spread and fewer extreme values.

Severe weather displays a wide range — while many outages are short, several storms cause very long disruptions, reflecting their unpredictable impact. Overall, this visualization suggests that infrastructure-related issues (like equipment failure and fuel supply problems) lead to more prolonged outages, whereas operational or intentional disruptions tend to be contained more quickly.

### Interesting Aggregates
Now, I aggreagate the mean duration of outages based on different combinations of climate regions and cause category.

<iframe 
    src="assets/table.html"
    width="100%" 
    height="100%" 
    frameborder="0"
></iframe>

Across climate regions, outage durations vary widely by cause. Fuel supply emergencies consistently lead to the longest outages, often far exceeding other categories, while equipment failures also produce long disruptions in certain regions. In contrast, causes like intentional attacks, islanding, and public appeal tend to result in much shorter outages overall. Severe weather shows moderate but variable durations depending on the region. These patterns suggest that outages linked to infrastructure or supply chain issues are significantly more time-intensive to resolve than those caused by human or operational factors.