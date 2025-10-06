# 👨🏼‍⚕️ Health Care Analysis

This project explores a de-identified healthcare dataset containing over 55,000 hospital admission records from 2019 to 2024. Each record includes patient demographics, medical conditions, admission details, and billing information.
The goal of this analysis was to understand how different medical conditions influence hospital billing costs. To be more specific, to identify which conditions tend to generate the highest average billing amounts.

## 🧭 Table of Contents
  - [Dataset Description](#dataset-description)  
  - [Objective](#objective)  
  - [Tools Used](#tools-used)
    - [Research Questions & Findings](#research-questions--findings)  
    	1. [Which medical conditions have the highest average billing amounts?](#1-which-medical-conditions-have-the-highest-average-billing-amounts)  
    	2. [How do admission types affect average billing amounts?](#2-how-do-admission-types-emergency-routine-urgent-etc-affect-average-billing-amounts)  
    	3. [Which insurance providers cover the highest average billing amounts?](#3-which-insurance-providers-cover-the-highest-average-billing-amounts)  
    	4. [Does age influence the average billing amount for patients?](#4-does-age-influence-the-average-billing-amount-for-patients)  
    	5. [Do males or females have higher average billing amounts overall?](#5-do-males-or-females-have-higher-average-billing-amounts-overall)  
    	6. [Which combinations of medical condition and admission type produce the highest costs?](#6-which-combinations-of-medical-condition-and-admission-type-produce-the-highest-costs)  
  - [Conclusion](#-conclusion)  

## Dataset Description

- **Rows:** 55,500  
- **Columns:** 15  
- **Key fields:** Age, Gender, Blood Type, Medical Condition, Admission Type, Billing Amount, Insurance Provider, etc.  
- **Source:** [synthetic/de-identified healthcare data (no PHI).  ](https://www.kaggle.com/datasets/prasad22/healthcare-dataset)
- **Time period:** 2019–2024.  

Each record represents one hospital admission, including patient demographics, admission details, and billing information.

## Objective
Identify which medical conditions result in the **highest average billing amount** and explore how factors such as admission type, insurance provider, and demographics contribute to overall healthcare costs.

# Tools used
- SQL
- Excel

## Research Questions & Findings

### 1. Which medical conditions have the highest average billing amounts?
**SQL Query**

```sql
WITH condition_stats AS (
	SELECT 
		[Medical Condition],
		AVG([Billing Amount]) AS avg_bill
	FROM hc_data
	GROUP BY [Medical Condition]
	)
SELECT 
	[Medical Condition],
	FORMAT(avg_bill,'N2') AS [Average Bill]
FROM condition_stats
ORDER BY avg_bill DESC;
```
**Result** 

<img width="210" height="133" alt="image" src="https://github.com/user-attachments/assets/fd3ac9ae-e322-4a46-a3bd-463e46ecd6c3" />

From the queries we ran, we can see that obesity is the most expensive medical condition. 

### 2. How do admission types (Emergency, Routine, Urgent, etc.) affect average billing amounts?
**SQL Query**

```sql
WITH admission_stats AS (
	SELECT 
		[Admission Type],
		AVG([Billing Amount]) AS avg_bill
	FROM hc_data
	GROUP BY [Admission Type]
)
SELECT 
	[Admission Type],
	FORMAT(avg_bill, 'N2') AS [Average Bill]
FROM admission_stats
ORDER BY avg_bill DESC;
```
**Result** 

<img width="194" height="77" alt="image" src="https://github.com/user-attachments/assets/ac7a5266-5384-4b72-9c74-edadc89c9a11" />

Elective admissions had the highest average billing amount at $25,602.23, slightly above urgent ($25,517.36) and emergency ($25,497.40) cases. This suggests that planned procedures tend to cost more than unplanned or emergency treatments.

### 3. Which insurance providers cover the highest average billing amounts?
**SQL Query**

```sql
WITH insurance_avg AS (
	SELECT 
		[Insurance Provider],
		AVG([Billing Amount]) AS avg_bill
	FROM hc_data
	GROUP BY [Insurance Provider]
)
SELECT 
	[Insurance Provider] AS [Insurance Provider],
	FORMAT(avg_bill, 'N2') AS [Average Bill]
FROM insurance_avg
ORDER BY avg_bill DESC;
```
**Result** 

<img width="218" height="117" alt="Screenshot 2025-10-06 135630" src="https://github.com/user-attachments/assets/4cd38439-88d3-443c-8c60-4d8e995c8b7d" />

Medicare patients had the highest average billing amount at $25,615.99, followed closely by Blue Cross at $25,613.01 and Aetna at $25,553.29. This shows that billing costs are fairly consistent across major insurance providers, with Medicare slightly leading in average hospital expenses.

### 4. Does age influence the average billing amount for patients?
**SQL Query**

```sql
SELECT 
	CASE 
		WHEN [Age] BETWEEN 0 AND 17 THEN '0–17'
		WHEN [Age] BETWEEN 18 AND 35 THEN '18–35'
		WHEN [Age] BETWEEN 36 AND 55 THEN '36–55'
		WHEN [Age] BETWEEN 56 AND 75 THEN '56–75'
		ELSE '76+' 
	END AS Age_Group,
	FORMAT(AVG([Billing Amount]), 'N2') AS [Average Bill]
FROM hc_data
GROUP BY 
	CASE 
		WHEN [Age] BETWEEN 0 AND 17 THEN '0–17'
		WHEN [Age] BETWEEN 18 AND 35 THEN '18–35'
		WHEN [Age] BETWEEN 36 AND 55 THEN '36–55'
		WHEN [Age] BETWEEN 56 AND 75 THEN '56–75'
		ELSE '76+' 
	END
ORDER BY AVG([Billing Amount]) DESC;
```

**Result**

<img width="173" height="112" alt="image" src="https://github.com/user-attachments/assets/3caebbff-c57f-4d6f-8d96-b0355d0bfb00" />

Patients aged 0–17 had the highest average billing amount at $28,512.86, which stands out compared to other groups. However, this age group had only 116 records, suggesting the average is likely inflated by a few high-cost pediatric cases. Among adults, billing amounts were relatively consistent—ranging from $25,464.45 to $25,600.36—with the 56–75 group having the highest average. This supports the idea that older adults tend to incur slightly higher hospital costs, while the spike in the youngest group is due to limited data and outliers.

### 5. Do males or females have higher average billing amounts overall?
**SQL Query**

```sql
SELECT
	[Gender],
	FORMAT(AVG([Billing Amount]),'N2') AS [Average Bill]
FROM hc_data
GROUP BY [Gender]
ORDER BY [Average Bill] DESC;
```

**Result**

<img width="156" height="55" alt="image" src="https://github.com/user-attachments/assets/b01fabd8-0854-4959-a2e8-4e2c11d88b99" />

Male patients had a higher average billing amount at $25,607.86, compared to $25,470.65 for females. While the difference is relatively small, it suggests that male patients tend to incur slightly higher hospital costs on average, though overall billing amounts remain fairly consistent between genders.

### 6. Which combinations of medical condition and admission type produce the highest costs?

```sql
SELECT 
	[Medical Condition],
	[Admission Type],
	FORMAT(AVG([Billing Amount]), 'N2') AS [Average Bill]
FROM hc_data
GROUP BY [Medical Condition], [Admission Type]
ORDER BY AVG([Billing Amount]) DESC;
```

<img width="301" height="368" alt="image" src="https://github.com/user-attachments/assets/85f99db6-0771-4ccc-9cce-a51e108062cd" />

Diabetes patients admitted for elective procedures had the highest average billing amount at $25,983.14, followed closely by obesity (urgent admissions) and asthma (emergency admissions). Overall, chronic conditions such as diabetes, obesity, and asthma consistently appear among the most expensive, regardless of admission type. This suggests that managing long-term health conditions often leads to higher hospital costs, especially when patients require specialized or unplanned care.

## 🩺 Conclusion

From analyzing over **55,000 hospital admission records (2019–2024)**, several key insights emerged:

- **Obesity** had the highest overall average billing amount among medical conditions, indicating that treatments related to chronic weight-related conditions can be costly.  
- **Elective admissions** were slightly more expensive than emergency or urgent ones, showing that planned medical procedures tend to drive higher hospital costs.  
- **Medicare** patients had the highest billing averages, though differences among major insurance providers were minimal, suggesting consistent pricing across insurers.  
- While patients aged **0–17** appeared to have the highest average bills, this was largely due to a small sample size and high-cost pediatric cases. Among adults, the **56–75** age group incurred the highest costs, aligning with expectations about aging and healthcare utilization.  
- **Male patients** had marginally higher average billing amounts than females, though the difference was negligible.  
- When combining **medical condition** with **admission type**, chronic conditions such as **diabetes, obesity, and asthma** emerged as the most expensive categories, especially under elective or urgent admissions.

Overall, the data shows that **chronic illnesses and planned procedures** are the biggest cost drivers in hospital billing, while factors like **age, gender, and insurance provider** have only modest effects.  
Future analysis could incorporate **length of stay**, **procedure type**, and **severity level** to build predictive models for healthcare cost forecasting.


