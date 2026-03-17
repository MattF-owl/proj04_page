# Power Outages Analysis

By Matthew Feng and Yu-Ming (Kenny) Wu
---

# Introduction 

In this data science project, we are given a dataset about the major power outage caused by severe weather conditions in the US, from January 2000 to July 2006. The server outages (or major outages) is refers to  those that impacted least 50,000 customers or caused an unplanned firm load loss of atleast 300 MW. This defined by the Department of Energy.

The dataset includes the basic information (time and location) as well as the impacts regarding the power outages. Our goal is to investigate further meaning in the data through statistical tests and eventually come up with a prediction model to estimate the duration of a power outage by information that can be gained when a power outage occurs. 

## Columns used from the dataset for this project

| Column Name | Description |
|-------------|-------------|
| "U.S._STATE" | Represents all the states in the continental U.S. |
| "YEAR" | Indicates the year when the outage event occurred |
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
1. Modify column name format (ie: replace "." by "_")
2. Reindex the dataframe 
3. Remove all rows with missing outage-time-related features
4. Add an extra column named "outage_duration", which is calculated by restoration time and start time of the power outage in hours
5. Drop columns regarding outage/restoration time 


This is the first 5 rows of the cleaned dataframe:

|   Obs | US_state   |   Year |   Pc_realgsp_state ($) |   Total_price (cents / kWh) | Cause_category     |   Demand_loss_mw |   Customers_affected |   Total_sales |   Outage_duration |
|------:|:-------------|-------:|-----------------------:|----------------------------:|:-------------------|-----------------:|---------------------:|--------------:|------------------:|
|     1 | Minnesota    |   2011 |                  51268 |                        9.28 | severe weather     |              nan |                70000 |       6562520 |        51         |
|     2 | Minnesota    |   2014 |                  53499 |                        9.28 | intentional attack |              nan |                  nan |       5284231 |         0.0166667 |
|     3 | Minnesota    |   2010 |                  50447 |                        8.15 | severe weather     |              nan |                70000 |       5222116 |        50         |
|     4 | Minnesota    |   2012 |                  51598 |                        9.19 | severe weather     |              nan |                68200 |       5787064 |        42.5       |
|     5 | Minnesota    |   2015 |                  54431 |                       10.43 | severe weather     |              250 |               250000 |       5970339 |        29         |


# Exploratory Data Analysis
## Univariate Analysis
Distribution of outage duration (hours):
<iframe
  src="graph/dist_of_outage_duration.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Distribution of realGSP by state ($):
<iframe
  src="graph/dist_of_real_GSP_per_state.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

## Bivariate Analysis
Average outage distribution by state:
<iframe
  src="graph/avg_outage_duration_by_state.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

## Map Figures
Number of distribution by state
<iframe
  src="graph/number_of_power_outage.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Average outage duration by state
<iframe
  src="graph/avg_outage_duration_map.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

# Assessment of Missingness
In the cleaned dataframe, there are columns with missing values: Total_price (cents / kWh), Demand_loss_mw, Customers_affected, and Total_sales. The following table shows the missingness proportion of each column. 

| Column Name | Proportion of Missing Values |
|-------------|------------------------------|
|Total_price (cents / kWh)|0.008130|
|Demand_loss_mw|0.455285|
|Customers_affected|0.284553|
|Total_sales|0.008130|


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

The following figure shows the result the permutation test. The test statistic is the total variance distance between two groups because "Year" is a categorical feature. 

<iframe
  src="graph/total_price_missing_perm.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

Similarly, The following figure shows the frequency of outage over year conditioned on whether "Total_sales" is missing. 

<iframe
  src="graph/total_sales_missing.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

The following figure shows the result the permutation test. The test statistic is the total variance distance between two groups because "Year" is a categorical feature. 

<iframe
  src="graph/total_sales_missing_perm.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>



## Handling Missingness

| Column | Method | 
|--------|--------|
| "Demand_loss_mw" | group mean inputation on "Cause_category" |
| "Customers_affected" | group mean inputation conditioned on "Us_state" |
| "Total Sales" | group mean inputation on "Us_state" |
| "Total_price (cents / kWh)" | group mean inputation conditioned on "Us_state" |


# Hypothesis Testing 
We will be testing on whether richer/more developed states in the US have a different outage duration, meaning that the power restoration comes back faster in states with better economic status. After finding the average real GSP of all states (roughly 50,000), we decided to label each state by "above 50,000" and "below 50,000"

Null hypothesis: The duration of the restoring time has no difference between the states with more than 50,000 real GSP and the states with less than 50,000 GSP.

Alternative hypothesis: There is a difference in the power outage duration between states with more than 50,000 real GSP and those with less than 50,000 GSP.

Test statistics: The absolute difference in the median of 2 groups. We chose median because the distribution of the power outage duration is right-skewed. The absolute value is for observing the difference between categorical groups. 

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
Our model aims to estimate/predict the power outage duration by the selected features ("Pc_realgsp_state ($)", "Total_price (cents / kWh)", "Cause_category", "Demand_loss_mw", "Customers_affected", "Total_sales",). This is a typical regression problem, so the performance matrix chosen is root mean square error (RMSE). The lower RMSE, the closer distance between the predicted value and the actual value. 

## Base Line Model
The selected features as indicated above. Before training, we need to address a few issues regarding the data. First, "Cause_category" is a nomial categorical feature, so we need to apply OneHotEncoder to convert the categories into columns of 0's and 1's. Then, we standardize the continuous quantitative features to reduce the impact of outliers. Lastly, since the distribution of power outage duration is right-skewed, performing a log-transformation makes the trend relatively more linear. For the base line model, we implemented the linear regression model and observed the performance on the testing data (20% of the dataset). The following figures show the actual outage duration verses our prediction in the logarithmic scale and the original scale.

<iframe
  src="graph/baseline_log.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

<iframe
  src="graph/baseline_original_scale.html"
  width="100%"
  height="600"
  frameborder="0">
</iframe>

By looking at both scatter plots, it is obvious that the model does very poorly in the prediction because the data points are sparse. Statistically, the RMSE of the testing is 1.249 in the log scale and 126.373 in the original scale. The average outage duration is only 43.755 hours. The information emphasizes how bad the prediction is. If we look closely to the figure of the original scale, we can observe that the data points are mostly clustered in the bottom-left corner, indicating that the majority of the training data has a short outage duration. It implies that the existance of outliers in outage durations, which is one issue that we must address in the final model. 

## Final Model 

## Fairness Analysis
