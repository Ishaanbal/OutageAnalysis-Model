# Analyzing Intentional Attacks on Electricity Providers

When we think of power outages, we usually associate them with crazy weather patterns or infrastructure failure. However, intentional attacks to our electricity grid are on the rise. These acts of domestic terror often cause serious damage and go unpunished. Therefore, we raise the research question of how to minimize these attacks to avoid Americans getting left in the dark.

Research done as part of the class DSC80 at UCSD. By Ishaan Bal and Lacha Barton-Gluzman

---

## Introduction

Our dataset covers power outages from the years 2000 to 2016 in the US. Power outages due to intentional attacks are on the rise, and we must address them to avoid blackouts. In addition, these systems could be exploited by a malicious actor on a more international scale. In order to avoid power outages and address this vulernability in our national security, we have to get better at stopping attackers from bringing down our power grid. 

In this project, we decided to study power outages caused by intentional attacks to determine how a power company could avoid outages due to factors like vandalism or sabotage.
To explore our research question, we looked at plenty of data from our 1534 row dataset. We considered factors like when the outage happened (YEAR, MONTH), where the outage happened (U.S._STATE, CLIMATE.REGION, NERC.REGION), why the outage happened (CAUSE.CATEGORY, CAUSE.CATEGORY.DETAIL), and the impact of the outage (OUTAGE.DURATION, CUSTOMERS.AFFECTED).

---

## Data Cleaning and Exploratory Data Analysis

To begin working with our data, we had to clean it up through multiple steps that would allow the computer to read the file how we intended.

### Reading and Initial Cleaning
Our analysis began by importing the `outage.xlsx` file, carefully skipping the initial metadata rows to ensure our DataFrame contained only pertinent information. We also eliminated rows and columns that did not contribute to our analysis, like the blank column that said 'variables' and the row specifying the units of each column. 

### Timestamp Conversion
Next, we merged date and time columns into single datetime columns (`OUTAGE.START` and `OUTAGE.END`). This step was crucial for accurately calculating outage durations and understanding their distribution over time.

### Outage Duration Calculation
The calculation of outage durations (`OUTAGE.DUR.DT`) from the `OUTAGE.START` and `OUTAGE.END` timestamps allowed us to get the duration of each outage event in datetime format. This metric was helpful in our exploratory analysis, enabling us to identify patterns and trends in outage occurrences and durations.

Through these steps, our dataset was transformed into a structured and analyzable format, laying the groundwork for insightful exploration and analysis.

---

## Assessment of Missingness

---

## Hypothesis Testing

---

## Framing a Prediction Problem


Based on info available in the first few moments of a power outage, can we perform binary classification for whether the attack is intentional or not? We used the OUTAGE.START.TIME, MONTH, NERC.REGION, U.S._STATE (time and geographical info). We are predicting for the response variable IS_INTENTIONAL that is a column we engineered from CAUSE.CATEGORY evaluating to True if the cause was intentional attack and False otherwise. We chose this instead of multiclass classification since we’re only concerned with predicting the intentional attacks in our research question. To measure this we used accuracy. We chose accuracy because while it was important for us to be precise and accuracy considers precision, we felt that it was more important to think about recall, specifically because a false negative would have much more significant consequences than false positive. 


---

## Baseline Model

---

## Final Model

---

## Fairness Analysis

---
