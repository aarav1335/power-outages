# Analysis of Power Outages Data
## Introduction

Electricy has become a central and critical part of life as society has modernized. Therefore, power outages and the reliability of this electricy have become more detrimental to our life as they disrupt transportation, education, and basic daily activities for thousands of people at once. For these important sectors, it is becoming increasingly important to be able to assess the extent of the outage in order to allocate resources and communicate in time. 

In this project, I analyze a dataset of large power outages in the United States to better understand what factors are associated with longer outages and to build a model that predicts outage duration.

## Dataset

The dataset contains records of major power outages in the continental U.S. from January 2000 to July 2016. Each row represents a single outage event, and the columns describe:

### Outage Attributes

-   `OUTAGE.DURATION` — total minutes without power (this is the **target variable**)    
-   `CUSTOMERS.AFFECTED`
-   `DEMAND.LOSS.MW` - total demand lost at the peak of outage (in MegaWatts)
    

### Location Information

-   `U.S._STATE`
-   `POSTAL.CODE`
-   `NERC.REGION`
-   `CLIMATE.REGION`
    
### Cause Information

-   `CAUSE.CATEGORY` (e.g., Severe Storm, Equipment Failure, Intentional Attack)
-   `CAUSE.CATEGORY.DETAIL`
-   `HURRICANE.NAMES` (if applicable)

### **Contextual & Demographic Variables**

-   Population fields (`POPULATION`, `POPDEN_URBAN`, etc.)    
-   Economic indicators (`PC.REALGSP.STATE`, `PC.REALGSP.CHANGE`)


Central Question

The guiding question for this project is:

Can we predict the duration of a power outage using information available at or near the time the outage begins (such as cause, location, and number of customers affected)?

This naturally leads to treating the problem as a regression task, where the goal is to model OUTAGE.DURATION using relevant features from the dataset.

Why This Question Matters

Understanding how long outages last — and predicting those durations — is important for several reasons:

Customer communication: Utilities can provide more realistic repair-time estimates.

Resource allocation: Helps determine which outages require more urgent attention from repair crews.

Infrastructure planning: Identifying patterns in long outages can highlight regions or conditions that need investment.

Equity: Some outages may disproportionately impact specific groups, raising fairness considerations about resilience and infrastructure quality.