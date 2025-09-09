### PROJECT TOPIC
HEALTHCARE-DATASET-ANALYSIS

### PROJECT OVERVIEW
The project Healthcare Dataset Analysis is focused on analyzing hospital dataset to gain meaningful insights into patients demographics, treatments, hospital performance and insurance billing. The analysis aims to support better clinical and operational decision-msking by answering key business and healthcare questions.

### DATA SOURCE
The datasets is gotten from Electrical Medical Records a Health Institutions.

### TOOL USED
- SQL SERVER 2022 for Data cleaning uploading and querying
- Excel for Visualization

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
 Screenshot (28).png

```
#### Summary of Average Age by Gender

- Based on the analysis of the Healthcare Dataset, the average age of patients was calculated and grouped by gender. The results show that:
	- Male Patients: The average age is 51 years.
	- Female Patients: The average age is also 51 years.
- Indicating that there is no significant difference in the average age between male and female patients within the dataset. Both groups fall into the middle-age category,     suggesting a balanced age distribution across genders.

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
- From the Healthcare Dataset, the frequency of patient blood types was analyzed. The results shows that:
	- Blood Type AB- is the most common, with 1,275 patients.
- This suggests that AB-, while generally considered rare in the global population, is disproportionately more frequent in this dataset. This could be due to the sample characteristics or demographic distribution of patients included.

---- QUESTION 3
---- How many patients fall into different age groups (e.g., 0–18, 19–35, 36–60, 61+)?
``` sql
SELECT 
    CASE 
        WHEN Age BETWEEN 0 AND 18 THEN '0-18'
        WHEN Age BETWEEN 19 AND 35 THEN '19-35'
        WHEN Age BETWEEN 36 AND 60 THEN '36-60'
        WHEN Age >= 61 THEN '61+'
    END AS Age_Group,
    COUNT(*) AS Patient_Count
FROM Healthcare_Dataset
GROUP BY 
    CASE 
        WHEN Age BETWEEN 0 AND 18 THEN '0-18'
        WHEN Age BETWEEN 19 AND 35 THEN '19-35'
        WHEN Age BETWEEN 36 AND 60 THEN '36-60'
        WHEN Age >= 61 THEN '61+'
    END
```
```  61+	3661
    0-18	164
   19-35	2490
   36-60	3685
```

---- QUESTION 4
---- What is the distribution of patients by admission type (Emergency, Elective, Urgent)?
```SQL
select Admission_Type,
   count(*) AS patient_count
from  [dbo].[Healthcare_Dataset]
group by Admission_Type
```
```Emergency	3367
Urgent	3391
Elective	3242
```
---- QUESTION 5
---- Which hospital has admitted the highest number of patients?

``` SQL
select top 1
     Hospital,
	 count(Name) AS NameCount
from  [dbo].[Healthcare_Dataset]
group by Hospital
order by NameCount desc

```

```
Smith PLC	19

```
----- QUESTION 6
----- What is the average length of stay (Discharge Date – Admission Date) by hospital?

``` SQL
select 
    Hospital,
    AVG(DATEDIFF(DAY, Date_of_Admission, Discharge_Date)) AS AverageLengthOfStay
from  [dbo].[Healthcare_Dataset]
where
     Date_of_Admission IS NOT NULL AND
	 Discharge_Date IS NOT NULL
group by
       Hospital
```

---- QUESTION 7
----  Which medications are prescribed most frequently for each medical condition?

``` SQL
  WITH 
  MedicationCounts AS ( 
    SELECT Medical_Condition, 
	Medication, COUNT(*) AS PrescriptionCount 
FROM [dbo].[Healthcare_Dataset]
GROUP BY Medical_Condition, Medication ),
          RankedMedications AS ( 
	SELECT 
	     Medical_Condition, 
	     Medication, PrescriptionCount, 
		 ROW_NUMBER() OVER ( PARTITION BY Medical_Condition 
ORDER BY PrescriptionCount DESC ) 
    AS rn 
FROM MedicationCounts ) 
SELECT 
    Medical_Condition, 
	Medication, PrescriptionCount 
FROM RankedMedications 
WHERE rn = 1 

```

```Arthritis	Penicillin	366
   Asthma	Aspirin	370
   Cancer	Penicillin	364
   Diabetes	Aspirin	335
   Hypertension	Lipitor	356
   Obesity	Paracetamol	332
