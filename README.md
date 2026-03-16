# INTRODUCTION # 

Employee attrition is a critical issue for organizations because frequent employee turnover can increase recruitment costs, disrupt work processes, and negatively affect organizational performance. Understanding the factors that influence employee attrition is therefore essential for effective human resource management.

One important perspective when studying attrition is the organizational hierarchy. Employees at different management levels often experience different work conditions, responsibilities, and expectations. For example, frontline employees may face operational pressure and lower salaries, while managers may experience higher stress and workload. These differences may lead to varying attrition patterns across organizational levels.

**The aim of this project is to analyze how employee attrition differs across management levels within an organization.** The analysis uses the IB**M HR Analytics Employee Attrition dataset,** which contains information about employees’ demographic characteristics, job roles, salaries, satisfaction levels, and whether they left the company.

The study follows the analytical process used in the case study structure: defining the business question, preparing and processing the data, performing analysis, sharing results through visualizations, and providing recommendations.

The central research question of this study is whether employee attrition differs significantly across organizational management levels. Additionally, the analysis explores potential factors that may explain these differences, such as salary, job satisfaction, overtime, work-life balance, and years of experience.

# 1. ASK #

## 1.1 Research Problem ## 

Organizations often face the challenge that certain employee groups leave the company more frequently than others. If attrition is concentrated at specific organizational levels, it may indicate underlying issues related to management practices, workload, compensation, or job satisfaction.

Understanding which employee groups are most likely to leave can help organizations develop targeted retention strategies.

## 1.2 Research Question ##

How does employee attrition differ across organizational management levels?

## 1.3 Sub-questions ##

- Which management level has the highest attrition rate?
- Is employee attrition related to salary, job satisfaction, or work experience?
- Do departments or job roles influence attrition in addition to management level?
- Is overtime or work-life balance associated with a higher probability of leaving?

## 1.4 Practical Relevance ## 

The results of this analysis may help organizations:
- identify employee groups with higher attrition risk
- understand potential causes of employee turnover
- improve employee retention strategies
- support data-driven HR decision making

# 2. PREPARE #

## 2.1 Data Source ## 

The analysis uses the **IBM HR Analytics Employee Attrition dataset**, which is publicly available on** Kaggle.** The dataset contains information about employees including demographic characteristics, job roles, salary, job satisfaction, and whether the employee left the organization.

The dataset is provided in CSV format, making it suitable for import into SQL databases and data analysis tools.

## 2.2 Dataset Structure ##

The dataset contains multiple variables describing employees. Important attributes for this analysis include: 

- _Attrition_ – indicates whether the employee left the company
- _JobLevel_ – hierarchical level within the organization
- _JobRole_ – employee’s role
- _Department_ – organizational department
- _MonthlyIncome_ – employee salary
- _YearsAtCompany_ – years worked at the company
- _TotalWorkingYears_ – total career experience
- _JobSatisfaction_ – satisfaction level
- _WorkLifeBalance_ – work-life balance rating
- _OverTime_ – whether the employee works overtime
- _Age_ – employee age

These variables allow analysis of how demographic factors, work conditions, and organizational structure relate to employee attrition.

## 2.3 Definition of Management Levels ##

To analyze attrition across organizational hierarchy, employees are grouped into management categories based on JobLevel:

- **JobLevel 1–2** → Specialists / frontline employees
- **JobLevel 3** → Middle management
- **JobLevel 4–5** → Senior management

This classification simplifies interpretation of the hierarchical structure within the dataset.

## 2.4 Data Credibility Evaluation (ROCCC) ##

To ensure the dataset is suitable for analysis, the ROCCC framework is applied.

**R - Reliable**

The dataset is widely used in HR analytics learning environments and educational research. Although it is not an official corporate dataset, it is structured and commonly used for analytical practice.

**O - Original**

The dataset originates from IBM HR analytics simulations and is distributed through Kaggle for analytical practice. It represents synthetic but realistic HR data.

**C - Comprehensive**

The dataset includes multiple variables describing employee characteristics, job roles, satisfaction, compensation, and career progression. These variables allow comprehensive analysis of potential attrition factors.

**C - Current**

The dataset does not represent a specific year but contains relevant HR indicators commonly used in workforce analytics. While it may not represent current organizational conditions exactly, the variables remain relevant for studying attrition patterns.

**C - Cited**

The dataset is publicly available on Kaggle and can be properly cited in academic and analytical work. Overall, the dataset is considered appropriate for educational analysis of employee attrition.

# 3. PROCESS #

The purpose of the process stage is to prepare the dataset for analysis by cleaning, verifying, and transforming the data. Proper data processing is essential because inaccurate or inconsistent data may lead to incorrect analytical results. Ensuring data quality improves the reliability of the conclusions drawn from the analysis.

