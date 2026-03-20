# Power Outages Analysis

  By Matthew Feng and Yu-Ming (Kenny) Wu
  
---

# Introduction 

In this data science project, we are given a dataset about the major power outage caused by severe weather conditions in the US, from January 2000 to July 2006. The server outages (or major outages) is refers to  those that impacted least 50,000 customers or caused an unplanned firm load loss of atleast 300 MW. This defined by the Department of Energy. People can access to the original data at https://engineering.purdue.edu/LASCI/research-data/outages, which is published by Purdue University, Laboratory for Advancing Sustainable Critical Infrastructure.

The dataset includes the basic information (time and location) as well as the impacts regarding the power outages. Such as the general information - time and geographic areas, regional climate information, outage events information, cause of the event, extent of outages, regional electricity consumption information, regional economic characteristics, and regional land-use characterics. 

Our goal is to investigate further meaning in the data through statistical tests and eventually come up with a prediction model to estimate the duration of a power outage by information that can be gained when a power outage occurs. We are trying to figure out a research problem: which outage characteristics have the greatest impact on restoration time? To solveing this problem, we want to help people, especially policy makers, to figure out what can they do to reduce power outage duration, improve their living quality.

The data set originally have 1534 rows and 55 columns, which corresponded to 1534 outage events and 55 features for each outage event. There are few of these features listed below, which related with our analysis.

## Columns used from the dataset for this project

| Column Name | Description |
|-------------|-------------|
| "U.S._STATE" | Represents all the states in the continental U.S. |
| "YEAR" | Indicates the year when the outage event occurred |
| "MONTH" | Indicates the month when the outage event occurred |
| "CLIMATE.REGION" | U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.) |
| "PC.REALGSP.STATE" | Per capita real gross state product (GSP) in the U.S. state |
| "OUTAGE.START.DATE" | Outage start date |
| "OUTAGE.START.TIME" | Outage start time |
| "OUTAGE.RESTORATION.DATE" | Outage restoration date |
| "OUTAGE.RESTORATION.TIME" | Outage restoration time |
| "TOTAL.PRICE" | Average monthly electricity price in the U.S. state |
| "CAUSE.CATEGORY" | Categories of events causing the major power outages |
| "DEMAND.LOSS.MW" | Amount of peak demand lost during an outage event |
| "CUSTOMERS.AFFECTED" | Number of customers affected by the outage |
| "TOTAL.SALES" | Total electricity consumption in the U.S. state |

# Data Cleaning and Exploratory Data Analysis
Before analyzing the data, we need to rename the columns to increase readability and handle missingness for a more accurate analysis. 

## Actions done 
1. Since the raw data is in excel form, thus we removed 5 extra rows at the top and 1 extra column at the right side, moved the features name at the top of the dataframe
   
2. After remove these extra rows and column, the top two rows are feature names and the unit or description with corresponded feature names. To format this, we modify punctuations from `.` to underscore `_` for better readability. Also, combined the unites and the coresponding features together with a single space. Such as: `RES.PERCEN` -> `Res_percen (%)`, `RES.PRICE` -> `Res_Price (cents/kWh)` etc.

3. Next, we select few relevant features that used for my analysis. The columns include: `US_state`, `Year`, `Pc_realgsp_state ($)`, `Outage_start_date`, `Outage_start_time`, `Outage_restoration_date`, `Outage_restoration_time`, `Total_price (cents / kWh)`, `Cause_category`, `Demand_loss_mw`, `Customers_affected`, `Total_sales`.

4. To get the outage restoration duration, we combined `Outage_start_date`, `Outage_start_time`, `Outage_restoration_date`, `Outage_restoration_time` together. First, we transform all these features into "datetime" data strucutre, then use `Outage_restoration_date` and `Outage_restoration_time` minus the `Outage_start_date` and `Outage_start_time` to get the restoration duration for each outage, transform the durating into the unit of hour. Lastly, drop columns regarding outage/restoration time  - `Outage_start_date`, `Outage_start_time`, `Outage_restoration_date`, `Outage_restoration_time`.

5. Notice there are `0` exist in `Outage_duration` which we calculated, which means there is 0 hour of outage happend, seems like a missing value. Thus we transform "0"s in the `Outage_duration` into `np.nan`.

This is the first 5 rows of the cleaned dataframe:

