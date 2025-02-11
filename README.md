# SWISS-CODING-FINAL-PROJECT

## A - Project title:

LADA's care sales analysis

## B - Purpose and Outcome:

Purpose: To help management understand the insights behind sales data of LADA

Outcome: Insights into the potential customer segments and popular model that LADA should focus their sales promotions on in the future.

## C - **Technologies**

I intend to use Excel, Power BI, SQL, Python (Pandas, Matplotlib, etc.) for this project

## D - Dataset and Sources:

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

In this project, I intend to perform correlation analysis and generate actionable insights from the data.

#### Data cleaning:

- Data Profiling: load dataset and investigate data head using df.head(); join the 2 dataset to gain a fact table including all the details of all transactions: using **purchase_id** columns in 2 tables as key; Summary Statistics for Numerical Columns and Non-Numeric Columns.
- Fixing data types: object → string/date/category ; fix dates using :

```python
df['Joining_Date'] = pd.to_datetime(df['Joining_Date'], format="%Y-%m-%d", errors='coerce')

```

- Checking consistency: Checking and Fixing Consistency of Non-Numeric Data
- Handling Missing Values: using df.isnull().sum(); this dataset has no missing value so I do not need to do this step.
- Removing Duplicates: Check Number of Duplicate Rows using df.duplicated().sum()
- Outlier Detection and Handling

#### **Exploratory Data Analysis (EDA):**

Generate summary statistics for all variables. 

Create visualizations to uncover patterns and relationships : correlation, histograms, box plots, correlation.

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
