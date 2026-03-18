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

|   YEAR |   MONTH | U.S._STATE   | NERC.REGION   | CLIMATE.REGION     | CLIMATE.CATEGORY   |   ANOMALY.LEVEL | OUTAGE.START        | OUTAGE.RESTORATION   | CAUSE.CATEGORY     |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   TOTAL.SALES |   TOTAL.CUSTOMERS |   POPULATION |   POPPCT_URBAN |   POPDEN_URBAN |   AREAPCT_URBAN | SEASON     |
|-------:|--------:|:-------------|:--------------|:-------------------|:-------------------|----------------:|:--------------------|:---------------------|:-------------------|------------------:|-----------------:|---------------------:|--------------:|------------------:|-------------:|---------------:|---------------:|----------------:|:-----------|
|   2011 |       7 | Minnesota    | MRO           | East North Central | normal             |            -0.3 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  | severe weather     |              3060 |              nan |                70000 |   6.56252e+06 |           2595696 |      5348119 |         0.7327 |           2279 |          0.0214 | summer     |
|   2014 |       5 | Minnesota    | MRO           | East North Central | normal             |            -0.1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  | intentional attack |                 1 |              nan |                  nan |   5.28423e+06 |           2640737 |      5457125 |         0.7327 |           2279 |          0.0214 | non-summer |
|   2010 |      10 | Minnesota    | MRO           | East North Central | cold               |            -1.5 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  | severe weather     |              3000 |              nan |                70000 |   5.22212e+06 |           2586905 |      5310903 |         0.7327 |           2279 |          0.0214 | non-summer |
|   2012 |       6 | Minnesota    | MRO           | East North Central | normal             |            -0.1 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  | severe weather     |              2550 |              nan |                68200 |   5.78706e+06 |           2606813 |      5380443 |         0.7327 |           2279 |          0.0214 | summer     |
|   2015 |       7 | Minnesota    | MRO           | East North Central | warm               |             1.2 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  | severe weather     |              1740 |              250 |               250000 |   5.97034e+06 |           2673531 |      5489594 |         0.7327 |           2279 |          0.0214 | summer     |

## Univariate Analysis
