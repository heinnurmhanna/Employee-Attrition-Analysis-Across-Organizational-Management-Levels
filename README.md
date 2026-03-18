# INTRODUCTION # 

Employee attrition is a critical issue for organizations because frequent employee turnover can increase recruitment costs, disrupt work processes, and negatively affect organizational performance. Understanding the factors that influence employee attrition is therefore essential for effective human resource management.

One important perspective when studying attrition is the organizational hierarchy. Employees at different management levels often experience different work conditions, responsibilities, and expectations. For example, frontline employees may face operational pressure and lower salaries, while managers may experience higher stress and workload. These differences may lead to varying attrition patterns across organizational levels.

**The aim of this project is to analyze how employee attrition differs across management levels within an organization.** The analysis uses the **IBM HR Analytics Employee Attrition dataset,** which contains information about employees’ demographic characteristics, job roles, salaries, satisfaction levels, and whether they left the company.

The study follows the analytical process used in the case study structure: defining the business question, preparing and processing the data, performing analysis, sharing results through visualizations, and providing recommendations.

The central research question of this study is whether employee attrition differs across organizational management levels. Additionally, the analysis explores potential factors that may explain these differences, such as salary, job satisfaction, overtime, work-life balance, and years of experience.

# 1. ASK #

## 1.1 Research Problem ## 

Organizations often face the challenge that certain employee groups leave the company more frequently than others. If attrition is concentrated at specific organizational levels, it may indicate underlying issues related to management practices, workload, compensation, or job satisfaction.

Understanding which employee groups are most likely to leave can help organizations develop targeted retention strategies.

## 1.2 Research Question ##

How does employee attrition differ across organizational management levels?

## 1.3 Sub-questions ##

1. Which management level has the highest attrition rate?
2. Is employee attrition related to salary, job satisfaction, or work experience?
3. Do departments or job roles influence attrition in addition to management level?
4. Is overtime or work-life balance associated with a higher probability of leaving?

## 1.4 Practical Relevance ## 

The results of this analysis may help organizations:
- identify employee groups with higher attrition risk
- understand potential causes of employee turnover
- improve employee retention strategies
- support data-driven HR decision making

# 2. PREPARE #

## 2.1 Data Source ## 

The analysis uses the **IBM HR Analytics Employee Attrition dataset**, which is publicly available on **Kaggle.** The dataset contains information about employees, including demographic characteristics, job roles, salary, job satisfaction, and whether the employee left the organization.

The dataset is provided in CSV format, making it suitable for import into SQL databases and data analysis tools.

## 2.2 Dataset Structure ##

The dataset contains multiple variables describing employees. Important attributes for this analysis include: 

- _Attrition_ – indicates whether the employee left the company
- _JobLevel_ – hierarchical level within the organization (scale from 1 to 5)
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

For interpretative purposes, JobLevel values were assigned descriptive labels to reflect management level:

- _JobLevel 1_ – Entry-level employees
- _JobLevel 2_ – Junior specialists
- _JobLevel 3_ – Middle management / experienced specialists
- _JobLevel 4_ – Senior management
- _JobLevel 5_ – Executive leadership

These labels are not provided in the original dataset but are introduced to support clearer analysis.

## 2.4 Data Credibility Evaluation (ROCCC) ##

The dataset was evaluated using the ROCCC framework, which assesses reliability, originality, comprehensiveness, currency, and citation.

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

BigQuery was chosen because it allows efficient querying of structured datasets using SQL, which is well suited for tasks such as filtering, grouping, and calculating statistics.

Since the main objective of this project is to analyze employee attrition across management levels using aggregated comparisons, SQL provides a clear and efficient approach. While tools such as R are often used for advanced statistical modeling, the focus of this project is data preparation and descriptive analysis, making BigQuery the more appropriate tool.

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

To make the analysis more meaningful and aligned with the research objective, additional variables are created. According to the case study approach, transforming data into a more interpretable structure is a key step in the process phase, as it enables more effective analysis and clearer insights.

In this project, two new variables are created:
- _management_level_ – defines a more detailed organizational hierarchy
- _attrition_flag_ – converts attrition into a numerical format

```sql
CREATE TABLE `hr-analytics-hh.employee_data.hr_attrition_prepared` AS
SELECT
    *,
    CASE
        WHEN JobLevel = 1 THEN 'Entry-level employees'
        WHEN JobLevel = 2 THEN 'Junior specialists'
        WHEN JobLevel = 3 THEN 'Middle management / experienced specialists'
        WHEN JobLevel = 4 THEN 'Senior management'
        WHEN JobLevel = 5 THEN 'Executive leadership'
        ELSE 'Other'
    END AS management_level,
    IF(Attrition, 1, 0) AS attrition_flag
FROM `hr-analytics-hh.employee_data.attrition_performance`;
```

