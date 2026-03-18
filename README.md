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
   
2. after remove these extra rows and column, the top two rows are feature names and the unit or description with corresponded feature names. To format this, I connection symbols from '.' to underscore '_' for giving a better visualizaiton. Also, combined the unites and the coresponding features together with a single space. Such as: 'RES.PERCEN' -> 'Res_percen (%)', 'RES.PRICE' -> 'Res_Price (cents/kWh)' etc.

3. Next, I select few relevant features that used for my analysis. They are: 'US_state', 'Year', 'Pc_realgsp_state ($)', 'Outage_start_date', 'Outage_start_time', 'Outage_restoration_date', 'Outage_restoration_time', 'Total_price (cents / kWh)', 'Cause_category', 'Demand_loss_mw', 'Customers_affected', 'Total_sales'.

4. To get the outage restoration duration, we combined 'Outage_start_date', 'Outage_start_time', 'Outage_restoration_date', 'Outage_restoration_time' together. First, we transform all these features into "datetime" data strucutre, then use 'Outage_restoration_date' and 'Outage_restoration_time' minus the 'Outage_start_date' and 'Outage_start_time' to get the restoration duration for each outage, transform the durating into the unit of hour. Lastly, drop columns regarding outage/restoration time  - 'Outage_start_date', 'Outage_start_time', 'Outage_restoration_date', 'Outage_restoration_time'.

5. Notice there are '0' exist in 'Outage_duration' which we calculated, which means there is 0 hour of outage happend, seems like a missing value. Thus we transform "0"s in the 'Outage_duration' into 'np.nan'

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
  src="graph/median_outage_duration_by_cause.html"
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

## Grouping and Aggregates 
We create a pivot-table to see the median outage duration by each cause over years. The pivot-table is presented as below:

| Cause_category                |      2000 |      2001 |      2002 |      2003 |      2004 |      2005 |     2006 |       2007 |      2008 |      2009 |      2010 |       2011 |      2012 |       2013 |       2014 |       2015 |      2016 |
|:------------------------------|----------:|----------:|----------:|----------:|----------:|----------:|---------:|-----------:|----------:|----------:|----------:|-----------:|----------:|-----------:|-----------:|-----------:|----------:|
| equipment failure             | nan       |   8.23333 | nan       |   2.59167 |   3.68333 |   2.48333 |   2.65   |   3.43333  |   4.48333 |  15.45    |   4.91667 |  0.0666667 |  1.75     |   3.25833  | nan        | nan        | nan       |
| fuel supply emergency         | nan       | nan       | nan       | nan       | nan       | nan       | nan      |  27.9333   | 396       | 109.5     | 168       | 78.75      | 60        |  43.7167   |  93.575    |   4.25     | 568.367   |
| intentional attack            | nan       | nan       | nan       |  22.1583  | nan       | nan       | nan      | nan        | nan       | nan       | nan       |  1.16667   |  0.25     |   1        |   0.15     |   1        |   1.3     |
| islanding                     | nan       | nan       | nan       | nan       | nan       | nan       | nan      |   0.783333 |   3.725   |   4.84167 |   2.03333 |  0.883333  |  0.333333 |   0.533333 |   0.933333 |   0.583333 |   3.71667 |
| public appeal                 | nan       |   2.33333 | nan       |  25.8     |  72       | nan       |   5.95   |  40.5083   | 100.958   |   5       |   8.53333 | 10         |  5        | nan        |   6.7      |   5.55833  | nan       |
| severe weather                |  41.5     | 169       |  86       |  62.2333  |  34.5     |  73.5     |  45.5417 |  25.5      |  53       |  45.1667  |  48.875   | 38.3167    | 41.7833   |  31        |  16.6667   |  32.5      |  23.4167  |
| system operability disruption |   6.25833 |   4.01667 |   3.83333 |  44.9     |   1.58333 |   4.11667 |   3.95   |   6.91667  |   3.24167 |   1.775   |   3.31667 |  4.86667   |  0.216667 |   2.01667  |   0.866667 |   1.08333  |   3.7     |


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
