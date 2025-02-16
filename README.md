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

Perform correlation analysis and generating actionable insights from the data.

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

Provide actionable insights and recommendations based on my analysis, supported from calculations from my data
