# Data Science Challenge - Lighthouse

## The Problem

The challenge aims to evaluate the development of an Exploratory Data Analysis (EDA) and the knowledge/insights related to a classic data science problem: time series. For this, we would like to predict the GDP growth index for each country in the years 2024-2028 and later compare them with the predictions made by Statistica. Additionally, it is necessary to replace the "no data" fields with numerical values using inferences of your choice.

### Information about the data:

- The numerical GDP values are in percentage format.
- The dataset contains values filled as 'no data.'
- The dataset already contains GDP values for 2024-2028, resulting from a time series forecast with an unknown method.

## First Steps to Solve the Problem

### 1 Dataset Analysis

Understanding how the 'no data' values are distributed across countries, replacing them with 'None,' and checking the percentage of missing values for each country.

It was found that the dataset also contains GDP values for groups/categories, divided into: economies, regions, continents, and a set of continents.

### 2 Countries by Group/Category

Various lists were created with the same names as the existing groups and categories, and it was determined which countries belonged to them. Subsequently, a graphical analysis of past data (1980 to 2023) was performed, checking if there were any similarities in the data curves of these countries.

From this point on, all countries with missing data were analyzed, and the groups to be used for filling in the missing values were defined.

#### a) The following groups were used to replace the null values for countries:

- South Asia
- Advanced Economies
- Africa
- Europe
- Middle East
- Pacific Islands
- Southeast Asia
- Soviet Union*
- Central America

**This was the only group that did not exist in the dataset but was created to fill in the missing data. The justification for creating this group was based on the graphical analysis of the countries that were part of the Soviet Union and the understanding that they all tended to have a similar economy during their time in the union.*

#### b) The following groups were used to replace the null values for their respective groups:

- Sub-Saharan Africa (Region)
- Sub-Saharan Africa
- Euro area
- Middle East
- Central Asia and the Caucasus
- Central America

### 3 Method for Replacing Null Values

#### 3.1 Filling in Past Values

The steps taken were:

##### 3.1.1 Moving Average Calculation

The annual moving average was calculated based on all countries belonging to the same group. However, to calculate the average, only data from 1980 to 2019 was considered. This approach was implemented due to the COVID-19 pandemic, which caused a global anomaly.

*It is worth noting that the values of the groups/categories were not used for calculating the moving average.*

##### 3.1.2 Exceptions for a Few Null Values

For cases where countries had only 2 null values in their entire past data series, the mean was calculated for that specific country and filled in the missing data.

##### 3.1.3 Identifying Outliers

Finding the outliers for each country. The boxplot technique was used to find values considered outliers, using the following rule:

    q1 = np.percentile(series_without_nan, 25)
    q3 = np.percentile(series_without_nan, 75)
    iqr_value = q3 - q1
    
    lower_bound = q1 - 1.5 * iqr_value
    upper_bound = q3 + 1.5 * iqr_value

In this way, values above the upper bound and below the lower bound were not used for variance calculation.

##### 3.1.4 Variance Calculation

After determining which values would be used without the presence of outliers, the variance of each country was calculated. A rule was implemented so that if the **variance** was ***greater than a certain value X (individually determined based on each country's behavior***, the **median** would be calculated. This provided a *second filter for possible outliers*.

##### 3.1.5 Applying the Country's Variance to the Group's Moving Average

To finally fill in the null values, the variance (or median) was added (or subtracted) from the group's moving average. This created a curve identical to the moving average but with a shift in the curve.

To determine whether the *variance/median* found would be added or subtracted, a simple check was made:

- If the non-null values of the country for each year were greater than the moving average, the *variance/median* would be added to the moving average itself.
- Otherwise, the *variance/median* would be subtracted.

#### 3.2 Filling in Future Values

To fill in the missing future values, which do not yet represent predictions but rather a method to deal with missing data (as required in the challenge), the same rules described in section *3.1) Filling in Past Values* were applied.

The only exception for this case was that the moving average calculation was performed based on **all** the values in the dataset, **after handling the past missing data.**

### 4 Choice of Prediction Method

#### 4.1 Seasonal Decompose

Seasonal decompose was used to check if the data series exhibited trends and seasonality. For this purpose, a random list of 10 countries was created to examine these parameters.

Within seasonal decompose, it was opted to use:

    period = 10

This choice was made based on graphical analysis and the observation that similarities occurred among all countries during a 10-year period.

#### 4.2 Creation of Metrics

A function was created to calculate metrics: MAE, RMSE, and MAPE.

#### 4.3 Augmented Dickey-Fuller Test

The ADF test was used to find countries that had stationary and non-stationary data series. All models were applied separately based on the type of data series, whether stationary or non-st