|   Obs | US_state   |   Year |   Pc_realgsp_state ($) |   Total_price (cents / kWh) | Cause_category     |   Demand_loss_mw |   Customers_affected |   Total_sales |   Outage_duration |
|------:|:-----------|-------:|-----------------------:|----------------------------:|:-------------------|-----------------:|---------------------:|--------------:|------------------:|
|     1 | Minnesota  |   2011 |                  51268 |                        9.28 | severe weather     |              nan |                70000 |       6562520 |        51         |
|     2 | Minnesota  |   2014 |                  53499 |                        9.28 | intentional attack |              nan |                  nan |       5284231 |         0.0166667 |
|     3 | Minnesota  |   2010 |                  50447 |                        8.15 | severe weather     |              nan |                70000 |       5222116 |        50         |
|     4 | Minnesota  |   2012 |                  51598 |                        9.19 | severe weather     |              nan |                68200 |       5787064 |        42.5       |
|     5 | Minnesota  |   2015 |                  54431 |                       10.43 | severe weather     |              250 |               250000 |       5970339 |        29         |

# Exploratory Data Analysis
## Univariate Analysis

For exploring the data set, we used univariate analysis to visulize the distribution or the other information of a single feature.

First, we draw the histogram on feature `Outage_duration`:

<iframe
  src="graph/dist_of_outage_duration.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the histogram graph above, we can see that the outage duration have a right skew shape, shows most outage duration being fixed really fast.

Second, we can visualize the `Pc_realgsp_state` by histogram, to see the distribution of the real GSP per state, in dollar:

<iframe
  src="graph/dist_of_real_GSP_per_state.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the histogram above, we can see most US states have averagely 50k of real gsp at most time from January 2000 to July 2016. Only few states have 160k of real gsp at some time between January 2000 to July 2016.

### Map Figures

Last, we can visualize the number of outage happened in each state of US, from January 2000 to July 2016:

<iframe
  src="graph/number_of_power_outage.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the map above, we can see that the most number of outages are happened in California, and the second most number of outages are happened in Texas.

## Bivariate Analysis

Also we used bivariate analysis to visulize the distribution or the other information of two features. To help me understand some relation between pairs of features.

First, we want to visualize the relation between the causation of outages and the median of outage duration by bar graph. we chose to use median of outage duration because of the distribution of outage duration is skewed, using median can better explain the distribution of outage duration.

<iframe
  src="graph/median_outage_duration_by_cause.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the bar graph, we can see that most outages is caused by the fuel supply emergency. The second main cause of power outage is severe weather.

### Map Figures

Second, we can visualize the average outage duration in each state of US, from January 2000 to July 2016:

<iframe
  src="graph/avg_outage_duration_map.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the map above, we can see that Wisconsin and West Virginia take longest average time to restore power to the outage area.

## Grouping and Aggregates 

We create a pivot-table to see the median outage duration by each cause over years. Using median value can better represent the shape and the distribution of the skewed data. The columns are the corresponded years, the rows are corresponded causation of power outages. The pivot-table is presented as below:

| Cause_category                |      2000 |      2001 |      2002 |      2003 |      2004 |      2005 |     2006 |       2007 |      2008 |      2009 |      2010 |       2011 |      2012 |       2013 |       2014 |       2015 |      2016 |
|:------------------------------|----------:|----------:|----------:|----------:|----------:|----------:|---------:|-----------:|----------:|----------:|----------:|-----------:|----------:|-----------:|-----------:|-----------:|----------:|
| equipment failure             | nan       |   8.23333 | nan       |   2.59167 |   3.68333 |   2.48333 |   2.65   |   3.43333  |   4.48333 |  15.45    |   4.91667 |  0.0666667 |  1.75     |   3.25833  | nan        | nan        | nan       |
| fuel supply emergency         | nan       | nan       | nan       | nan       | nan       | nan       | nan      |  27.9333   | 396       | 109.5     | 168       | 78.75      | 60        |  43.7167   |  93.575    |   4.25     | 568.367   |
| intentional attack            | nan       | nan       | nan       |  22.1583  | nan       | nan       | nan      | nan        | nan       | nan       | nan       |  1.16667   |  0.25     |   1        |   0.15     |   1        |   1.3     |
| islanding                     | nan       | nan       | nan       | nan       | nan       | nan       | nan      |   0.783333 |   3.725   |   4.84167 |   2.03333 |  0.883333  |  0.333333 |   0.533333 |   0.933333 |   0.583333 |   3.71667 |
| public appeal                 | nan       |   2.33333 | nan       |  25.8     |  72       | nan       |   5.95   |  40.5083   | 100.958   |   5       |   8.53333 | 10         |  5        | nan        |   6.7      |   5.55833  | nan       |
| severe weather                |  41.5     | 169       |  86       |  62.2333  |  34.5     |  73.5     |  45.5417 |  25.5      |  53       |  45.1667  |  48.875   | 38.3167    | 41.7833   |  31        |  16.6667   |  32.5      |  23.4167  |
| system operability disruption |   6.25833 |   4.01667 |   3.83333 |  44.9     |   1.58333 |   4.11667 |   3.95   |   6.91667  |   3.24167 |   1.775   |   3.31667 |  4.86667   |  0.216667 |   2.01667  |   0.866667 |   1.08333  |   3.7     |

