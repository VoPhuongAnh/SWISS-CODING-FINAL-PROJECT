# SWISS-CODING-FINAL-PROJECT

## A - Project title:

LADA's potential customers analysis.

## B - Purpose and Outcome:

Purpose: To help LADA's management understand the insights behind customer segments through studying LADA's sales database

Outcome: 

Meaningfull insights into sales situation of the company over 3 years in general, and each year in detail.

Try to understand the main features of the potential customers or popular model that LADA should focus their sales promotions on in the future.

## C - **Technologies**

In this project, I intend to use Python and its labraries for the cleaning step before doing any further EDA and export cleaned dataset to csv file.  
For download dataset, I decide to use Kaggel's API to diectly get the raw data from this website.

In the visualization step, i use Power BI for mainly visualization and doing some more further analysis. 

I currently use Github to store all the images, code and documentaion for this project. The repo is public for referencing and studying.

## D - Dataset and Sources:

**Disclaimer:** This data is not belong to mine. You can fine the whole dataset at this link: https://www.kaggle.com/datasets/artemcheremuhin/lada-car-sales

**License:** CC0: Public Domain

_In case of comercial use and purpose, please contact Kaggle and the author of this dataset. _

### Dataset 1: lada_buyers.csv

Source: **LADA_car_sales - Kaggle**

Description :  This dataset contains information about Lada car buyers, including demographic data, income, and purchase details. 

Structure: This data is reported in 1 csv file with 7 columns and 5532 records

Columns: This dataset contain total of 7 columns, details as below:  

- purchase_id – Unique identifier for each purchase.
- id – Unique identifier for each buyer
- Age – Age of the buyer
- Sex – Gender of the buyer
- Income – Annual income of the buyer (RUB).
- Purchase_Date – Date of the car purchase (ranging from January 2021 to December 2023).
- Region – Region where the car was purchased (includes all regions of the Volga Federal District in Russia).

Rows: This dataset contain total of 5532 records

### Dataset 2: lada_machines.csv

Source: **LADA_car_sales - Kaggle**

Description :  This dataset contains details about the purchased Lada cars, including model type, price, engine specifications, and additional options.

Structure: This data is reported in 1 csv file with 7 columns and 5532 records

Columns: This dataset contain total of 7 columns, details as below: 

- purchase_id – Unique identifier for each purchase, linking to lada_buyers.csv.
- Model – Model of the purchased Lada car
- Price – Purchase price of the car
- Engine_Power – Engine power of the purchased car in horsepower
- Transmission – Type of transmission (Manual "MT" or Automatic "AT").
- Fuel_Type – Type of fuel used by the car (Gasoline or Diesel).
- Num_Additional_Options – Number of additional options purchased with the car.

Rows: This dataset contain total of 5532 records

## E - Initial Analysis plan:

In this project, I intend to start with cleaning and furnishing the dataset first; and then perform furrther correlation analysis and generate actionable insights from the data.

#### Data cleaning:

a/ Data loading: I use Kaggle API to get the data. Below is the code:

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns


# 1. Install Kaggle API in Google Colab
!pip install kaggle

from google.colab import files
files.upload()

import os
# Make a directory to store Kaggle credentials
os.makedirs('/root/.kaggle', exist_ok=True)

# Move the kaggle.json file into this directory
!mv kaggle.json /root/.kaggle/

# Change the permissions of the kaggle.json file
!chmod 600 /root/.kaggle/kaggle.json

!kaggle datasets download -d artemcheremuhin/lada-car-sales

!unzip lada-car-sales.zip

# Read the csv files in dataset

# Load the dataset
df_buyer = pd.read_csv('lada_buyers.csv')

# Display the first few rows - 1st dataset:
df_buyer.head()
```

b/ Data Profiling: Sine the columns for analysisng is loacated in 2 different dataframe so that I join the 2 dataframes to gain a consolidated table including all the columns of the 2 dataframe, using **purchase_id** columns in 2 tables as key;

- I use the below code:
  ```
  # combine 2 table using purchase_id as foreign key
  df_joined = pd.merge(df_buyer, df_machine, on='purchase_id', how='outer')

  # check the data head again: 
  df_joined.head()
  ```
  So my result is:
  
|   | purchase_id |     id | Age | Sex |   Income | Purchase_Date |                  Region |  Model |     Price | Engine_Power | Transmission | Fuel_Type | Num_Additional_Options |
|--:|------------:|-------:|----:|----:|---------:|--------------:|------------------------:|-------:|----------:|-------------:|-------------:|----------:|-----------------------:|
| 0 |           1 | 199010 |  46 |   M | 240000.0 |    2021-07-01 |   Удмуртская Республика |   2113 |  900000.0 |          112 |           MT |    Petrol |                      4 |
| 1 |           2 | 200176 |  56 |   M | 575000.0 |    2023-03-01 |       Самарская область | Kalina | 2750000.0 |          132 |           MT |    Petrol |                      1 |
| 2 |           3 | 199908 |  31 |   M | 287000.0 |    2021-01-01 |    Республика Татарстан |   2112 | 1450000.0 |          142 |           MT |    Petrol |                      4 |
| 3 |           4 | 199237 |  44 |   M | 240000.0 |    2023-12-01 |   Нижегородская область |   2109 | 1400000.0 |           97 |           AT |    Petrol |                      1 |
| 4 |           5 | 198720 |  57 |   M | 262000.0 |    2022-01-01 | Республика Башкортостан | Largus |  700000.0 |          142 |           MT |    Petrol |                      1 |
  
c/ Fixing data types: object → string/date/category ; fix dates using :

- I check the info of joined dataframe and see that there are some columns do not have the dtype that I want:
   ```
   # check columns and dtypes:
   df_joined.info()

   # >> result:

      <class 'pandas.core.frame.DataFrame'>
   RangeIndex: 5532 entries, 0 to 5531
   Data columns (total 13 columns):
    #   Column                  Non-Null Count  Dtype  
   ---  ------                  --------------  -----  
    0   purchase_id             5532 non-null   int64  
    1   id                      5532 non-null   int64  
    2   Age                     5532 non-null   int64  
    3   Sex                     5532 non-null   object 
    4   Income                  5532 non-null   float64
    5   Purchase_Date           5532 non-null   object 
    6   Region                  5532 non-null   object 
    7   Model                   5532 non-null   object 
    8   Price                   5532 non-null   float64
    9   Engine_Power            5532 non-null   int64  
    10  Transmission            5532 non-null   object 
    11  Fuel_Type               5532 non-null   object 
    12  Num_Additional_Options  5532 non-null   int64  
   dtypes: float64(2), int64(5), object(6)
   memory usage: 562.0+ KB
   ```
- I will not use df_joined.convert_dtypes().dtypes to auto convert dtypes of object columns. Instead I will convert dtype for each colum to get the corect dtype for each cloumn:
```
# Convert columns with object dtype to category:

