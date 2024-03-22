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

### The Case for CAUSE.CATEGORY.DETAIL Being NMAR

Upon initial analysis, the CAUSE.CATEGORY.DETAIL column's missingness seems to exhibit characteristics of NMAR. This inference is drawn from the observation that detailed cause information is more frequently absent in cases where the outage is of a sensitive nature, such as outages due to cyber-attacks or other security-related issues. This pattern suggests that the details of these outages might be deliberately withheld or not recorded due to their sensitive or controversial nature, making the missingness dependent on the unobserved data itself.

---

## Hypothesis Testing

#### Null Hypothesis (H0):
The distribution of outage durations for intentional attacks is the same as the distribution of outage durations for all other causes.

#### Alternative Hypothesis (H1):
The distribution of outage durations for intentional attacks is different from the distribution of outage durations for all other causes.

_threshold:_ 0.05%

#### Conclusion:
Given that a p-value of 0.2478 is significantly above our alpha level of 0.05, we would fail to reject the null hypothesis. There is not enough evidence to suggest that the distribution of outage durations for intentional attacks is significantly different from that of other causes. The observed difference could likely be due to random variation in the sample data, and no significant effect is detected that could be attributed to the cause being an intentional attack versus other causes.


## Framing a Prediction Problem

Based on the exploration above, our immediate question was as follows: _**Based on the data available just moments after a power outage, are we able to predict whether the outage was due to an intentional attack?**_
For us, this boiled down to a Binary Classification Task, we used the four features OUTAGE.START.TIME, MONTH, NERC.REGION, U.S._STATE (representing a spread of time and geographical info). We will predict the response variable IS_INTENTIONAL, a column we engineered from CAUSE.CATEGORY -- evaluating to True if the cause was an intentional attack and False otherwise. We chose this instead of multiclass classification since we were only concerned with predicting the intentional attacks in our research question, and believed that we could get more accurate and generalizable results predicting a binary value, as opposed to all seven of the classes present in CAUSE.CATEGORY. 

To measure the effectiveness of our model, we used accuracy. We chose accuracy because while it was important for us to be precise, we also felt that it was just as important to think about recall, and our ability to generalize. False Negatives are costly in our scenario because it would mean an intentional attack going unnoticed, and perpetrators getting away. Conversely, False Positives are also important, due to their ability to result in a waste of resources, as additional support could be dispatched towards the site of a false intentional attack. These reasons led us to accuracy, which considers both true and false negatives equally. 


---

## Baseline Model

#### Model Description
The model we chose to build on was a Logistic Regression Classifier, predominantly used for binary classification tasks. 
Our model aims to predict whether a power outage is intentional (IS_INTENTIONAL = 1) or not (IS_INTENTIONAL = 0).

#### Model Parameters
**Solver**: The logistic regression uses the 'saga' solver, which was chosen due to its efficiency in large datasets and support for regularization; an issue we were running into.
**Max Iterations**: The maximum number of iterations is set to 1000 to allow the solver ample opportunity to converge. Previous testing on lower values for this hyperparameter resulted in inconsistent non-convergence.

#### Features & Preprocessing
In this first iteration of our model, we attempted to think from the perspective of a prospective vandal, and had the thought that the time and condition of day could be very impactful to the success of an intentional attack. Thus, we chose two quantitative features, (the **month** and **time of day**), along with a third numerical feature, the anomaly level, a variable describing the level of current freak weather conditions in a specific region. 

| Feature            | Type              | Preprocessing                                                                                          |
|--------------------|-------------------|--------------------------------------------------------------------------------------------------------|
| ANOMALY.LEVEL        | Quantitative | Transformed using the absolute value, as we wanted to get the magnitude of freak weather events in both directions (hot/cold) |
| MONTH              | Quantitative       | Standard scaled to address non-standard scale issues for Logistic Regression.                         |
| OUTAGE.START.TIME  | Quantitative         | Transformed into a 24-hour format integer scale representing the hour of the day the outage started. Custom transformation using a FunctionTransformer.   |

#### Base Model Assessment
**Model Performance**
_Training Accuracy: ~0.7237
Testing  Accuracy: ~0.7359_