In this pivot table, the "nan" value shows there is not power outage happened in the corresponded year with the corrponded causations. I notice that on row "servere weather" and "system operability disruption" there are power outages happened every year, showing these two causation is more common than other 6 power outage causations.

Also, in the row of "fuel supply emergency", the duration of outage restoration is averagely longer then all other 7 power outage causations based on the corresponded years, showing "fuel supply emergency" is a severe power outage causation.

# Assessment of Missingness
In the cleaned dataframe, there are columns with missing values: Total_price (cents / kWh), Demand_loss_mw, Customers_affected, and Total_sales. The following table shows the missingness proportion of each column. 

| Column Name | Proportion of Missing Values |
|-------------|------------------------------|
|Total_price (cents / kWh)|0.014342|
|Demand_loss_mw|0.459583|
|Customers_affected|0.288787|
|Total_sales|0.014342|
|Outage_duration|0.037810|


## NMAR Analysis
Among these columns, "Demand_loss_mw" and "Customers_affected" are likely to be MNAR, because the missingness may depend on the companies which measure the scale of impact of the power outages. When the outage is severe, it may be difficult to collect data over a wide range. On the other hand, when the impact of the outage is negligible, the companies may just ignore and not to report it. It is also possible that certain companies are not reporting, but we still cannot conclude the exact reason why some data is missing. 

## MAR Analysis 
Now, we want to find out the missingness dependency of "Total_price (cents / kWh)" and "Total_sales". By examining the dataframe, we found out that both columns have missing value in the year of 2016, so we decided to  perform some permutation test to confirm this dependency. The following figure shows the frequency of outage over year conditioned on whether "Total_price (cents / kWh)" is missing. 

<iframe
  src="graph/total_price_missing.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Then we set up a hypothesis test to find the missing mechanism of feature `Total_price (cents / kWh)` by using permutation test. We will set the significance level into 0.05.

**Null Hypothesis:**  The distribution of `Year` is same when `Total_price (cents / kWh)` is missing vs not missing.

**Alternate Hypothesis:** The distribution of `Year` is different when `Total_price (cents / kWh)` is missing vs not missing.

**Test Statistic:** TVD - Total Variation Distance

The following figure shows the result the permutation test:

<iframe
  src="graph/total_price_missing_perm.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the graph above, we can see the observed TVD of feature `Total_price (cents / kWh)` missingness has a p-value 0.0. The result show we reject the null hypothesis. This result means the feature `Total_price (cents / kWh)` missing value is dependent on the feature `Year`, showing `Total_price (cents / kWh)` is likely to be MAR.

Similarly, The following figure shows the frequency of outage over year conditioned on whether "Total_sales" is missing. 

<iframe
  src="graph/total_sales_missing.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Then we set up a hypothesis test to find the missing mechanism of feature `Total_sales` by using permutation test. We will set the significance level into 0.05.

**Null Hypothesis:**  The distribution of `Customers_affected` is same when `Total_sales` is missing vs not missing.

**Alternate Hypothesis:** The distribution of `Customers_affected` is different when `Total_sales` is missing vs not missing.

**Test Statistic:** Mean difference 

The following figure shows the result the permutation test. The test statistic is the total variance distance between two groups because "Year" is a categorical feature. 