df_joined['purchase_id'] = df_joined['purchase_id'].astype('string')
df_joined['id'] = df_joined['id'].astype('string')

# Convert columns with object dtype to category (if they are categorical variables)

df_joined['Sex'] = df_joined['Sex'].astype('category')
df_joined['Region'] = df_joined['Region'].astype('category')
df_joined['Model'] = df_joined['Model'].astype('category')
df_joined['Transmission'] = df_joined['Transmission'].astype('category')
df_joined['Fuel_Type'] = df_joined['Fuel_Type'].astype('category')

# convert object to datetime  type:
df_joined['Purchase_Date'] = pd.to_datetime(df_joined['Purchase_Date'], format="%Y-%m-%d", errors='coerce')
```
... then check the df.info() again to see the result:

```
# check dtype after convert dtypes:
df_joined.info()

# >> result:
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 5532 entries, 0 to 5531
Data columns (total 13 columns):
 #   Column                  Non-Null Count  Dtype         
---  ------                  --------------  -----         
 0   purchase_id             5532 non-null   string        
 1   id                      5532 non-null   string        
 2   Age                     5532 non-null   int64         
 3   Sex                     5532 non-null   category      
 4   Income                  5532 non-null   float64       
 5   Purchase_Date           5532 non-null   datetime64[ns]
 6   Region                  5532 non-null   category      
 7   Model                   5532 non-null   category      
 8   Price                   5532 non-null   float64       
 9   Engine_Power            5532 non-null   int64         
 10  Transmission            5532 non-null   category      
 11  Fuel_Type               5532 non-null   category      
 12  Num_Additional_Options  5532 non-null   int64         
dtypes: category(5), datetime64[ns](1), float64(2), int64(3), string(2)
memory usage: 374.6 KB
```
d/ Removing Duplicates: Check Number of Duplicate Rows using df.duplicated().sum()
- I run the code . to see whether or not my columns contain duplicates:
  ```
  df_joined.duplicated().sum()

  # >> result
  0
  ```
There is no duplicates so that I don't need to do anything else for this step and assume that my dataframe has no duplicate.

e/ Handling Missing Values: using df.isnull().sum(); this dataset has no missing value so I do not need to do this step.
- I run the below code to check if the df has mising values:
  ```
  # check mising value:
  df_joined.isnull().sum()
  ```
and the result:

|                        | 0 |
|-----------------------:|---|
|       purchase_id      | 0 |
|           id           | 0 |
|           Age          | 0 |
|           Sex          | 0 |
|         Income         | 0 |
|      Purchase_Date     | 0 |
|         Region         | 0 |
|          Model         | 0 |
|          Price         | 0 |
|      Engine_Power      | 0 |
|      Transmission      | 0 |
|        Fuel_Type       | 0 |
| Num_Additional_Options | 0 |
| dtype: int64           |   |


f/ Summary Statistics for Numerical Columns and Non-Numeric Columns:
- I use the below code to see basic desctiptive analysic abot the Numerical Columns and Non-Numeric Columns in this df.

```
# Summary Statistics for Numerical Columns:
df_joined.describe()

```
and the result:

|       |         Age |       Income |                 Purchase_Date |        Price | Engine_Power | Num_Additional_Options |
|------:|------------:|-------------:|------------------------------:|-------------:|-------------:|-----------------------:|
| count | 5532.000000 | 5.532000e+03 |                          5532 | 5.532000e+03 |  5532.000000 |            5532.000000 |
|  mean |   50.267354 | 4.257746e+05 | 2022-06-14 22:20:18.221258240 | 1.946231e+06 |   117.060557 |               1.663774 |
|  min  |   25.000000 | 2.400000e+05 |           2021-01-01 00:00:00 | 7.000000e+05 |    87.000000 |               0.000000 |
|  25%  |   46.000000 | 2.590000e+05 |           2021-09-01 00:00:00 | 1.300000e+06 |   102.000000 |               1.000000 |
|  50%  |   51.000000 | 3.660000e+05 |           2022-06-16 00:00:00 | 1.950000e+06 |   117.000000 |               1.000000 |
|  75%  |   56.000000 | 5.110000e+05 |           2023-04-01 00:00:00 | 2.562500e+06 |   132.000000 |               2.000000 |
|  max  |   63.000000 | 1.943000e+06 |           2023-12-01 00:00:00 | 3.200000e+06 |   147.000000 |               5.000000 |
|  std  |    7.046628 | 2.101793e+05 |                           NaN | 7.369210e+05 |    18.667676 |               1.321465 |

```
# Summary Statistics for Non-Numeric Columns

# Filter non-numeric (categorical) columns
non_numeric_df_joined = df_joined.select_dtypes(include=['string', 'category'])

# Get the summary statistics for non-numeric columns and display:
non_numeric_df_joined.describe()
```

and the result:

|        | purchase_id |     id |  Sex |                  Region |  Model | Transmission | Fuel_Type |
|-------:|------------:|-------:|-----:|------------------------:|-------:|-------------:|----------:|
|  count |        5532 |   5532 | 5532 |                    5532 |   5532 |         5532 |      5532 |
| unique |        5532 |   5000 |    2 |                      14 |     20 |            2 |         2 |
|   top  |           1 | 198899 |    M | Республика Башкортостан | Granta |           MT |    Petrol |
|  freq  |           1 |      3 | 5025 |                     821 |    314 |         3827 |      4983 |

g/ Checking consistency: 
- I decide to check and Fix any Inconsistencise of Non-Numeric column in df:

```
# check to see all the colum of this df
df_joined.columns

# >> result:

