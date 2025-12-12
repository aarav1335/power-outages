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

### Columns Kept

I selected outage-related, impact-related, and contextual variables such as:

- YEAR, MONTH, U.S._STATE
- OUTAGE.START.DATE, OUTAGE.START.TIME
- OUTAGE.RESTORATION.DATE, OUTAGE.RESTORATION.TIME
- OUTAGE.DURATION, CAUSE.CATEGORY
- CUSTOMERS.AFFECTED, DEMAND.LOSS.MW
- POPULATION, CLIMATE.REGION, and related demographic fields

These columns directly support analyzing outage duration and the factors associated with it.

### Cleaning Steps Performed

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
    src="tables/df_head.html" 
    width="100%" 
    height="350" 
    style="border: 1px solid #ccc; border-radius: 4px;"
></iframe>