<iframe
  src="graph/total_sales_missing_perm.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the graph above, we can see the observed TVD of feature `Total_sales` missingness has a p-value 0.0. The result show we reject the null hypothesis. This result means the feature `Total_sales` missing value is dependent on the feature `Customers_affected`, showing `Total_sales` is likely to be MAR.

Lastly, we set up a hypothesis test to find the missing mechanism of feature `Outage_duration` by using permutation test. We will set the significance level into 0.05.

<iframe
  src="graph/outage_missing.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

**Null Hypothesis:**  The distribution of `Cause_category` is same when `Outage_Duration` is missing vs not missing.

**Alternate Hypothesis:** The distribution of `Cause_category` is different when `Outage_Duration` is missing vs not missing.

**Test Statistic:** TVD - Total Variation Distance

<iframe
  src="graph/outage_missing_perm.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Through the graph above, we can see the observed TVD of feature `Outage_Duration` missingness has a p-value 0.0. The result show we reject the null hypothesis. This result means the feature `Outage_Duration` missing value is dependent on the feature `Cause_category`, showing `Outage_Duration` is likely to be MAR.


## Handling Missingness

| Column | Method | 
|--------|--------|
| "Demand_loss_mw" | group mean inputation on "Cause_category" |
| "Customers_affected" | group mean inputation conditioned on "US_state" |
| "Total Sales" | group mean inputation on "US_state" |
| "Total_price (cents / kWh)" | group mean inputation conditioned on "US_state" |
| "Outage_duration" | group mean inputation conditioned on "Cause_category" |


# Hypothesis Testing 
We will be testing on whether richer/more developed states in the US have a different outage duration, meaning that the power restoration comes back faster in states with better economic status. After finding the average real GSP of all states (roughly 50,000), we decided to label each state by "above 50,000" and "below 50,000" (Since feature `Pc_realgsp_state ($)` have a roughly normal distribution, which we find the median and mean of this feature is rouphly 50,000, thus,  mean is an appropriate measure of center using for the permutation test).

**Null hypothesis:** The duration of the restoring time has no difference between the states with more than 50,000 real GSP and the states with less than 50,000 GSP.

**Alternative hypothesis:** There is a difference in the power outage duration between states with more than 50,000 real GSP and those with less than 50,000 GSP.

**Test statistics:** The absolute difference in the median of 2 groups. We chose median because the distribution of the power outage duration is right-skewed. The absolute value is for observing the difference between categorical groups. 

The following graph shows the distribution of the power outage duration between two groups. The label "True" indicates the power outages occured in states with a real GSP over 50,000. False just means the other way. 

<iframe
  src="graph/outage_duration_with_labels.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

The following figure visualizes the result of the permutation test, where the red axis represents our observed test statistic. The p-value of this test is 0.0, meaning that none of the iterations in the test has a greater test statistics than our observed test statistic (same as the figure). Therefore, we reject the null hypothesis and conclude that there is a difference in the distributions of outage duration between two labels of states. 

<iframe
  src="graph/perm_test_dist.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>


## Framing a Prediction Problem 
Our model aims to estimate/predict the power outage duration by the some selected features. We want to use these features to estimate/predict the time (hours) that the power outage can be fixed.

This is a typical regression problem, so the performance matrix chosen is mean absolute error (MAE) to check the bias of the model prediction, since the response value , `Outage_Duration`, is right skewed, using MAE can be less sensative to these extreme `Outage_duration` values; using R square(R^2) metrix to check the variance of the model prediction. We are aiming for lower RMSE, which reduce the bias between predicted value and actual value, making predicted value closer to actual value. We are also aiming for larger R square score (closer to 1), which model can explains a greater proportion of the variability of the actual value.

## Data Cleaning for Modeling

Before modeling, we notice that there is a small number of unusually large  `Outage_duration` values may reduce the performance of the model, which these data are extreme and not representative of most cases. 

Thus, we seems these data as outliers, as defined by the IQR method, which defines outliers as values either below Q1 - 1.5 * IQR or above Q3 + 1.5 * IQR, where Q1 is the 25 percentile of the data, Q3 is the 75 percentile of the data, and IQR is range between Q1 and Q3 (Q3 - Q1).

We will use the data set without these `Outage_duration` outliers for both baseline modeling and the final modeling.

## Base Line Model 