Creating these variables simplifies later analysis and allows clearer comparisons between organizational levels.

## 3.6 Validating the Prepared Dataset ##

Before starting the analysis, the prepared dataset needs to be checked to make sure everything is correct.  This step confirms that the dataset is correctly structured and ready for the analytical phase, where attrition rates will be calculated and compared across management levels.

During this step, the following things are checked:
- the management level grouping is correct
- the attrition_flag variable correctly shows whether an employee left (1) or stayed (0)
- the dataset is ready for analysis

```sql
SELECT 
    management_level, 
    COUNT(*) AS total_employees
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY management_level
ORDER BY total_employees DESC;
```

| Management Level                            | Total Employees |
| ------------------------------------------- | --------------- |
| Entry-level employees                       | 543             |
| Junior specialists                          | 534             |
| Middle management / experienced specialists | 218             |
| Senior management                           | 106             |
| Executive leadership                        | 69              |

The results show how employees are distributed across different management levels in the dataset.

**It is important to note that these numbers include both employees who are currently working and those who have left the company. Therefore, this table does not represent attrition yet, but only the overall structure of the dataset.**

The results indicate that most employees are in entry-level and junior specialist positions, while fewer employees are in higher management levels. This type of distribution is typical in organizations with a hierarchical structure.

At this stage, the main purpose of this analysis is to verify that the management level grouping has been applied correctly and that all categories are present.

This step confirms that the dataset is correctly structured and ready for further analysis, where attrition rates will be calculated separately.

# 4. ANALYSE #

The purpose of the analysis stage is to explore the prepared dataset, identify patterns, and answer the research question:
How does employee attrition differ across organizational management levels?

This stage focuses on aggregating the data, performing descriptive analysis, and identifying relationships between variables.

## 4.1 Attrition by Management Level ##

Understanding attrition across management levels is central to the research question. If certain levels experience higher turnover, it may indicate structural issues such as workload imbalance, lack of career progression, or inadequate compensation. Identifying these differences helps organizations target retention strategies more effectively.

The attrition rate is calculated for each management level by dividing the number of employees who left by the total number of employees in that level. The comparison reveals which level has the highest turnover and how attrition changes across the organizational hierarchy.

```sql
SELECT 
    management_level,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(SUM(attrition_flag) / COUNT(*) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY management_level
ORDER BY attrition_rate_percent DESC;
```

| Management Level                            | Total Employees | Employees Left | Attrition Rate (%) |
| ------------------------------------------- | --------------- | -------------- | ------------------ |
| Entry-level employees                       | 543             | 143            | 26.34              |
| Junior specialists                          | 534             | 52             | 9.74               |
| Middle management / experienced specialists | 218             | 32             | 14.68              |
| Senior management                           | 106             | 5              | 4.72               |
| Executive leadership                        | 69              | 5              | 7.25               |


**Interpretation of Results:**
- The results show that attrition is highest among entry-level employees (26.34%), which is significantly higher than at all other management levels. This indicates that lower-level employees are the most likely to leave the organization.
- Attrition decreases consistently with higher management levels. Middle management shows moderate attrition (14.68%), while senior management (4.72%) and executive leadership (7.25%) have the lowest rates.
- This pattern suggests that employees in higher positions are more stable, likely due to better compensation, career opportunities, and working conditions.
- Overall, there is a clear negative relationship between management level and attrition: as management level increases, attrition decreases.

## 4.2 Salary and Attrition ##

Salary is one of the most fundamental factors influencing employee decisions to stay or leave. If lower-paid employees show higher attrition, it may indicate that compensation is not competitive or does not match job expectations.

To analyze this relationship, salary is examined from two perspectives: 
- income groups
- management levels

### 4.2.1 Attrition by Income Group ###

This analysis examines how employee attrition varies across different salary ranges to determine whether lower income levels are associated with higher turnover.

```sql
SELECT 
    CASE 
        WHEN MonthlyIncome < 3000 THEN 'Low income'
        WHEN MonthlyIncome BETWEEN 3000 AND 7000 THEN 'Medium income'
        ELSE 'High income'
    END AS income_group,
    AVG(attrition_flag) AS attrition_rate
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY income_group;
```

| Income Group  | Avg Monthly Income | Attrition Rate |
| ------------- | ------------------ | -------------- |
| Low income    | <     3000         | 28.61%         |
| Medium income | ~3000–7000         | 12.03%         |
| High income   | >     7000+        | 10.80%         |


The results show that employees in the low-income group have the highest attrition rate (28.61%), which is more than double compared to medium and high-income groups. This indicates a strong relationship between lower salary and higher likelihood of leaving.

