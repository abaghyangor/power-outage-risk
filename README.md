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
