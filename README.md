# I. Introduction
Using SQL likes handle **datetime, aggregate, and window functions** to display revenue, retention rate of customer, index related to stocks (trend of stock, stock/sales).
# II. Requirements
1.	Google Cloud Platform account
2.	Project on Google Cloud Platform
3.	Google BigQuery API enabled
4.	SQL query editor or IDE
# III. Dataset Access
1.	Sign in to your Google Cloud Platform account and create a new project.
2.	Go to the BigQuery console and choose your newly created project.
3.	In the navigation menu, click on 'Add Data' and select 'Search a project.'
# IV. Exploring the Dataset
**Query 1: Calc Quantity of items, Sales value & Order quantity by each Subcategory in L12M**

**_SQL code_**

![image](https://github.com/user-attachments/assets/4b770d8e-31d2-45f9-a0ab-35224e7948e9)

**_Query result_**

![image](https://github.com/user-attachments/assets/9277eeb1-3d88-4a5c-8366-e2e15635b767)

**Query 2: Ranking Top 3 TeritoryID with biggest Order quantity of every year. If there's TerritoryID with same quantity in a year, do not skip the rank number.**

**_SQL code_**

![image](https://github.com/user-attachments/assets/4c6a412d-2a2c-4f39-8498-d16498209625)
![image](https://github.com/user-attachments/assets/af983de6-bce8-4c7f-bd5c-e06c5da45dda)


**_Query result_**

![image](https://github.com/user-attachments/assets/f8c93705-0647-49be-b40b-882b78584b17)

**Query 3: Calc Total Discount Cost belongs to Seasonal Discount for each SubCategory.**

**_SQL code_**

![image](https://github.com/user-attachments/assets/db168042-c48f-42cf-a53d-27459c153118)

**_Query result_**

![image](https://github.com/user-attachments/assets/864ddb28-49aa-4479-8679-5ae3a09ca68d)

**Query 4: Retention rate of Customer in 2014 with status of Successfully Shipped (Cohort Analysis)**

**_SQL code_**

![image](https://github.com/user-attachments/assets/12e9cd02-8630-4ff3-8490-578e402a3815)

**_Query result_**

![image](https://github.com/user-attachments/assets/49ac553a-a610-4f50-82ad-a8c0da86d241)

**Query 5: Calc Ratio of Stock / Sales in 2011 by product name, by month. Order results by month desc, ratio desc. Round Ratio to 1 decimal.**

**_SQL code_**

![image](https://github.com/user-attachments/assets/a3177125-095d-497f-be84-e75e65fac2f8)
![image](https://github.com/user-attachments/assets/078fedba-2a74-4be5-beb1-7f0e60136041)

**_Query result_**

![image](https://github.com/user-attachments/assets/e751a585-39fb-4f0b-b89d-c0221f94904d)

**Query 6: No of order and value at Pending status in 2014**

**_SQL code_**

![image](https://github.com/user-attachments/assets/e9f126e2-1090-4fd2-99ce-75384359d9f3)

**_Query result_**

![image](https://github.com/user-attachments/assets/a6896be9-fb5e-43f1-a49c-41d21c16c4f8)

**Query 7: Calc Ratio of Stock / Sales in 2011 by product name, by month. Order results by month desc, ratio desc. Round Ratio to 1 decimal.**

**_SQL code_**

![image](https://github.com/user-attachments/assets/b637e8ba-5188-4127-9bef-98b287164a15)
![image](https://github.com/user-attachments/assets/faac702a-effc-4ab6-b80c-8f75f7e0bf9a)

**_Query result_**

![image](https://github.com/user-attachments/assets/07864a1c-7b3b-41a7-995d-27a7d35e3fa9)

**Query 8: No of order and value at Pending status in 2014**

**_SQL code_**

![image](https://github.com/user-attachments/assets/15068d55-2872-428d-8f88-78fb41604ce8)

**_Query result_**

![image](https://github.com/user-attachments/assets/6e876513-52a2-4517-80e1-0dfe12fa46ef)



