## 3.1 Importing the Dataset into BigQuery ## 

The dataset was downloaded from Kaggle and uploaded to Google BigQuery, where all further data preparation and analysis were conducted. Importing the dataset into BigQuery ensures that the data is stored in a structured database environment where it can be queried and analyzed efficiently.

## 3.2 Verifying the Dataset Structure ##

After importing the dataset into BigQuery, the first step was to verify that the dataset was successfully loaded and that the number of records matched expectations.

This step is important because errors during the import process may result in missing rows, incomplete datasets, or incorrect data structures. Verifying the dataset structure ensures that the analysis is performed on the complete dataset.

The following SQL query was used to check the total number of records in the dataset:

```sql
SELECT COUNT(*) AS total_records
FROM `hr-analytics-hh.employee_data.attrition_performance`;
```

**The query result showed that the dataset contains 1470 records, which corresponds to the expected number of employees in the IBM HR Analytics dataset.**

This confirms that the dataset was successfully imported into BigQuery and that no rows were lost during the data upload process. Ensuring the correct number of records is essential before proceeding with further data cleaning and analysis steps.

## 3.3 Checking for Duplicate Records ## 

After verifying the dataset structure, the next step was to check for duplicate records. Duplicate entries can distort analysis results because they may artificially increase the number of employees or affect calculated statistics such as averages and attrition rates.

To detect duplicates, the EmployeeNumber variable was used as a unique identifier for each employee. The following SQL query was executed to check whether any employee appears more than once in the dataset.

```sql
SELECT EmployeeNumber, COUNT(*)
FROM `hr-analytics-hh.employee_data.attrition_performance`
GROUP BY EmployeeNumber
HAVING COUNT(*) > 1;
```

The query returned no results, indicating that there are no duplicate EmployeeNumber values in the dataset. This means that each record represents a unique employee, and there are no duplicate entries that could distort the analysis. **Therefore, no additional data cleaning was required at this stage.**

Ensuring that the dataset does not contain duplicates is important because it confirms the integrity of the data and guarantees that each employee is counted only once in the analysis.

## 3.4 Checking for Missing Values ## 

After verifying that the dataset contains the correct number of records and no duplicate entries, the next step was to check for missing values in important variables. Missing values can affect the reliability of statistical calculations and may lead to biased analytical results if not handled properly.

To verify whether key variables contain missing values, the following SQL query was executed:

```sql
SELECT
SUM(CASE WHEN Attrition IS NULL THEN 1 ELSE 0 END) AS missing_attrition,
SUM(CASE WHEN JobLevel IS NULL THEN 1 ELSE 0 END) AS missing_joblevel,
SUM(CASE WHEN MonthlyIncome IS NULL THEN 1 ELSE 0 END) AS missing_income
FROM `hr-analytics-hh.employee_data.attrition_performance`;
```
The query results showed the following values:

| Row | missing_attrition | missing_joblevel | missing_income |
|-----|-------------------|------------------|----------------|
| 1   | 0                 | 0                | 0              |

**This means that none of these key variables contain missing values in the dataset.** Each employee record includes valid information for attrition status, job level, and monthly income.

This result confirms that the dataset is complete for these important analytical variables, and no additional data cleaning steps were required at this stage. Ensuring that key variables do not contain missing values is essential because it allows reliable calculation of attrition rates and comparisons between management levels.

## 3.5 Creating Additional Variables for Analysis ##

To make the analysis easier and more meaningful, additional variables can be created.

In this project two new variables are created:

management_level – groups employees into hierarchical levels

attrition_flag – converts the attrition variable into a numerical value

This transformation allows easier grouping and calculation of attrition rates.

CREATE VIEW hr_attrition_prepared AS
SELECT *,
CASE
WHEN JobLevel IN (1,2) THEN 'Specialist'
WHEN JobLevel = 3 THEN 'Middle Management'
WHEN JobLevel IN (4,5) THEN 'Senior Management'
END AS management_level,

CASE
WHEN Attrition = 'Yes' THEN 1
ELSE 0
END AS attrition_flag
FROM hr_attrition;

Creating these variables simplifies later analysis and allows clearer comparisons between organizational levels.

3.6 Validating the Prepared Dataset

Before starting the analysis, the prepared dataset should be checked once more to confirm that the transformations were applied correctly.

This step ensures that:

the management level grouping works correctly

the attrition flag variable contains correct values

the dataset is ready for analytical queries

Example query:

SELECT management_level, COUNT(*)
FROM hr_attrition_prepared
GROUP BY management_level;

This final validation ensures that the dataset is consistent and ready for the analysis stage.