```

---- QUESTION 8
---- Which doctors have treated the most patients for each condition?

``` SQL
WITH
    DoctorPatientCounts AS ( 
SELECT Medical_Condition, 
       Doctor, COUNT(DISTINCT Name) AS PatientCount 
FROM [dbo].[Healthcare_Dataset]
GROUP BY Medical_Condition, Doctor ), 
           RankedDoctors AS ( 
    SELECT Medical_Condition, 
	       Doctor, PatientCount, 
		   RANK() OVER (PARTITION BY Medical_Condition 
ORDER BY PatientCount DESC) AS Rank 
   FROM DoctorPatientCounts ) 
       SELECT Medical_Condition, 
         Doctor, PatientCount 
FROM RankedDoctors 
WHERE Rank = 1
```

```Arthritis	Patricia Moore	3
Asthma	Michael Smith	4
Cancer	William Fox	2
Cancer	Zachary Smith	2
Cancer	Matthew Gonzalez	2
Cancer	Michelle Anderson	2
Cancer	Karen Smith	2
Cancer	Julia Perez	2
Cancer	Joseph Morales	2
Cancer	Nicole Johnson	2
Cancer	Ryan Thompson	2
Cancer	Susan Miller	2
Cancer	Steven Adams	2
Cancer	Brittany Brooks	2
Cancer	Angela Miller	2
Cancer	Amanda Jones	2
Cancer	Aaron Smith	2
Cancer	Dawn Murphy	2
Cancer	Jennifer Turner	2
Cancer	Jacob Moore	2
Cancer	Jasmine Russell	2
Cancer	James Johnson	2
Diabetes	Matthew Smith	3
Hypertension	David Johnson	3
Hypertension	Jennifer Miller	3
Obesity	Jennifer Smith	3
Obesity	Robert Brown	3
```

---- QUESTION 9
----  Which insurance provider covers the highest total billing amount?

``` SQL
select top 1
     [Insurance_Provider],
	 sum([Billing_Amount]) AS
Total_Billing
from [dbo].[Healthcare_Dataset]
group by
       [Insurance_Provider]
order by
        Total_Billing desc
```

```
Cigna	52340171.604248
```

----QUESTION 10
---- What is the average billing amount per admission type?

```SQL
select 
     [Admission_Type],
	 AVG([Billing_Amount]) AS
Average_Billing
from [dbo].[Healthcare_Dataset]
group by [Admission_Type]
```
```Emergency	24708.51191246
Urgent	25960.8335437915
Elective	25891.8326591478

```

---- QUESTION 11
----  Which doctor has the highest average billing per patient?

``` SQL
select top 1
     [Doctor],
	 AVG([Billing_Amount]) AS
Avg_Billing_Per_Patient
from [dbo].[Healthcare_Dataset]
group by [Doctor]
order by Avg_Billing_Per_Patient desc

```

```
Timothy Serrano	49995.90234375

```

---- QUESTION 12
---- Which doctor treated the most patients in emergency admissions?

``` SQL
select top 1
     [Doctor],
	 count(*) AS Emergency_Patient_Count
from [dbo].[Healthcare_Dataset]
where [Admission_Type] = 'Emergency'
group by [Doctor]
order by Emergency_Patient_Count desc

```

```
Jennifer Smith	4

```

---- QUESTION 13
----  What percentage of patients had “Normal”, “Abnormal”, or “Inconclusive” test results?

```SQL
select 
      [Test_Results],
      count(*) * 100.0/ sum(count(*))
over () AS Percentage
from [dbo].[Healthcare_Dataset]
group by [Test_Results]
```
```
   Abnormal	34.560000000000
   Normal	32.670000000000
   Inconclusive	32.770000000000

```

----- QUESTION 14
----- Is there a relationship between medical condition and test results?

----- QUESTION 15
----- Which hospital records the highest percentage of abnormal test results?
``` SQL
select top 1
     Hospital,
	 CAST(SUM(CASE WHEN Test_Results = 'Abnormal' THEN 1 ELSE 0 END) AS FLOAT)
	 * 100 / COUNT(*) AS Abnormal_Percentage
from [dbo].[Healthcare_Dataset]
group by Hospital
order by Abnormal_Percentage
```
```
Brooks, Wright and Olson	100

```



