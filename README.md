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

### Univariate Analysis

<iframe
  src="assets/univariate_analysis1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This chart shows how common each type of attack is, splitting up the data by cause category.

<iframe
  src="assets/univariate_analysis2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This histogram shows the distributions of outage durations. This graph showed us that outages are heavily skewed towards lower durations. As the range of durations goes from almost no time to around 75 days, we encourage you to zoom in on this graph using the controls in the margins!

### Bivariate Analysis

<iframe
  src="assets/bivariate_analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This scatter plot depicts the number of customers affected by an outage proportional to how long the outage lasted. 

### Interesting Aggregates

<iframe
  src="assets/aggregation_analysis1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
These stacked bar graphs shows the distributions of cause category in each climate region in the US. This chart highlights how intentional attacks are much more likely to cause a power outage in the Northwest and Southwest climate regions. 

<iframe
  src="assets/aggregation_analysis2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
This graph shows what proportion of outages were caused by intentional attack in each year from 2000 to 2016. We can observe that intentional attacks were rare from 2000 to 2003 as low percentages of outages were caused by attacks. Then, from 2004 to 2010, we see that there were zero intentional attacks in this dataset. In 2011, however, this number balloons to around 45%, and reaches a maximum of over 55% in 2016. 

---

## Assessment of Missingness

---

## Hypothesis Testing

---

## Framing a Prediction Problem

Based on the exploration above, our immediate question was as follows: _**Based on the data available just moments after a power outage, are we able to predict whether the outage was due to an intentional attack?**_
For us, this boiled down to a Binary Classification Task, we used the four features OUTAGE.START.TIME, MONTH, NERC.REGION, U.S._STATE (representing a spread of time and geographical info). We will predict the response variable IS_INTENTIONAL, a column we engineered from CAUSE.CATEGORY -- evaluating to True if the cause was an intentional attack and False otherwise. We chose this instead of multiclass classification since we were only concerned with predicting the intentional attacks in our research question, and believed that we could get more accurate and generalizable results predicting a binary value, as opposed to all seven of the classes present in CAUSE.CATEGORY. 

To measure the effectiveness of our model, we used accuracy. We chose accuracy because while it was important for us to be precise, we also felt that it was just as important to think about recall, and our ability to generalize. False Negatives are costly in our scenario because it would mean an intentional attack going unnoticed, and perpetrators getting away. Conversely, False Positives are also important, due to their ability to result in a waste of resources, as additional support could be dispatched towards the site of a false intentional attack. These reasons led us to accuracy, which considers both true and false negatives equally. 


---

## Baseline Model

#### Model Description
The model we chose to build on was a Logistic Regression Classifier, predominantly used for binary classification tasks. 
It aims to predict whether a power outage is intentional (IS_INTENTIONAL = 1) or not (IS_INTENTIONAL = 0).

#### Model Parameters
**Solver**: The logistic regression uses the 'saga' solver, which was chosen due to its efficiency in large datasets and support for regularization; an issue we were running into.
**Max Iterations**: The maximum number of iterations is set to 1000 to allow the solver ample opportunity to converge. Previous testing on lower values for this hyperparameter resulted in inconsistent non-convergence.

#### Features & Preprocessing
In this first iteration of our model, we had two quantitative features, (the **month** and **time of day**), along with two categorical features, split into multiple columns after OneHot Encoding, (the **us state** and **NERC regions** as partitioned by the North American Electric Reliability Corporation).

| Feature            | Type              | Preprocessing                                                                                          |
|--------------------|-------------------|--------------------------------------------------------------------------------------------------------|
| NERC.REGION        | Categorical/Nominal | One hot encoding for all unique values, dropping the first encoded column to avoid multicollinearity. |
| U.S._STATE         | Categorical/Nominal | One hot encoding for all unique values, dropping the first encoded column to avoid multicollinearity. |
| MONTH              | Quantitative       | Standard scaled to address non-standard scale issues for Logistic Regression.                         |
| OUTAGE.START.TIME  | Numerical         | Transformed into a 24-hour format integer scale representing the hour of the day the outage started. Custom transformation using a FunctionTransformer.   |

#### Base Model Assessment
**Model Performance**
_Training Accuracy: ~0.8139
Testing  Accuracy: ~0.8492_

The model shows strong performance based on our observed accuracy scores, with a score on the testing set that fluctuates very close to the training set, sometimes even scoring higher. This indicates effective generalization to unseen data. Going into the future, this generalization is what we are looking for when attempting to determine whether additional specific support should be dispatched to look into a power outage.


## Final Model

---

## Fairness Analysis

---