Attrition decreases significantly as income increases, suggesting that higher compensation contributes to employee retention.

### 4.2.2 Attrition by Management Level ###

This analysis examines how salary and attrition differ across organizational management levels to understand how compensation relates to turnover within the company hierarchy.

```sql
SELECT
  management_level,
  AVG(MonthlyIncome) AS avg_income,
  AVG(attrition_flag) AS attrition_rate
FROM hr-analytics-hh.employee_data.hr_attrition_prepared
GROUP BY management_level;
```

| Management Level      | Avg Income | Attrition Rate |
| --------------------- | ---------- | -------------- |
| Entry-level employees | 2786.92    | 26.34%         |
| Junior specialists    | 5502.28    | 9.74%          |
| Middle management     | 9817.25    | 14.68%         |
| Senior management     | 15503.78   | 4.72%          |
| Executive leadership  | 19191.83   | 7.25%          |

This analysis shows that entry-level employees have both the lowest average salary and the highest attrition rate, reinforcing the pattern observed in the income group analysis.

Higher management levels earn significantly more and generally have lower attrition rates. For example, senior management has the lowest attrition (4.72%) alongside a high average income.

### 4.2.3 Comparison and Interpretation ###

The two analyses complement each other:
- The income group analysis shows a direct relationship between salary and attrition
- The management level analysis shows how this relationship appears within the organizational structure

**Both results consistently indicate that lower salary is associated with higher attrition.**

However, the management-level analysis also reveals that salary is not the only factor. For example, middle management has a relatively high income but still shows higher attrition (14.68%) compared to junior specialists (9.74%). This suggests that other factors, such as job stress or responsibilities, may also influence attrition.

## 4.3 Job Satisfaction and Attrition by Management Level ##

This analysis examines how employee attrition varies across different job satisfaction levels within each management level. The goal is to determine whether the relationship between job satisfaction and attrition differs across the organizational hierarchy.

```sql
SELECT 
    management_level,
    JobSatisfaction,
    COUNT(*) AS total_employees,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY management_level, JobSatisfaction
ORDER BY 
    CASE 
        WHEN management_level = 'Entry-level employees' THEN 1
        WHEN management_level = 'Junior specialists' THEN 2
        WHEN management_level = 'Middle management / experienced specialists' THEN 3
        WHEN management_level = 'Senior management' THEN 4
        WHEN management_level = 'Executive leadership' THEN 5
        ELSE 6
    END,
    JobSatisfaction;
```
| Row | management_level                                   | JobSatisfaction | total_employees | attrition_rate_percent |
|-----|----------------------------------------------------|-----------------|-----------------|------------------------|
| 1   | Entry-level employees                              | 1               | 106             | 38.68                  |
| 2   | Entry-level employees                              | 2               | 108             | 26.85                  |
| 3   | Entry-level employees                              | 3               | 162             | 26.54                  |
| 4   | Entry-level employees                              | 4               | 167             | 17.96                  |
| 5   | Junior specialists                                 | 1               | 102             | 12.75                  |
| 6   | Junior specialists                                 | 2               | 97              | 9.28                   |
| 7   | Junior specialists                                 | 3               | 163             | 12.88                  |
| 8   | Junior specialists                                 | 4               | 172             | 5.23                   |
| 9   | Middle management / experienced specialists        | 1               | 50              | 20.00                  |
| 10  | Middle management / experienced specialists        | 2               | 37              | 13.51                  |
| 11  | Middle management / experienced specialists        | 3               | 64              | 9.38                   |
| 12  | Middle management / experienced specialists        | 4               | 67              | 16.42                  |
| 13  | Senior management                                  | 1               | 21              | 4.76                   |
| 14  | Senior management                                  | 2               | 22              | 4.55                   |
| 15  | Senior management                                  | 3               | 28              | 7.14                   |
| 16  | Senior management                                  | 4               | 35              | 2.86                   |
| 17  | Executive leadership                               | 1               | 10              | 10.00                  |
| 18  | Executive leadership                               | 2               | 16              | 12.50                  |
| 19  | Executive leadership                               | 3               | 25              | 4.00                   |
| 20  | Executive leadership                               | 4               | 18              | 5.56                   |

The results show a consistent pattern where lower job satisfaction is associated with higher attrition, but the strength of this relationship differs by management level.