Index(['purchase_id', 'id', 'Age', 'Sex', 'Income', 'Purchase_Date', 'Region',
       'Model', 'Price', 'Engine_Power', 'Transmission', 'Fuel_Type',
       'Num_Additional_Options'],
      dtype='obje
```
- Then I use a function called check_unique_values(df) . tocheck the number of unique value in each colum:
```
# Define a function to check unique values for each column in the DataFrame
def check_unique_values(df):
    # Iterate over each column and print the number of unique values
    unique_values = df.nunique()

    # Display the result
    for column, unique_count in unique_values.items():
        print(f"Column '{column}' has {unique_count} unique values.")

# Example usage with df_joined
check_unique_values(df_joined)

# >> result:
'''
Column 'purchase_id' has 5532 unique values.
Column 'id' has 5000 unique values.
Column 'Age' has 39 unique values.
Column 'Sex' has 2 unique values.
Column 'Income' has 777 unique values.
Column 'Purchase_Date' has 36 unique values.
Column 'Region' has 14 unique values.
Column 'Model' has 20 unique values.
Column 'Price' has 51 unique values.
Column 'Engine_Power' has 13 unique values.
Column 'Transmission' has 2 unique values.
Column 'Fuel_Type' has 2 unique values.
Column 'Num_Additional_Options' has 6 unique values.
'''
```
- Then, i change the items in such categorical columns, that might possibly contains inconsistent values, into lower letter and check the unique values again

```
# Check consitency of col='Region' by counting the unique values after I change all items in col='Region' to lower letter

df_joined['Region'] = df_joined['Region'].str.lower()
len(df_joined['Region'].unique())

# >> result: 14

# the number of unique values unchanges >> no inconsistent values

# check consitency of col='Model' by counting the unique values after I change all items in col='Model' to lower letter

df_joined['Model'] = df_joined['Model'].str.lower()
len(df_joined['Model'].unique())

# >> result: 20

# the number of unique values unchanges >> no inconsistent values
```
After the checking, I noticed that th df does not contain inconsistent colum

g/ Outlier Detection and Handling:

- From the descriptive analysis above, I check all the colums to see if I need to remove outliers for any colums.
- The age colum contain min values at 25 and max values at 65. This is reasonably match with the range of human being age so I don't need to remove outliers for this column:
- I notice the Income column might contain outliers since in the customer group, there might have some individual with extraordinary income range. Hence, I use IQR to dèine lower and upper bound; and boxplot as well to check.

```
# Find IQR df_joined['Income'] :

# Quartiles calcualtion
Q1 = df_joined['Income'].quantile(0.25)
Q3 = df_joined['Income'].quantile(0.75)

# IQR calculation
IQR = Q3 - Q1

# Lower and Upper bounds for non-outliers calculating
lower_bound = Q1 - 2 * IQR # dùng 2 * IQR thay vì 1.5 * IQR để  hạ thấp lower bound
upper_bound = Q3 + 2 * IQR # dùng 2 * IQR thay vì 1.5 * IQR để nâng cao upper bound

cond1 = df_joined['Income'] >= lower_bound
cond2 = df_joined['Income'] <= upper_bound

df_joined_clean = df_joined[cond1 & cond2]

# check summary of df_joined afer remove outliers of df_joined['Income']
df_joined_clean.info()

# >> result:

<class 'pandas.core.frame.DataFrame'>
Index: 5419 entries, 0 to 5531
Data columns (total 13 columns):
 #   Column                  Non-Null Count  Dtype         
---  ------                  --------------  -----         
 0   purchase_id             5419 non-null   string        
 1   id                      5419 non-null   string        
 2   Age                     5419 non-null   int64         
 3   Sex                     5419 non-null   category      
 4   Income                  5419 non-null   float64       
 5   Purchase_Date           5419 non-null   datetime64[ns]
 6   Region                  5419 non-null   object        
 7   Model                   5419 non-null   object        
 8   Price                   5419 non-null   float64       
 9   Engine_Power            5419 non-null   int64         
 10  Transmission            5419 non-null   category      
 11  Fuel_Type               5419 non-null   category      
 12  Num_Additional_Options  5419 non-null   int64         
dtypes: category(3), datetime64[ns](1), float64(2), int64(3), object(2), string(2)
memory usage: 481.9+ KB

```
The df_joined_clean now contains 5419 entries, 113 unit less incomparison to the df_joined.

```
# Số lượng outlier được loại bỏ ra khỏi model:
outlier_num = df_joined['purchase_id'].count()-df_joined_clean['purchase_id'].count()

# tỉ trọng outlier trong dataset
outlier_per = outlier_num / df_joined['purchase_id'].count() * 100

print('Number of Outliers: ', outlier_num)
print('Percentage of Outlier among the whole dataset: ', outlier_per)

# >> result:
Number of Outliers:  113
Percentage of Outlier among the whole dataset:  2.0426608821402747
```
Next, I check the boxplot to see . the difference:

```
# boxplot to check outliers of 'Income','Price' : dataframe trước khi loại bỏ outliers

df_joined.boxplot(column=['Income','Price'])
```
![Image](https://github.com/user-attachments/assets/cd89344a-8ba6-483e-aace-b154a4df71c4)

```
# boxplot của dataframe sau khi đã bỏ đi các outlier:
df_joined_clean.boxplot(column=['Income','Price'])
```
![Image](https://github.com/user-attachments/assets/5cf4b99a-6a71-4206-ada9-a7c290b3e9fc)

From the result: the df_joined_clean have less outlers for further analysis.

#### **Exploratory Data Analysis (EDA):**

- I have a question that How many years that a customer (in this dataset) need to afford a car, asuming that his/ her income does not change over years? So that I add . aclumn for futher analysis:

```
# Add 1 more colum: Number of saving year to afford a model:
df_joined_clean['Saving_year_num'] = df_joined_clean['Price'] / df_joined['Income']
```
- Then I create visualizations to uncover patterns and relationships : correlation, histograms, pair plots, correlation:

```
# Check correlation between numeric coumns:
df_numeric_cols = df_joined_clean.select_dtypes('number')

# calculate correlation matrix of numeric columns in df_joined_clean:
correlation_matrix = df_numeric_cols.corr()
correlation_matrix
```
>> Result:

|                        |       Age |    Income |     Price | Engine_Power | Num_Additional_Options | Saving_year_num |
|-----------------------:|----------:|----------:|----------:|-------------:|-----------------------:|----------------:|
|           Age          |  1.000000 |  0.025615 |  0.012572 |    -0.008720 |               0.011194 |       -0.005341 |
|         Income         |  0.025615 |  1.000000 | -0.004596 |     0.005928 |               0.020252 |       -0.611459 |
|          Price         |  0.012572 | -0.004596 |  1.000000 |    -0.014953 |              -0.009871 |        0.713459 |
|      Engine_Power      | -0.008720 |  0.005928 | -0.014953 |     1.000000 |              -0.014891 |       -0.006256 |
| Num_Additional_Options |  0.011194 |  0.020252 | -0.009871 |    -0.014891 |               1.000000 |       -0.015783 |
|     Saving_year_num    | -0.005341 | -0.611459 |  0.713459 |    -0.006256 |              -0.015783 |        1.000000 |


```
# Visualizing the Correlation:
plt.figure(figsize=(10,8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm')
plt.title('Correlation Matrix Heatmap')

plt.show()
```
Result: 

![Image](https://github.com/user-attachments/assets/233816b7-54cc-4600-8484-057ec9c2a522)

Using pairplot:

```
# Pairplot
import seaborn as sn

sn.pairplot(df_joined_clean)
plt.show()
```
And the result:

![Image](https://github.com/user-attachments/assets/3b3ea33d-8448-4858-8b0e-96e33eaf5c55)

- For details, I also check the scatter plot for some specific colums:

```
# Scatter plot for Income vs Saving_year_num:

plt.figure(figsize=(10,10))
sns.scatterplot(data=df_joined_clean, x='Saving_year_num', y='Income')

plt.title('Scatter plot for Income vs Saving_year_num')

plt.show()
```

Result:

![Image](https://github.com/user-attachments/assets/bce8723c-dc6d-4061-b250-c7d25a1766fc)

```
# Scatter plot for Price vs Saving_year_num:
plt.figure(figsize=(10,10))
sns.scatterplot(data=df_joined_clean, x='Saving_year_num', y='Price')

plt.title('Scatter plot for Price vs Saving_year_num')

plt.show()
```

Result:
![Image](https://github.com/user-attachments/assets/2b128abc-e439-4d0e-8c0d-6b568fce9e8b)

- I also draw histomgram to see in distribution of gender in the dataset:

```
# plot histogram for colum Sex
plt.figure(figsize=(5,5))

plt.hist(df_joined_clean['Sex'], bins=10, color='tan', edgecolor='white')

# set title
plt.title('Histogram for Sex')
plt.xlabel('Sex')
plt.ylabel('Frequency')

# show
plt.show()
```
Result: 
![Image](https://github.com/user-attachments/assets/9a55e0ee-0eba-48ff-b3df-b740f731e66b)


Then I export the dataframe to csv for further analysis and visualization in Power BI. The files are stored in this Github repo of mine.

```
# export the dataframe to csv file for EDA in Power BI
df_joined_clean.to_csv('lada_sales_clean_df.csv', index=False)

# export the dataframe to csv file for EDA in Power BI
df_joined.to_csv('lada_sales_df.csv', index=False)
```


#### Analysis:

### **1. Sales Performance Analysis**

- **Sales Trend Analysis**:
    - Breakdown of total sales for each year (2021, 2022, 2023).
        - Total sales of LADA over 3 years from 2021 until 2023 is 1817, 1804 and 1798
    - Comparison of year-over-year sales performance.
        - Sale performance of each year seems to have the same seasonal patterns. .
        - Sales numbers do not change too much between the 3 years. There is a slightly decline in total sales over years. This might be an serious negative impact of Covid-19 pandemic and economic downturns.
    - Monthly sales trends to identify seasonal patterns.
        - Sales number is normally on down trend during the 1st quarter until it reach the lowest sales in April, around 100 order.
        - In every year, April is likely to have the lowest sales and revenues. The situation seems to be improved after April and the sales begin to increase again until they reach the peak in June or July. After August, the sales decrease but the demand for car is still there.
        - Among the last quarter of year, demand for car seems not to be high leads to a decrease in sales. October seems to have the most orders among the 3 months. November usually the second lowest month in sales. After December, the trend is slightly go up again until annual total sales reach the minor peak in the beginning of 1st quarter.
- **Sales by Region**:
    - Identify which regions in the Volga Federal District have the highest and lowest sales. - done
        - **Republic of Bashkortostan, Republic of Tatartsan** has highest sales in every years
        - **Republic of Mari El** has the lowest sales in every years
    - Regional growth or decline in sales over the years.
        - The first, second runner-up and last rank in the list are unchanged.
        - **Republic of Bashkortostan** has their sales increased in2022 and then slightly decreased in the next year. However, in February 2021 the sales dropped unexpectedly in comparison to the result of the previous month. It happened the same with sales total in December of 2023, the sales dramatically declined in comparison to the same period of the previous 2 years.
        - **Republic of Tatartsan**  is at the second rank over 3 years. Their sales number is closed to the first rank in 2021. The gap is much bigger in 2021 even though Republic of Tatarstan’s sales only slightly decreased in 2022. Its total sales finally boosted in 2023.
        - **Republic of Mari El** has a slightly increase in sales. In January and May 2022, they have extremely good sales increase in comparison to other months in year. This region seems to have lowest sales on March, September and October.
- **Sales by Model**:
    - Analyze which LADA car models are the most popular.
        - Across all 20 Model, total sales ranged from 241 to 312. The taste for Model of LADA over years has changed. In 2021. it’s granta that has the highest unit sold; in 2022, it’s 2113, and in 2023 it’s 2107 having the highest sales.
        - Model granta had the highest sales at 312, followed by 2107 and 2113. 2111 had the lowest sales at 241. ﻿At 312 units sold, granta had the highest total sales and was 29.46% higher than 2111, which had the lowest sales at 241 unit sold.
        - In total the of 3 years, granta, 2107 and 2113 are the models bought the most by customers; accounting for nearly 16.78% sales of LADA .
- **Sales by Transmission Type (MT vs AT)**:
    - Evaluate sales split between manual and automatic transmission cars.
        - Total sales for MT (3,740) was higher than AT (1,679) in over 3 years and in almost regions.
        - MT accounted for 69.02% of total sales while AT accounted for 30.98% of total sales
    - Examine trends in consumer preference for transmission type over the years. :
        - In general, the consumer preference for transmission type over the years does not change from 2021 until 2023.

---

### **2. Revenue Analysis**

- **Revenue by Year**:
    - Calculate total revenue from car sales for 2021, 2022, and 2023.
        - Total revenue of LADA over 3 years is 10.5 billion; in details :  ~ 3.5 billion in 2021, a bit over 3.5 billion 1804 and ~ 3.48 billion in 2023.
    - Year-over-year revenue growth.
        - In general, decline in sales and impact of general economic downturns lead to a slightly decrease in revenue over years.
- **Revenue by Region**:
    - Breakdown of revenue by region within the Volga Federal District.
        - For most of the years, **Republic of Bashkortostan, Republic of Tatartsan** and **Nizhny Novgorod Oblast** contributed to the most revenue of  LADA, with total revenue around 4.2 billion in 3 years.
        - **Republic of Mari El** contributed the least to total revenue of LADA
- **Revenue by Car Model**:
    - Analyze revenue generated by LADA models.
        - Over the 3 years, granta, 2107 and 2113 are the models brought back the most revenue to LA; accounting for nearly 16.86% revenue of LADA .
    - Compare high and low-revenue models and their contribution to overall sales.
        - While 2111 seems to be the least favorite model of LADA, with 241 cars sold and returned 4.44% revenue of LADA
- **Revenue by Transmission Type**:
    - Calculate the contribution of MT vs AT transmission cars to total revenue.
        - Total revenue for MT (around 2.3 billion to 2.5 billion) is likely to be double of AT’s total revenue (around 1.1 billion over the years.)

---

### **3. Customer Demographics and Analysis**

- **Age Demographics**:
    - Breakdown of customer age groups (e.g., 18–25, 26–35, etc.) :
        - Most of the customers are in age range from 45 - 55 years old.
        - Group of age bin 50 has the highest number of customer at 1466 people, accounting for 27.05% of total sales , following is age bin 55 with 1343 people, accounting for 24.78%; and the third largest age group is 45-year-old with 1131 people, accounting for 20.87 % of total sales.
        - People in age from 25 to 35 contributed the lease to total sales in 3 years; with 25 customer in age 25, accounting for 0.42%; 113 customer in age 30, accounting for 2.09% of sales and 315 customers in age of 35, accounting for 5.81% of total sales.
    - Trends in the age of buyers over the three years. - done in cus ana
        - We tend to have more sales for customer in age 50 to 55 in 2021 and 2023.
        - In 2023, most of the buyers in age 49, 52 and 55.
        - Sales from customer under 39 and above 58 decreased in 2022 and 2023.
    - Correlation between age and car model preferences. - how?
        - From the correlation matrix, there is very weak relationships between age and model.
        - From the analysis, customer in age bin of 45,50 and 55 seems to like models such as: granta (in 2021), samara and 2109 (in 2022); 2107, riva and 2113 in 2023.
        
- **Gender Analysis**:
    - Gender split of customers (Male vs Female).
        - 90.87% customers are males and 9.13% customers are female.
        - Largest number of female customers are living in Republic of Tatartsan (77 among 495 customers)
    - How the gender split changes over time and across regions.
        - There is not so much change in the ratio between Male and Female customers acrocss regions over time since the annually percentage of Female customers always low, hardly exceeds 30 customers.
- **Income Analysis**:
    - Distribution of customer income levels.
        - Most of the customers have income in range under 300,000 RUB per year, with 600 id and accounting for 15%.
        - Following is 429 customers with income in income bins of 300,000 RUB (accounting for ~7,9 % of total) and 305 customers in bins of 400,000 RUB (accounting for ~ 5.6% of total)
        - Only 29 customer with income in bins 900,000 RUB and 4 people in bins 1,000,000 RUB. Among these 4 people, 2 customers have age above 50 and 2 other in age bin 35.
    - Correlation between income and car purchase price.
        - From the chart: Customers in age of 28 to 35 tend to spend money for expensive models; thus the revenue for these age groups usually higher than other age groups, even in the Covid-years (2021-2022). In 2022, the average income of young people under 30 significantly increased and then turned back to normal level as before 2022.
    - Changes in purchasing behavior based on income levels.
        - There is not so much change in this shopping behavior since young people tend to spend most of their money to high-end and shopping while the older people tend to spend for more affordable models and keep their monies for saving and investment plans.
- **Saving Year Analysis**:
    - Average number of years customers saved for the car is 1.66 year . This does not change much over the years.
    - Does saving duration vary by income, age, or car model? - how and which chart?
        - Income and Number of saving year to afford a car model have strong negative correlation. in opposite, Price of Model and Saving year number have strong positive correlation.
        - Younger people tend to have longer of saving year to afford a model; This might be because their annual income is not so high but the model they buy is more expensive. On the other hand, the older ones with higher income level and less expensive model purchased shall need less year to afford their car model.
    - **Customer Segmentation**:
        - **Age-Based Segmentation**
            - **Segment 1: 45–55 Years Old (Primary Target)**
                - **Characteristics**:
                    - Accounts for over 70% of total sales.
                    - Predominantly prefers affordable, reliable models such as **Granta**, **2107**, and **2113**.
                - **Strategy**:
                    - Focus on promoting **value-for-money models** that emphasize **reliability** and **practicality**.
                    - Tailor offers for long-term savings and financing options.
            - **Segment 2: 25–35 Years Old (Young & Premium Buyers)**
                - **Characteristics**:
                    - Contributes the least to total sales but is willing to spend on **higher-end models**.
                    - Prefers **premium models** with advanced features.
                - **Strategy**:
                    - Market **premium and feature-rich models** through **digital platforms** like Instagram and TikTok.
                    - Provide **leasing or trade-in options** to attract these buyers despite their lower income.
            - **Segment 3: Under 25 and Over 58 Years Old (Low Priority Segments)**
                - **Characteristics**:
                    - These groups contribute minimally to total sales.
                - **Strategy**:
                    - Limited focus, but special **affordable models** and incentives could be used to attract them.
        - **Income-Based Segmentation**
            - **Segment 1: Low-Income (Under 300,000 RUB)**
                - **Characteristics**:
                    - Largest proportion of customers with modest purchasing power.
                    - Prefers **affordable models** like **Granta** and **2107**.
                - **Strategy**:
                    - Offer **budget-friendly models** with **affordable financing** options, **installment plans**, and **discounts**.
            - **Segment 2: Mid-Income (300,000–900,000 RUB)**
                - **Characteristics**:
                    - A significant group, likely to purchase models with a balance between affordability and features.
                - **Strategy**:
                    - Promote **mid-range models** and emphasize **value** and **cost-effectiveness**.
            - **Segment 3: High-Income (900,000–1,000,000 RUB)**
                - **Characteristics**:
                    - Prefers **premium models** and has higher spending power.
                - **Strategy**:
                    - Create exclusive offers for high-end models with **premium features**, **customization options**, and **VIP after-sales services**.
        - **Region-Based Segmentation**
            - **Segment 1: High-Performing Regions (Bashkortostan, Tatarstan, Nizhny Novgorod Oblast)**
                - **Characteristics**:
                    - These regions generate the highest revenue and account for a large portion of total sales.
                - **Strategy**:
                    - Strengthen dealership presence and launch **region-specific promotions**.
                    - Focus on **community events** and **local loyalty programs** to maintain strong sales in these areas.
            - **Segment 2: Low-Performing Regions (Mari El, and other underperforming regions)**
                - **Characteristics**:
                    - These regions contribute less to total revenue.
                - **Strategy**:
                    - Investigate reasons for low sales through **market research**.
                    - Tailor promotions, advertising, and dealership expansion in these regions to increase visibility and engagement.
        - **Car Model Preference Segmentation**
            - **Segment 1: Affordable Model Buyers (Granta, 2107, 2113)**
                - **Characteristics**:
                    - These models dominate sales and are preferred by customers in the 45–55 age group, especially those with lower incomes.
                - **Strategy**:
                    - Promote these models heavily in **budget-conscious segments** with an emphasis on **reliability** and **value for money**.
                    - Offer **flexible payment plans** for those who need assistance with financing.
            - **Segment 2: Premium Model Buyers (High-End Models)**
                - **Characteristics**:
                    - Young, high-income buyers in the 25–35 range are more likely to purchase higher-end models.
                - **Strategy**:
                    - Focus marketing efforts on **premium models**, showcasing features like **advanced technology** and **customization options**.
                    - Highlight **premium experience** and **luxury** to appeal to this segment.
    
    ---
    

### **4. Purchase Behavior and Preferences**

- **Fuel Type Preferences (Gasoline vs Diesel)**:
    - Breakdown of fuel type preferences.
        - Total sale of Fuel type (4882 units sold, accounting for 90.09%) is much higher than Diesel type ((537 units sold, accounting for 9.91%)
    - Shifts in fuel type preference over time. - done in
        - There is no change in the preference regarding fuel type over 3 years.
- **Additional Options**:
    - Customers tend to add 1-2 additional option or none of option to their purchase of car. Over 3 years, only 177 purchase orders having 5 additional options.

---

### **5. Model-Specific Insights**

- **Engine Power and Price**:
    - Correlation between engine power and the purchase price.
        - Engine power and Car Price have weak negative of correlation
    - Analyze customer preferences for car engine power across the years.
        - Customers tend to choose engine power of 137 and 142 in 2021, 102 and 117 in 2022; 147, 132 and 122 in 2023.
- **Price Range Analysis**:
    - Price segmentation and its impact on sales volume. - price bins and order count
        - 1084 of the sales is in price bin of 2,000,000, accounting for 20% of total sales; following by 1067 (19.69%) order in price bin of 1,000,000 and 1063 (19.62%) orders in price bin of 1,500,000

#### Visualization:

Using Power BI dashboard to visualize key metrics that I studied from the dataset.

Display the relationship between columns and metrics using relevant charts and histograms

Cards: 

Bar charts : 

Columns/ Cluster charts:

Pie charts:

Line chart:

 

#### **Data Storytelling**

Presentation method: Using PowerPoint file and Power BI

## **F - Outcome**

### **1. Recommendations**

- **Sales Strategy**:
    - Suggest strategies to boost sales in underperforming regions.
        - According to the sales and revenue analysis, LADA should focus their resources (human, facilities, outlets…) to the 3 biggest regions they occupied :  **Republic of Bashkortostan, Republic of Tatartsan** and **Nizhny Novgorod Oblast.** They also need to investigate the root cause for sales declined in these 3 regions in such summer months (Mar, April and May)
        - Since that **Republic of Mari El** is the region having least contribute to sales or revenue of the whole company, LADA should also consider on whether or not they should close the outlets in this region. In case they does not want to loose this region to other rivals, LADA should have ,much more efficient solutions to investigate the root cause for sales decrease in some specific months of year; and improve sales performance of this region as well.
    - Recommendations for focusing on high-demand models or transmission types.
        - Customer usually place orders for MT type rather than AT type so that LADA should consider this point in making sales & marketing plans for promotion of their models or find out solutions to boost the sales of AT transmission type cars.
        - For granta:
            - **Recommendation**: Since granta has consistently performed well, with the highest sales in 2021 and a significant share of total sales (29.46% higher than the lowest-selling model), it is crucial to continue promoting granta as a flagship model due to its popularity. Consider increasing its production capacity or improving availability in regions with higher demand.
            - **Action**: Launch targeted marketing campaigns emphasizing Granta's value proposition. Highlight its affordability and popularity to attract a broader customer base, especially in regions where it already performs well.
        - For the 2 model 2107 and 2113 :
            - **Recommendation**: Both models have shown strong sales performance over the past three years. Maintain focus on these models by refining their features, offering more customization options, and potentially introducing limited-edition variants to keep the demand high.
            - **Action**: Expand distribution of 2107 and 2113 in high-performing regions. Introduce loyalty programs or trade-in offers to keep existing customers engaged with these models.
        - For 2111 Model
            - **Recommendation**: With only 241 units sold, the 2111 model has the lowest sales and may not be resonating well with customers. Evaluate the reasons for its poor performance — whether it's due to pricing, lack of features, or shifting customer preferences.
            - **Action**: Consider rebranding or redesigning the 2111 model, focusing on improving its key selling points (e.g., fuel efficiency, engine power, or interior features). If the model is not delivering satisfactory returns, it may be worth phasing it out or reducing its presence in the market. Alternatively, reposition the 2111 model as a more affordable entry-level option if the customer segment is price-sensitive.
    - Trend Monitoring to Adapt to Market Shifts:
        - **Recommendation**: The taste for LADA car models has shifted over the years. Continuous monitoring of sales data, customer reviews, and competitor activities will allow LADA to stay ahead of changing market demands and adapt its models accordingly.
        - **Action**: Set up a system for quarterly reviews of car model performance to quickly identify trends. Adapt production schedules, marketing efforts, and distribution channels based on this data.
- **Revenue Optimization**:
    - *Diversify the Model Portfolio by Product Line Expansion:* ****
        - **Recommendation**: To capture a broader customer base, LADA should consider expanding its portfolio to cater to different segments (e.g., electric vehicles or hybrids) or introducing models with higher-end features to appeal to premium buyers.
        - **Action**: Use customer feedback, regional preferences, and competitor analysis to introduce new variants or models that fill market gaps. For example, focusing on eco-friendly vehicles could align with global shifts toward sustainability.
    - *Tailor Marketing and Sales Efforts:*
        - **Recommendation**: Given that customer preferences for LADA models vary by year and region, it is crucial to implement a region-specific marketing strategy. For example, in regions where the granta and 2107 perform well, further emphasize their cost-effectiveness and dependability.
        - **Action:** Use sales data to craft targeted campaigns in regions where specific models are popular.
    - *Enhance Customer Experience and Engagement - Think about customer Retention Programs:*
        - **Recommendation**: Given that granta, 2107, and 2113 models contribute significantly to total sales, it’s essential to create a loyal customer base for these models. Introduce post-purchase services, loyalty rewards, and referral programs to encourage repeat purchases and word-of-mouth marketing.
        - **Action**: Offer complimentary services (e.g., free maintenance for the first year) for buyers of popular models. Implement a customer satisfaction survey to identify areas for improvement.
    - *Focus on High-Revenue Models (granta, 2107, and 2113)*
        - **granta, 2107, and 2113**: These models have consistently generated the most revenue for LADA, accounting for 16.86% of total revenue over the 3 years. They are the most successful models in terms of sales and profitability.
        - **Recommendation**: Increase the production of these high-performing models to meet the demand. Focus on enhancing their features, quality, and customer satisfaction to maintain and potentially grow their revenue contribution.
        - **Action**:
            - Launch targeted marketing campaigns focusing on the value and affordability of Granta, 2107, and 2113, especially in regions with high demand.
            - Offer exclusive accessories, upgrades, and limited-edition variants to increase the average selling price (ASP) of these models.
            - Expand sales channels to reach more customers, including online sales platforms and regional dealerships.
    - *Reevaluate and Potentially Phase Out Low-Revenue Models (e.g., 2111):*
        - **2111 Model**: The 2111 has underperformed, contributing just 4.44% to total revenue with only 241 units sold.
        - **Recommendation**: Consider rebranding, updating, or discontinuing the 2111 model. If the model continues to underperform, it may be more cost-effective to phase it out and focus on more profitable models.
        - **Action**:
            - Analyze customer feedback to identify why the 2111 model is unpopular (e.g., pricing, features, or consumer preferences).
            - If a redesign is feasible, update the 2111 with newer features, a more attractive price point, or improved performance to boost its appeal.
            - Alternatively, reallocate resources to the more successful models and reduce inventory for the 2111.
    - *Target High-Revenue Regions for Growth:*
        - **Republic of Bashkortostan, Republic of Tatarstan, Nizhny Novgorod Oblast**: These regions have contributed significantly to revenue, with a total of 4.2 billion over 3 years.
        - **Recommendation**: Focus on deepening LADA's presence in these regions, which already account for a substantial portion of total revenue. Develop region-specific strategies to boost sales further.
        - **Action**:
            - Expand promotional efforts in these regions, potentially with localized advertisements or partnerships with local influencers to boost brand visibility.
            - Offer region-specific deals and incentives for loyal customers (e.g., discounts for repeat buyers or trade-in options).
            - Open more showrooms or increase distribution networks to ensure LADA models are easily accessible in high-performing regions.
    - *Explore Underperforming Regions (e.g., Republic of Mari El):*
        - **Republic of Mari El**: This region has contributed the least to total revenue.
        - **Recommendation**: Investigate why this region has underperformed. It could be due to low brand awareness, economic conditions, or a mismatch between LADA's offerings and local preferences.
        - **Action**:
            - Conduct market research to better understand consumer behavior and preferences in Mari El. Adjust product offerings, pricing strategies, or marketing messages to meet local needs.
            - Offer special promotions, such as price reductions or financing options, to stimulate demand.
            - Consider localized marketing campaigns, emphasizing LADA's value proposition and catering to the specific preferences of the Mari El population.
    - *Capitalize on Transmission Type Preferences (MT vs AT):*
        - **Recommendation**: Since automatic transmission (AT) is preferred, focus on increasing its availability and improving its appeal.
        - **Action**:
            - Increase production of AT models if they are generating higher revenue and have greater demand. Ensure that the supply of AT models matches the growing demand.
            - If manual transmission (MT) models are still significant in certain regions, maintain a balanced offering to cater to both preferences.
            - Promote the benefits of AT cars (e.g., ease of driving, fuel efficiency, and advanced technology) in regions where there is strong demand for automatic vehicles.
    - *Increase Average Selling Price (ASP):*
        - **Recommendation**: To offset revenue declines due to lower overall sales, focus on strategies to increase the average selling price (ASP) of LADA vehicles.
            - **Action**:
                - Introduce additional options, customizations, or premium versions of popular models, which can drive up the ASP.
                - Develop attractive financing and leasing options to make higher-priced models more accessible to customers.
                - Offer add-on services, such as extended warranties, maintenance packages, and insurance, to increase the total transaction value per customer.
    - *Revenue Diversification:*
        - **Recommendation**: Explore ways to diversify LADA's revenue streams beyond car sales, especially in light of economic downturns affecting revenue growth.
        - **Action**:
            - Develop and promote a strong after-sales service network to increase service-related revenue (e.g., maintenance, repairs, accessories).
            - Explore the introduction of LADA-branded merchandise, accessories, or extended warranties to capture additional revenue from existing customers.
            - Investigate the potential for LADA to enter electric vehicle (EV) or hybrid car markets. Of course this might severely affect the production cost but , in return, this may help LADA catch-up with latest technology trends, attract new customer segments and increase revenue potential.
    - *Adjust Marketing Strategies for Revenue Maximization:*
        - **Recommendation**: Review marketing campaigns and optimize them for better revenue generation. Promotions and campaigns should target high-revenue models and high-performing regions.
        - **Action**:
            - Increase the use of digital marketing channels (e.g., social media, email marketing, and online ads) to reach potential buyers, particularly those interested in the more expensive or high-demand models.
            - Offer limited-time promotions, flash sales, or bundling deals (e.g., discounts on additional features or accessories) to boost sales volumes and revenue.
- **Customer Targeting**:
    - **Age-Based Customer Segmentation and Targeting**
        - **Target the 45–55 Age Group**:
            - **Key Insight**: The largest proportion of customers (27.05% of total sales) falls into the 50-year-old age bin, followed closely by the 55-year-olds (24.78%) and 45-year-olds (20.87%). This age group is more likely to purchase affordable and practical models such as **Granta, Samara, 2109, 2107, Riva**, and **2113**.
            - **Recommendation**:
                - Focus marketing campaigns, offers, and advertisements on models like **Granta, 2107, and 2113**, as these resonate most with customers in this age range.
                - Emphasize the reliability, practicality, and affordability of the car models in promotional materials, as this age group is often interested in cost-effective and dependable vehicles.
            - **Action**:
                - Offer tailored promotions such as **long-term financing** options or **loyalty discounts** for customers in this age group, as they may be more interested in long-term savings.
        - **Focus on Younger Customers (Under 35)**:
            - **Key Insight**: Customers aged 25–35 contributed the least to total sales, but they tend to spend more on high-end models (as seen in the 2022 trend). Despite their low overall numbers, this group has a higher purchasing power in terms of spending on more expensive models.
            - **Recommendation**:
                - Create specific campaigns to attract this younger demographic by promoting **higher-end models** with advanced features.
                - Utilize **digital marketing** and **social media platforms** that appeal to a younger audience (e.g., Instagram, TikTok, YouTube) to raise brand awareness and engagement.
            - **Action**:
                - Position LADA as a modern, tech-savvy brand with appealing features for younger buyers. Offer flexible payment options, like leasing or trade-in schemes, that appeal to a younger audience with possibly lower disposable income but a willingness to spend on premium models.
    - **Gender-Based Customer Segmentation and Targeting**
        - **Male Customers**:
            - **Key Insight**: 90.87% of LADA's customer base is male, with consistent sales across regions over time.
            - **Recommendation**:
                - Tailor marketing strategies toward male customers, who represent the majority of buyers. Focus on the ruggedness, reliability, and practical aspects of the vehicles in campaigns.
            - **Action**:
                - Highlight features like engine power, durability, and performance, as these qualities may resonate well with male buyers.
        - **Female Customers**:
            - **Key Insight**: Female customers represent a small portion of the customer base (9.13%), but they are concentrated in specific regions like **Republic of Tatarstan**.
            - **Recommendation**:
                - Consider creating targeted marketing campaigns in regions with higher female customer concentration, focusing on aspects of the car that might appeal to women, such as safety, comfort, and ease of driving.
            - **Action**:
                - Develop promotional strategies that emphasize car safety features, low maintenance costs, and ease of driving (e.g., automatic transmission models) to attract more female buyers.
    - **Income-Based Customer Segmentation and Targeting**
        - **Target Low-Income Customers (Under 300,000 RUB)**:
            - **Key Insight**: The largest group of customers falls under the 300,000 RUB income category. These customers are likely to prioritize affordable models and need financing options.
            - **Recommendation**:
                - Offer **budget-friendly models** like **Granta** and **2107**, emphasizing their **affordability and value**.
            - **Action**:
                - Provide financing plans or **installment payments** that cater to this income group, making it easier for them to afford the car.
                - Introduce a **trade-in program** where customers can exchange old vehicles for a discount on new LADA cars.
        - **Target High-Income Customers (900,000–1,000,000 RUB)**:
            - **Key Insight**: High-income customers (with annual incomes of 900,000–1,000,000 RUB) tend to buy more expensive cars, especially those aged 35 and above.
            - **Recommendation**:
                - Focus on premium models or high-end variants for these customers. Introduce exclusive features, customization options, and personalized services.
            - **Action**:
                - Create VIP loyalty programs or offer exclusive after-sales services, such as free maintenance for the first few years or complimentary upgrades, to attract these high-income customers.
    - **Region-Based Customer Segmentation and Targeting**
        - **Regions with High Sales (Republic of Bashkortostan, Republic of Tatarstan, Nizhny Novgorod Oblast)**:
            - **Key Insight**: These regions generate the most revenue, contributing a large portion of total sales.
            - **Recommendation**:
                - Continue investing in these high-performing regions with increased dealership presence, localized marketing, and regional discounts.
            - **Action**:
                - Offer region-specific promotions such as **regional loyalty programs** or community events that engage local customers.
        - **Regions with Low Sales (e.g., Republic of Mari El)**:
            - **Key Insight**: Republic of Mari El has the lowest sales revenue.
            - **Recommendation**:
                - Conduct market research to identify why sales are low in this region and adjust strategies accordingly.
            - **Action**:
                - Develop targeted advertising and promotional campaigns specific to this region, focusing on the affordability and practicality of LADA models. Consider providing special deals or increasing the dealership network to make cars more accessible.
    - **Saving Year Analysis and Targeting**
        - **Target Customers with Longer Saving Durations (Young and Low-Income)**:
            - **Key Insight**: Younger customers tend to save longer to afford a car, as they often buy more expensive models. This trend could be leveraged to introduce financing and leasing options for these customers.
            - **Recommendation**:
                - Create affordable finance plans, such as **low-interest rates** or **extended payment terms**, for younger customers who may need more time to save for their car.
            - **Action**:
                - Introduce flexible **leasing options** for younger buyers, which would allow them to drive newer models without a hefty upfront payment.
        - **Target Older Customers with Shorter Saving Durations**:
            - **Key Insight**: Older customers tend to have higher incomes and save less time to afford their vehicle.
            - **Recommendation**:
                - Provide tailored financing options that appeal to this demographic, with **lower interest rates** or **quick loan approvals** for those who can afford to pay upfront or within a short period.
            - **Action**:
                - Introduce packages with **premium add-ons** or offer **trade-in options** for those looking to upgrade to a new car.

---

### **2. Conclusion**

The analysis of LADA car sales and customer demographics reveals several important insights. The largest customer group is aged 45–55, accounting for over 70% of total sales, with the age bins of 50 and 55 being the most prominent. Customers aged 25–35 represent the smallest share of sales, but they tend to spend more on higher-end models. Over the years, sales among younger customers and those over 58 have decreased, while the 45–55 age group has consistently increased. There is a weak correlation between age and car model preferences, though customers aged 45–55 prefer models like Granta, 2107, and 2113.

In terms of gender, 90.87% of buyers are male, with female customers concentrated in specific regions like Tatarstan. This gender imbalance remains consistent over the years and across regions. Income analysis shows that the majority of customers earn under 300,000 RUB annually, with a small percentage of high-income customers (900,000–1,000,000 RUB). Younger buyers (under 30) tend to spend more on expensive models, whereas older customers with higher incomes prefer affordable models.

Customers generally save for 1.66 years to afford their cars, with younger buyers typically saving longer due to lower incomes and higher model prices. There is a strong negative correlation between income and the number of saving years, while a positive correlation exists between car price and saving duration. Regional analysis highlights high revenue-generating areas such as Bashkortostan, Tatarstan, and Nizhny Novgorod, while regions like Mari El underperform.

In conclusion, to optimize sales, LADA should focus marketing efforts on the 45–55 age group, promote budget-friendly models for low-income buyers, and introduce flexible financing for younger customers. Regional strategies should be tailored to high-performing regions, while efforts in underperforming areas should involve market research and targeted promotions. Additionally, attention to gender-specific marketing could help increase the female customer base.
