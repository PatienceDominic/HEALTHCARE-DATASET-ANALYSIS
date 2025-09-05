### PROJECT TOPIC
HEALTHCARE-DATASET-ANALYSIS

### PROJECT OVERVIEW
The project Healthcare Dataset Analysis is focused on analyzing hospital dataset to gain meaningful insights into patients demographics, treatments, hospital performance and insurance billing. The analysis aims to support better clinical and operational decision-msking by answering key business and healthcare questions.

### DATA SOURCE
The datasets is gotten from Electrical Medical Records a Health Institutions.

### TOOL USED
Excel for data cleaning and uploading SQL Server for querrying the data to uncover trends and performance metrics

### EXPLORATORY DATA ANALYSIS
The Data was explored to answer the following questions.
-  What is the average age of patients by gender?‎
-  Which blood type is most common among patients?
-  How many patients fall into different age groups (e.g., 0–18, 19–35, 36–60, 61+)?
-  What is the distribution of patients by admission type (Emergency, Elective, Urgent)?
-  Which hospital has admitted the highest number of patients?
-  What is the average length of stay (Discharge Date – Admission Date) by hospital?
-  Which medications are prescribed most frequently for each medical condition?
-  Which doctors have treated the most patients for each condition?
-  Which insurance provider covers the highest total billing amount?
-  What is the average billing amount per admission type?
-  Which doctor has the highest average billing per patient?
‎‎-  Which doctor treated the most patients in emergency admissions?
‎-  What percentage of patients had “Normal”, “Abnormal”, or “Inconclusive” test results?
-  Is there a relationship between medical condition and test results?
‎‎-  Which hospital records the highest percentage of abnormal test results?

----- QUESTION 1
----- What is the average age of patients by gender?‎

``` SQL

select Gender, AVG(Age) AS Average_Age
from [dbo].[Healthcare_Dataset]
group by Gender

```
```sql
Male	51
Female	51

```
---- QUESTION 2
----  Which blood type is most common among patients?

```SQL
select TOP 1 Blood_Type, count(*) AS count
from [dbo].[Healthcare_Dataset]
group by Blood_Type
order by count desc

```
```AB-	1275
```