The model shows strong performance based on our observed accuracy scores, with a score on the testing set that fluctuates very close to the training set, sometimes even scoring higher. This indicates effective generalization to unseen data. Going into the future, this generalization is what we are looking for when attempting to determine whether additional specific support should be dispatched to look into a power outage. These reasons lead us to believe our model is looking good... or at the very least moving in a good direction!


## Final Model
#### Additional Features
Looking at the types of features we had, we realized we had a strong focus on time, without much consideration for geography. Split into multiple columns after OneHot Encoding, (the **us state** and **NERC regions** as partitioned by the North American Electric Reliability Corporation) will serve as our model insight into the geographical location an outage is occurring at.

Along with the addition of this feature, after testing, we decided to remove the ANOMALY.LEVEL feature, as we believed it was counterproductive to our efforts in being able to generalize for the future. Removing complexity in this case will help us slightly prioritize recall over precision.

#### Final Model Breakdown
| Feature            | Type              | Preprocessing                                                                                          |
|--------------------|-------------------|--------------------------------------------------------------------------------------------------------|
| NERC.REGION        | Categorical/Nominal | One hot encoding for all unique values, dropping the first encoded column to avoid multicollinearity. |
| U.S._STATE         | Categorical/Nominal | One hot encoding for all unique values, dropping the first encoded column to avoid multicollinearity. |
| MONTH              | Quantitative       | Standard scaled to address non-standard scale issues for Logistic Regression.                         |
| OUTAGE.START.TIME  | Quantitative         | Transformed into a 24-hour format integer scale representing the hour of the day the outage started. Custom transformation using a FunctionTransformer.   |


#### Hyperparameter Search
We chose to iterate over the max_iter parameter for our Linear Classifier. With a simple loop over different iteration sizes, we found that, interestingly, even though a recursion size of around 100-200 was most effective, convergence would not be reached consistently. Instead, a value around 600 max iterations had nearly identical accuracy, while being able to converge every time we ran it.  


#### Final Model Assessment
**Model Performance**
_Training Accuracy: ~0.8139
Testing  Accuracy: ~0.8492_

We were very happy with the level of accuracy we were able to get our model to. Scores in this second iteration were consistently around 10% heigher, and the LinearClassifier continued to generalize well, with training and testing accuracies consistently being similar. Testing accuracy also continued to fluctuate higher than training accuracy semi-often, a feature of the first model we wanted to preserve. Overall, we would characterize this as a "good" model.


---

## Fairness Analysis

### Groups and Evaluation Metric
- **Group X:** States with a Per Capita Real GSP (Gross State Product) relative to the total Per Capita Real GDP of the U.S. **greater than 1** (Richer States).
- **Group Y:** States with a Per Capita Real GSP relative to the total Per Capita Real GDP of the U.S. **less than or equal to 1** (Poorer States).
- The evaluation metric chosen for this analysis is **accuracy**, which quantifies the proportion of correct predictions out of all predictions made by the model.

### Null and Alternative Hypotheses
- **Null Hypothesis (H0):** The model's performance is fair across both groups. The accuracy for predicting intentional power outages in richer states is roughly the same as in poorer states, and any observed differences are due to random chance.
- **Alternative Hypothesis (H1):** The model's performance is not fair across both groups. The accuracy for predicting intentional power outages significantly differs between richer and poorer states.

### Choice of Test Statistic and Significance Level
- **Test Statistic:** The absolute difference in accuracy between Group X (richer states) and Group Y (poorer states).
- **Significance Level (α):** 0.05, indicating a 5% risk of concluding a difference in model performance across groups when there is none.

### Resulting p-Value
- **p-value:** The p-value obtained from the permutation test. This value indicates the probability of observing a test statistic as extreme as, or more extreme than, the one observed if the null hypothesis were true.

Based on the p-value obtained from the permutation test:
- **p-value > 0.05**, we fail to reject the null hypothesis, indicating insufficient evidence to conclude that the model's performance differs significantly across richer and poorer states. This suggests that any observed differences in accuracy across these groups can be attributed to random chance, and the model can be considered fair with respect to predicting intentional power outages based on economic status.

---