- **Entry-level employees**: Attrition is very high at low satisfaction (38.68%) and decreases steadily to 17.96% at the highest satisfaction level. This indicates that job satisfaction is a key driver of turnover at the lowest organizational level.
- **Junior specialists**: Attrition is lower overall, ranging from 12.75% (low satisfaction) to 5.23% (high satisfaction). The relationship exists but is less pronounced.
- **Middle management**: Attrition is highest at low satisfaction (20.00%) and generally decreases, although there is a slight increase at the highest satisfaction level (16.42%), suggesting other influencing factors.
- **Senior management**: Attrition remains low across all satisfaction levels (between 2.86% and 7.14%), indicating limited impact of job satisfaction.
- **Executive leadership**: Attrition is also relatively low (between 4.00% and 12.50%) and does not follow a strong consistent pattern.

Job satisfaction affects attrition across all management levels, but its impact is strongest among entry-level employees. At higher management levels, attrition is lower and less dependent on satisfaction, suggesting that other factors such as salary, job stability, or career commitment play a more significant role.

## 4.4 Work Experience and Attrition ##

Work experience can influence employee stability within an organization. Employees with less experience may leave more frequently due to career exploration, while more experienced employees may stay longer due to stability, higher positions, and stronger organizational commitment.

To analyze this relationship, average years at the company and total working years were first considered across management levels. However, averages alone do not clearly show when employees are most likely to leave.

To better understand attrition patterns over time, a new variable called experience_group was introduced. This groups employees into categories based on their years at the company:
- 0–2 years
- 2–5 years
- 6–10 years
- 10+ years

This transformation allows for clearer interpretation by identifying critical periods in an employee’s career where attrition risk is highest, rather than relying only on average values.

```sql
SELECT 
    management_level,
    CASE 
        WHEN YearsAtCompany < 2 THEN '0–2 years'
        WHEN YearsAtCompany BETWEEN 2 AND 5 THEN '2–5 years'
        WHEN YearsAtCompany BETWEEN 6 AND 10 THEN '6–10 years'
        ELSE '10+ years'
    END AS experience_group,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY management_level, experience_group
ORDER BY 
    CASE 
        WHEN management_level = 'Entry-level employees' THEN 1
        WHEN management_level = 'Junior specialists' THEN 2
        WHEN management_level = 'Middle management / experienced specialists' THEN 3
        WHEN management_level = 'Senior management' THEN 4
        WHEN management_level = 'Executive leadership' THEN 5
        ELSE 6
    END,
    experience_group;
```

| Row | Management Level                                   | Experience Group | Attrition Rate (%) |
|-----|----------------------------------------------------|------------------|--------------------|
| 1   | Entry-level employees                              | 0–2 years        | 44.12              |
| 2   | Entry-level employees                              | 10+ years        | 5.88               |
| 3   | Entry-level employees                              | 2–5 years        | 20.83              |
| 4   | Entry-level employees                              | 6–10 years       | 21.57              |
| 5   | Junior specialists                                 | 0–2 years        | 18.37              |
| 6   | Junior specialists                                 | 10+ years        | 4.29               |
| 7   | Junior specialists                                 | 2–5 years        | 10.94              |
| 8   | Junior specialists                                 | 6–10 years       | 8.52               |
| 9   | Middle management / experienced specialists        | 0–2 years        | 27.78              |
| 10  | Middle management / experienced specialists        | 10+ years        | 12.50              |
| 11  | Middle management / experienced specialists        | 2–5 years        | 11.63              |
| 12  | Middle management / experienced specialists        | 6–10 years       | 15.05              |
| 13  | Senior management                                  | 0–2 years        | 20.00              |
| 14  | Senior management                                  | 10+ years        | 7.02               |
| 15  | Senior management                                  | 2–5 years        | 0.00               |
| 16  | Senior management                                  | 6–10 years       | 0.00               |
| 17  | Executive leadership                               | 0–2 years        | 0.00               |
| 18  | Executive leadership                               | 10+ years        | 10.53              |
| 19  | Executive leadership                               | 2–5 years        | 7.14               |
| 20  | Executive leadership                               | 6–10 years       | 0.00               |

**Key Findings**
- **Highest attrition occurs in early career stages:** Entry-level employees with 0–2 years experience have the highest attrition rate (44.12%), indicating that turnover is most critical in the first years.
- **Attrition decreases with experience:** Across most management levels, employees with longer tenure (10+ years) have significantly lower attrition rates.
- **Entry-level employees are most affected:** The difference between early-career and long-tenure attrition is largest at the entry level, showing that retention challenges are concentrated in this group.
- **Mid-level patterns are moderate:** Junior specialists and middle management show the same trend, but with lower overall attrition.
- **Higher management levels are more stable:** Senior management and executive leadership show low and inconsistent attrition rates, suggesting stronger job stability and long-term commitment.

The analysis shows that employee attrition is strongly related to tenure, particularly in the early stages of employment. Employees are most likely to leave within the first two years, especially at lower management levels.