Our model is Multiple Linear Regression, using features  `Month`, `US_state`, `Pc_realgsp_state ($)`, `Total_price (cents / kWh)`, `Cause_category`, `Demand_loss_mw`, `Customers_affected`, `Total_sales` to build oure baseline model, which to predict the duration of power outage restoration. All these informations can help people to better estimate the time length of power outage restoration.

We have ordinal feature: `Month`, nominal feautures: `US_state`, `Cause_category`, and quantitative features: `Pc_realgsp_state ($)`, `Total_price (cents / kWh)`, `Demand_loss_mw`, `Customers_affected`, `Total_sales`. We believe these features are relate with your response variabe `Outage_duration`, such as different region may have different approach to restore the power outage, so we use the feature `US_state`; the state real Gross State Product (GSP) represent the financial strength of the state may affect the time for restore the power outage, so we use the feature `Pc_realgsp_state ($)`.

In the baseline model we use `OneHotEncoder` to transform the ordinal features and nominal features in to 0 or 1 distinct groups, and we drop the first column after feature bing one-hot encoded to prevent the collinearity. Encoding these nominal and ordinal features help use convert object-type features in to numerical type which can used for training the model.

The performance of this model is not so good, which we get a mean absolute error of 21.6324, a R squared score of 0.02806. The MAE shows the model predict the outage duration deviate from the actual outage duration, and R squared score indicates the model explain very small proportion of the variability of the actual outage duration.

The model performance can be visualized below, which the scatter plot shows how predict value is far away from the actual value by the y=x reference line:

<iframe
  src="graph/baseline.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>


## Final Model 

To get a better estimate/prediction on the outage duration, we select new features, done some feature engineering, choose a new model, and use CV fold to train the model.

### Feature Selection
We use the pearson correlation to compared between feature `Outage_duration` with all other features, and get 3 more features: `Util_contri (%)`, `Pct_land (%)`, and `Pct_water_tot (%)` that have higher pearson correlations then all other features that not being selected in the baseline model. We included these new three features into our predictor data set.

### Feature Engineering
1. we mapping the feature `Month` into `Season`, from ordinal into nominal which each three month will be transformed in to their corresponded season, such as 12, 1, 2 will map to  "Winter". To do so, we expected using season instead of month having more explanatory power, which the power outage duration may affected by the seasonal climate. This feature transformation can also reduced the noise which reduced from 12 categories into 4 categories. We then include this transformer into our pipeline.

2. We put another transformer - `StandardScaler` - into our pipeline for quantitative data. Using `StandardScaler` can bring all quantitative data more centralized to 0 and keep the shpae of the feature distribution. This transformer can help model train more stably, reduce the number scale of the large=scale features.

### Model Selection
We changed our model from Multiple Linear Regression into Random Forest Regressor. This model can better capture the nonlinear relationships and be more robust to outliers and unusual patterns, which we expect will have better performance then Multiple Linear Regression. IN Random Forest Regressor, we choose to use hyperparameters: `n_estimators`, `max_depth`, `min_samples_leaf`.

### Model Training
We used the `GridSearchCV` with `cv` of 5 and `scoring` of "neg_mean_absolute_error". The `GridSearchCV` find the best hyperparameters of:
    *max_depth: 10
    *min_samples_leaf: 1
    *n_estimators: 200

### Model Performance
We get a better performanced model compare with the base line model. We get mean absolute error of 14.4750 and R square score of 0.3471. These metrix shows the predicted value made by the final model is closer to the actual value, and the final model can explain more proprotion of the variability of the actual outage duration compare witht he base line model.


The graph below the the learning curve for hyperparameter of `n_estimators`, which shows the training loss and validation loss changing as training progress:

<iframe
  src="graph/n_estimators.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

The graph below the the learning curve for hyperparameter of `max_depth`, which shows the training loss and validation loss changing as training progress:

<iframe
  src="graph/max_depth.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

The graph below the the learning curve for hyperparameter of `min_samples_leaf`, which shows the training loss and validation loss changing as training progress:

<iframe
  src="graph/min_samples_leaf.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

The model performance can be visualized below, which the scatter plot shows how predict value is far away from the actual value by the y=x reference line:

<iframe
  src="graph/final.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

## Fairness Analysis (Matthew)

<iframe
  src="graph/fairness.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>
