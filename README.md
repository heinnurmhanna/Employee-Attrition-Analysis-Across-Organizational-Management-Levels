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

The dataset is widely used in HR analytics learning environments; however, it is synthetic and may not fully represent real-world organizational behavior.

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

## 4.5 Overtime and Attrition by Management Level ##

The purpose of this section is to examine whether overtime is associated with higher employee attrition and whether this relationship differs across organizational management levels.

Overtime is often linked to increased workload, stress, and reduced work-life balance. If employees who work overtime are more likely to leave the organization, this may indicate that workload management plays a significant role in employee retention.

This analysis compares employees who work overtime with those who do not. In addition, the comparison is extended across management levels to determine whether the impact of overtime varies depending on organizational hierarchy.

### 4.5.1 Overtime and Attrition (overall) ###

```sql
SELECT
    OverTime,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY OverTime
ORDER BY attrition_rate_percent DESC;
```

| OverTime | Total Employees | Employees Left | Attrition Rate (%) |
| -------- | --------------- | -------------- | ------------------ |
| true     | 416             | 127            | 30.53              |
| false    | 1054            | 110            | 10.44              |

Employees who work overtime have a significantly higher attrition rate (30.53%) compared to those who do not (10.44%). This means that attrition is nearly three times higher among employees working overtime, indicating a strong relationship between overtime and employee turnover.

This pattern suggests that factors such as increased workload, stress, or reduced work-life balance may contribute to higher attrition among employees who work overtime. However, this represents an association rather than a causal relationship.

### 4.5.2 Overtime and Attrition (overall) ###

```sql
SELECT
    management_level,
    OverTime,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY management_level, OverTime
ORDER BY
    CASE
        WHEN management_level = 'Entry-level employees' THEN 1
        WHEN management_level = 'Junior specialists' THEN 2
        WHEN management_level = 'Middle management / experienced specialists' THEN 3
        WHEN management_level = 'Senior management' THEN 4
        WHEN management_level = 'Executive leadership' THEN 5
        ELSE 6
    END,
    OverTime;
```
| Management Level                            | OverTime | Total Employees | Employees Left | Attrition Rate (%) |
| ------------------------------------------- | -------- | --------------- | -------------- | ------------------ |
| Entry-level employees                       | false    | 387             | 61             | 15.76              |
| Entry-level employees                       | true     | 156             | 82             | 52.56              |
| Junior specialists                          | false    | 388             | 26             | 6.70               |
| Junior specialists                          | true     | 146             | 26             | 17.81              |
| Middle management / experienced specialists | false    | 155             | 19             | 12.26              |
| Middle management / experienced specialists | true     | 63              | 13             | 20.63              |
| Senior management                           | false    | 73              | 2              | 2.74               |
| Senior management                           | true     | 33              | 3              | 9.09               |
| Executive leadership                        | false    | 51              | 2              | 3.92               |
| Executive leadership                        | true     | 18              | 3              | 16.67              |

Employees who work overtime consistently show higher attrition rates across all management levels. The effect is strongest among entry-level employees, where attrition increases from 15.76% (no overtime) to 52.56% (with overtime). A similar pattern is observed among junior specialists and middle management, although the differences are less extreme.

At higher management levels, attrition remains relatively low overall, but overtime is still associated with increased turnover. For example, senior management attrition rises from 2.74% to 9.09%, and executive leadership from 3.92% to 16.67%. This suggests that overtime has a negative impact across all levels, although its effect is most pronounced among lower-level employees.

Compared to the previous overall analysis, these results provide a more detailed view by showing how overtime affects different groups. While the earlier results showed that overtime is strongly associated with higher attrition in general, this breakdown reveals that the effect is not uniform and is particularly severe among entry-level employees, indicating that workload pressure may disproportionately affect lower-level staff.

## 4.6 Attrition by Department and Job Role ##

The purpose of this section is to analyze whether employee attrition differs across departments and job roles. This helps determine whether turnover is influenced not only by management level but also by the nature of the work and organizational structure.

Different departments and job roles may involve varying workloads, expectations, and working conditions, which can influence employee retention.

This section includes two main analyses:
- Attrition by department
- Attrition by job role

The goal is to identify whether certain departments or roles are associated with higher attrition rates and whether they may help explain differences observed across management levels.

### 4.6.1 Attrition by Department ###

```sql
SELECT
    Department,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY Department
ORDER BY attrition_rate_percent DESC;
```
| Department             | Total Employees | Employees Left | Attrition Rate (%) |
| ---------------------- | --------------- | -------------- | ------------------ |
| Sales                  | 446             | 92             | 20.63              |
| Human Resources        | 63              | 12             | 19.05              |
| Research & Development | 961             | 133            | 13.84              |

Attrition rates differ across departments, with Sales (20.63%) and Human Resources (19.05%) showing noticeably higher turnover compared to Research & Development (13.84%). This suggests that employees in Sales and HR may experience conditions that are more strongly associated with leaving, such as higher pressure, workload, or different job expectations.

In contrast, Research & Development has the lowest attrition rate despite having the largest number of employees. This may indicate more stable working conditions, better job satisfaction, or stronger career development opportunities in that department. Overall, the results suggest that department plays an important role in understanding attrition beyond management level alone.

### 4.6.2 Attrition by Job Role ###

```sql
SELECT
    JobRole,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY JobRole
ORDER BY attrition_rate_percent DESC;
```

| Job Role                  | Total Employees | Employees Left | Attrition Rate (%) |
| ------------------------- | --------------- | -------------- | ------------------ |
| Sales Representative      | 83              | 33             | 39.76              |
| Laboratory Technician     | 259             | 62             | 23.94              |
| Human Resources           | 52              | 12             | 23.08              |
| Sales Executive           | 326             | 57             | 17.48              |
| Research Scientist        | 292             | 47             | 16.10              |
| Manufacturing Director    | 145             | 10             | 6.90               |
| Healthcare Representative | 131             | 9              | 6.87               |
| Manager                   | 102             | 5              | 4.90               |
| Research Director         | 80              | 2              | 2.50               |

Attrition varies significantly across job roles, with the highest turnover observed among Sales Representatives (39.76%), followed by Laboratory Technicians and Human Resources roles (around 23%). This suggests that certain operational or customer-facing roles may experience higher pressure, workload, or less favorable working conditions, leading to increased likelihood of leaving.

In contrast, managerial and senior roles such as Manager (4.90%) and Research Director (2.50%) show much lower attrition rates, indicating greater job stability and retention. Overall, the results suggest that job role is a strong factor in explaining attrition, and that turnover risk is particularly concentrated in lower-level or more operational positions rather than leadership roles.

### 4.6.3 Combined View: Department within Management Levels ###

```sql
SELECT
    management_level,
    Department,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY management_level, Department
ORDER BY
    CASE
        WHEN management_level = 'Entry-level employees' THEN 1
        WHEN management_level = 'Junior specialists' THEN 2
        WHEN management_level = 'Middle management / experienced specialists' THEN 3
        WHEN management_level = 'Senior management' THEN 4
        WHEN management_level = 'Executive leadership' THEN 5
        ELSE 6
    END,
    attrition_rate_percent DESC;
```

| Management Level                            | Department             | Total Employees | Employees Left | Attrition Rate (%) |
| ------------------------------------------- | ---------------------- | --------------- | -------------- | ------------------ |
| Entry-level employees                       | Sales                  | 76              | 32             | 42.11              |
| Entry-level employees                       | Human Resources        | 33              | 10             | 30.30              |
| Entry-level employees                       | Research & Development | 434             | 101            | 23.27              |
| Junior specialists                          | Sales                  | 240             | 37             | 15.42              |
| Junior specialists                          | Research & Development | 281             | 15             | 5.34               |
| Junior specialists                          | Human Resources        | 13              | 0              | 0.00               |
| Middle management / experienced specialists | Human Resources        | 6               | 2              | 33.33              |
| Middle management / experienced specialists | Sales                  | 83              | 17             | 20.48              |
| Middle management / experienced specialists | Research & Development | 129             | 13             | 10.08              |
| Senior management                           | Sales                  | 34              | 4              | 11.76              |
| Senior management                           | Research & Development | 68              | 1              | 1.47               |
| Senior management                           | Human Resources        | 4               | 0              | 0.00               |
| Executive leadership                        | Sales                  | 13              | 2              | 15.38              |
| Executive leadership                        | Research & Development | 49              | 3              | 6.12               |
| Executive leadership                        | Human Resources        | 7               | 0              | 0.00               |

Attrition varies significantly across both management levels and departments. The highest attrition is observed among entry-level employees in Sales (42.11%), followed by entry-level employees in Human Resources (30.30%) and Research & Development (23.27%). This suggests that attrition is particularly concentrated among lower-level employees, especially in Sales-related roles. Across all management levels, Sales consistently shows higher attrition compared to Research & Development, which tends to have the lowest rates.

At higher management levels, attrition remains relatively low, especially in Research & Development and Human Resources, where some groups show near-zero attrition. However, small group sizes (e.g., HR in middle and senior levels) should be interpreted cautiously. Overall, these results indicate that both department and management level jointly influence attrition patterns, with the strongest turnover risk concentrated in lower-level roles within high-pressure departments such as Sales.

When comparing all results from section 4.6, a consistent pattern emerges: Sales and certain operational roles (e.g., Sales Representative) show the highest attrition, while Research & Development and higher-level roles show greater stability. The combined analysis confirms that attrition is not driven by a single factor but is influenced by the interaction of job role, department, and management level, with the highest risk concentrated in entry-level and customer-facing positions.

## 4.7 Combined Analysis of Key Attrition Factors ##

The purpose of this section is to analyze how key factors influencing attrition interact with each other. While previous analyses examined variables individually, this section explores their combined effects to identify high-risk employee groups more precisely. 

This section includes multiple combined analyses:
- Management level and overtime
- Income group and job satisfaction
- Experience and overtime

These combinations provide a deeper understanding of how different factors reinforce or weaken each other.

### 4.7.1 Management Level and Overtime ###

```sql
SELECT
    management_level,
    OverTime,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY management_level, OverTime
ORDER BY
    CASE
        WHEN management_level = 'Entry-level employees' THEN 1
        WHEN management_level = 'Junior specialists' THEN 2
        WHEN management_level = 'Middle management / experienced specialists' THEN 3
        WHEN management_level = 'Senior management' THEN 4
        WHEN management_level = 'Executive leadership' THEN 5
        ELSE 6
    END,
    OverTime;
```

| Management Level                            | OverTime | Total Employees | Employees Left | Attrition Rate (%) |
| ------------------------------------------- | -------- | --------------- | -------------- | ------------------ |
| Entry-level employees                       | false    | 387             | 61             | 15.76              |
| Entry-level employees                       | true     | 156             | 82             | 52.56              |
| Junior specialists                          | false    | 388             | 26             | 6.70               |
| Junior specialists                          | true     | 146             | 26             | 17.81              |
| Middle management / experienced specialists | false    | 155             | 19             | 12.26              |
| Middle management / experienced specialists | true     | 63              | 13             | 20.63              |
| Senior management                           | false    | 73              | 2              | 2.74               |
| Senior management                           | true     | 33              | 3              | 9.09               |
| Executive leadership                        | false    | 51              | 2              | 3.92               |
| Executive leadership                        | true     | 18              | 3              | 16.67              |

Attrition is consistently higher among employees who work overtime across all management levels. The strongest effect is observed among entry-level employees, where attrition increases dramatically from 15.76% (no overtime) to 52.56% (with overtime), indicating that overtime may have a particularly strong impact on lower-level employees.

Although attrition is generally lower at higher management levels, overtime still increases turnover in these groups. For example, senior management attrition rises from 2.74% to 9.09%, and executive leadership from 3.92% to 16.67%. Overall, the results suggest that overtime is an important factor associated with attrition across the entire organizational hierarchy, but its impact is most pronounced among entry-level employees.

### 4.7.2 Income Group and Job Satisfaction ###

```sql
SELECT
    CASE
        WHEN MonthlyIncome < 3000 THEN 'Low income'
        WHEN MonthlyIncome BETWEEN 3000 AND 7000 THEN 'Medium income'
        ELSE 'High income'
    END AS income_group,
    JobSatisfaction,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY income_group, JobSatisfaction
ORDER BY
    CASE
        WHEN income_group = 'Low income' THEN 1
        WHEN income_group = 'Medium income' THEN 2
        ELSE 3
    END,
    JobSatisfaction;

```
| Income Group  | Job Satisfaction | Total Employees | Employees Left | Attrition Rate (%) |
| ------------- | ---------------- | --------------- | -------------- | ------------------ |
| Low income    | 1                | 69              | 30             | 43.48              |
| Low income    | 2                | 83              | 24             | 28.92              |
| Low income    | 3                | 120             | 33             | 27.50              |
| Low income    | 4                | 123             | 26             | 21.14              |
| Medium income | 1                | 131             | 22             | 16.79              |
| Medium income | 2                | 112             | 11             | 9.82               |
| Medium income | 3                | 197             | 31             | 15.74              |
| Medium income | 4                | 200             | 13             | 6.50               |
| High income   | 1                | 89              | 14             | 15.73              |
| High income   | 2                | 85              | 11             | 12.94              |
| High income   | 3                | 125             | 9              | 7.20               |
| High income   | 4                | 136             | 13             | 9.56               |

Attrition is highest among employees with both low income and low job satisfaction, reaching 43.48% at the lowest satisfaction level. Across all income groups, attrition generally decreases as job satisfaction increases, indicating a consistent negative relationship between satisfaction and turnover. However, this effect is strongest in the low-income group, where attrition remains relatively high even at higher satisfaction levels.

In medium and high-income groups, attrition rates are significantly lower overall and decline more clearly with increasing job satisfaction. This suggests that higher income may buffer the negative impact of low satisfaction. Overall, the results indicate that income and job satisfaction interact, with the highest attrition risk concentrated among employees who experience both low pay and low satisfaction.

### 4.7.3 Experience and Overtime ###

```sql
SELECT
    CASE
        WHEN YearsAtCompany < 2 THEN '0–2 years'
        WHEN YearsAtCompany BETWEEN 2 AND 5 THEN '2–5 years'
        WHEN YearsAtCompany BETWEEN 6 AND 10 THEN '6–10 years'
        ELSE '10+ years'
    END AS experience_group,
    OverTime,
    COUNT(*) AS total_employees,
    SUM(attrition_flag) AS employees_left,
    ROUND(AVG(attrition_flag) * 100, 2) AS attrition_rate_percent
FROM `hr-analytics-hh.employee_data.hr_attrition_prepared`
GROUP BY experience_group, OverTime
ORDER BY
    CASE
        WHEN experience_group = '0–2 years' THEN 1
        WHEN experience_group = '2–5 years' THEN 2
        WHEN experience_group = '6–10 years' THEN 3
        ELSE 4
    END,
    OverTime;
```
| Experience Group | OverTime | Total Employees | Employees Left | Attrition Rate (%) |
| ---------------- | -------- | --------------- | -------------- | ------------------ |
| 0–2 years        | false    | 146             | 37             | 25.34              |
| 0–2 years        | true     | 69              | 38             | 55.07              |
| 2–5 years        | false    | 399             | 36             | 9.02               |
| 2–5 years        | true     | 162             | 51             | 31.48              |
| 6–10 years       | false    | 328             | 24             | 7.32               |
| 6–10 years       | true     | 120             | 31             | 25.83              |
| 10+ years        | false    | 181             | 13             | 7.18               |
| 10+ years        | true     | 65              | 7              | 10.77              |

Attrition is highest among employees with short tenure who also work overtime. In the 0–2 years group, attrition increases sharply from 25.34% (no overtime) to 55.07% (with overtime), indicating that early-career employees are particularly sensitive to workload pressure. A similar pattern is observed in the 2–5 and 6–10 year groups, where overtime significantly increases attrition rates.

In contrast, employees with 10+ years of experience show much lower attrition overall, and the impact of overtime is less pronounced. This suggests that more experienced employees are more stable and less affected by overtime. Overall, the results indicate that overtime has the strongest effect on attrition during the early stages of employment.

## 4.8 Summary of Analysis ## 

**Attrition is highest at the lowest management level.**

The analysis shows that entry-level employees have the highest attrition rate at 26.34%. In comparison, attrition is much lower among senior management and executive leadership. This means that employees at the bottom of the organizational hierarchy are the most likely to leave the company.

**Salary, job satisfaction, and work experience are all related to attrition.**

Employees with low income have the highest attrition rate, while employees with medium and high income leave less often. A similar pattern appears with job satisfaction: lower satisfaction is associated with higher attrition, especially among entry-level employees. Work experience also matters, because employees with 0–2 years at the company are much more likely to leave than employees with longer tenure. Together, these results show that attrition is especially high among employees who are early in their careers, less satisfied, and lower paid.

**Department and job role also affect attrition.**

Attrition is not explained by management level alone. The department results show that Sales and Human Resources have higher attrition than Research & Development. The job role analysis shows that the highest attrition is found in roles such as Sales Representative and Laboratory Technician, while managerial and director-level roles have much lower attrition. This suggests that the type of work also plays an important role in employee turnover.

**Overtime is strongly associated with attrition.**

Employees who work overtime have a much higher attrition rate than those who do not. This pattern appears across all management levels, but it is strongest among entry-level employees. The same effect is visible when work experience is included: employees with short tenure and overtime show especially high attrition. This suggests that workload may be an important factor behind employee turnover.

**Overall conclusion**

The main research question asked how employee attrition differs across organizational management levels. The answer is that attrition is highest among entry-level employees and generally lower at higher management levels. However, management level is not the only factor. Attrition is also related to salary, job satisfaction, work experience, department, job role, and overtime. The highest risk of leaving is concentrated among lower-level employees, especially those who are lower paid, less satisfied, early in their careers, and working overtime.

# 5. SHARE #

The purpose of the share stage is to communicate the key findings of the analysis in a clear and accessible way. While the previous section focused on numerical analysis and interpretation, visualizations help highlight patterns, trends, and relationships more effectively.

Visual representations are particularly useful in identifying differences across groups, understanding the strength of relationships between variables, and supporting data-driven decision-making. In this study, visualizations are used to illustrate how employee attrition varies across management levels and how it is influenced by salary, job satisfaction, work experience, department, and overtime.

## 5.1 Attrition by Management Level ##

This visualization presents attrition rates across different management levels using a bar chart.

This view is important because it directly addresses the main research question: how attrition differs across organizational hierarchy. By comparing attrition rates visually, it becomes easier to identify which levels experience the highest turnover.

<img width="999" height="799" alt="Dashboard 2" src="https://github.com/user-attachments/assets/5691188f-04f7-4ec7-a067-6c26cc83b675" />

The visualization shows that attrition is highest among entry-level employees (26.3%), significantly exceeding all other management levels, which indicates that lower-level employees are the most likely to leave the organization. Attrition decreases as management level increases, with middle management showing moderate turnover (14.7%), while junior, executive, and especially senior management levels have considerably lower attrition rates. This pattern suggests a clear negative relationship between organizational level and attrition, where employees in higher positions are more stable, likely due to better compensation, career progression, and job security.

## 5.2 Attrition by Income Group ##

This visualization shows attrition rates across income groups (low, medium, and high) using a bar chart.

This view is important because it helps assess whether compensation is related to employee turnover. Salary is a fundamental factor in employment decisions, and visualizing attrition across income levels makes it easier to evaluate its impact.

<img width="999" height="799" alt="Dashboard 1" src="https://github.com/user-attachments/assets/e898305e-5059-4f15-87ab-15afb2885c8f" />

The visualization shows a clear relationship between income level and attrition, with employees in the low-income group experiencing the highest attrition rate (28.6%), which is more than double compared to medium (12.0%) and high-income groups (10.8%). Attrition decreases as income increases, indicating that higher compensation is associated with better employee retention. This suggests that salary plays a significant role in employees’ decisions to stay or leave, and that improving compensation, particularly for lower-paid employees, could be an effective strategy for reducing turnover.

## 5.3 Job Satisfaction and Attrition by Management Level ##

This visualization presents the relationship between job satisfaction and attrition across management levels, for example using a grouped bar chart or heatmap.

This view is important because it allows comparison of how job satisfaction affects different employee groups. Instead of looking at satisfaction in isolation, this visualization shows whether its impact varies across the organizational hierarchy.

<img width="999" height="799" alt="Dashboard 3" src="https://github.com/user-attachments/assets/08a7980e-f190-4140-9268-0253edc0a950" />

This heatmap shows that job satisfaction has a strong impact on attrition, particularly at the entry-level, where employees with low satisfaction (level 1) have the highest attrition rate (39%), which steadily decreases as satisfaction increases. In contrast, higher management levels such as senior and executive positions exhibit consistently low attrition regardless of satisfaction levels, indicating greater stability. While job satisfaction still influences attrition among junior and middle management employees, the effect is less pronounced than at the entry level.

## 5.4 Attrition by Work Experience ##

This visualization shows attrition rates across different experience groups (e.g., 0–2 years, 2–5 years, etc.), using a bar chart or line chart.

This view is important because it helps identify critical periods in an employee’s career where attrition risk is highest. Instead of relying on averages, grouping employees by tenure provides a clearer picture of when employees are most likely to leave.

The visualization typically shows that attrition is highest during the first two years and decreases with longer tenure. This highlights the importance of early-career retention strategies and suggests that organizations should focus on supporting employees during their initial period.

<img width="999" height="799" alt="Dashboard 3 (1)" src="https://github.com/user-attachments/assets/bcede8c5-e5b4-4d71-912e-b5f097f2d833" />

The visualization shows a clear relationship between work experience and employee attrition, with the highest attrition rates occurring during the first 0–2 years at the company. As employee tenure increases, attrition steadily decreases, indicating that employees become more stable over time. This pattern suggests that the early stage of employment is the most critical period for retention, as employees are more likely to leave during their initial years.

## 5.5 Attrition by Overtime ##

This visualization compares attrition rates between employees who work overtime and those who do not.

This view is important because it helps evaluate the role of workload in employee turnover. Overtime is often associated with stress and reduced work-life balance, and visualizing its impact makes this relationship more apparent.

<img width="999" height="799" alt="Dashboard 5" src="https://github.com/user-attachments/assets/6f12593c-977e-45cd-804a-c6725beaf3d2" />

The visualization shows a strong relationship between overtime and employee attrition, with employees who work overtime experiencing a significantly higher attrition rate (30.5%) compared to those who do not (10.4%). This indicates that employees working overtime are nearly three times more likely to leave the organization. The results suggest that increased workload, stress, and reduced work-life balance associated with overtime may be key factors driving employee turnover, highlighting the importance of managing workload to improve retention.

## 5.6. Attrition by Department and Job Role ##

This visualization presents attrition rates across departments and job roles, using bar charts.

This view is important because it highlights differences in attrition that are not explained by management level alone. It allows identification of specific organizational units or roles where turnover is concentrated.

<img width="999" height="799" alt="Dashboard 6" src="https://github.com/user-attachments/assets/858f24e1-6ff9-40f7-a9ff-544d5f93c2e7" />

The visualization shows that attrition varies across departments, with Sales having the highest attrition rate (20.6%), followed by Human Resources (19.0%), while Research & Development has the lowest (13.8%). This indicates that turnover is more concentrated in certain departments, particularly in Sale

<img width="999" height="799" alt="Dashboard 7" src="https://github.com/user-attachments/assets/779b8d6d-6448-4629-89b4-7a6b80892489" />

The visualization shows significant differences in attrition across job roles, with Sales Representatives experiencing by far the highest attrition rate (39.8%), followed by Laboratory Technicians (23.9%) and Human Resources roles (23.1%). In contrast, managerial and senior positions such as Research Director (2.5%) and Manager (4.9%) have the lowest attrition rates. This indicates that turnover is concentrated in more operational and customer-facing roles, while higher-level and specialized positions are more stable, suggesting that job role is a key factor influencing employee attrition.

## 5.7 Combined View: Management Level and Overtime ##

This visualization combines management level and overtime to show how workload affects different employee groups, typically using a grouped bar chart.

This view is important because it reveals interactions between variables. Instead of analyzing overtime separately, it shows how its impact varies across the organizational hierarchy.

<img width="999" height="799" alt="Dashboard 8" src="https://github.com/user-attachments/assets/0d36488b-371b-4d66-b5e3-12511cd6d0a1" />

The visualization shows that employee attrition is consistently higher among those who work overtime across all management levels. The difference is especially pronounced at the entry level, where attrition increases dramatically from 15.8% (no overtime) to 52.6% (with overtime). A similar, though less extreme, pattern can be observed among junior and middle management employees, indicating that overtime is strongly associated with higher turnover.

At higher management levels, overall attrition remains relatively low, but overtime still increases the likelihood of leaving. This suggests that workload and work-life balance are important factors influencing employee retention across the organization. Overall, the results indicate that overtime has the strongest negative impact on lower-level employees, highlighting the need for targeted workload management and retention strategies, particularly at the early stages of employees’ careers.

## 5.8 Summary of Visual Findings ##

The visualizations confirm that employee attrition is not evenly distributed across the organization. Instead, it is concentrated among entry-level employees, lower-income groups, and employees with short tenure. Additional patterns show that overtime and job satisfaction further influence attrition, especially when combined with other risk factors.

Overall, the visual analysis supports the conclusion that attrition is influenced by multiple interacting factors. By presenting these relationships visually, the findings become easier to interpret and more useful for identifying high-risk employee groups and informing targeted retention strategies

# 6. ACT #

The purpose of this stage is to translate the analytical findings into clear and practical recommendations for reducing employee attrition. The analysis showed that attrition is not evenly distributed across the organization but is concentrated among specific groups, particularly entry-level employees, lower-income employees, and those working overtime. Therefore, targeted actions are required.

### Key Recommendations ###

**1. Strengthen retention at the entry level**

Entry-level employees have the highest attrition rate, especially during the first two years. This indicates that early career stages are critical for retention. Organizations should improve onboarding processes, provide mentoring, and ensure clear career development opportunities. Regular feedback during the first year can help identify issues early.

**2. Manage workload and reduce overtime**

Overtime is strongly associated with higher attrition across all management levels, with the strongest effect at the entry level. Companies should monitor overtime, redistribute workload where needed, and ensure sufficient staffing. Reducing excessive workload can improve both retention and employee well-being.

**3. Review compensation for lower-income employees**

The analysis shows that lower-paid employees are more likely to leave the organization. It is important to review salary levels, ensure competitive compensation, and provide clear pay progression. Financial incentives may be particularly effective in high-turnover roles.

**4. Improve job satisfaction**

Low job satisfaction is closely linked to higher attrition, especially among entry-level employees. Organizations should regularly collect employee feedback, improve communication between managers and employees, and provide recognition and development opportunities.

**5. Apply targeted strategies in high-risk departments and roles**

Attrition is higher in departments such as Sales and in operational roles. This suggests the need for department-specific actions, such as reviewing workload, performance expectations, and working conditions in these areas.


### Priority Areas ###

Based on the findings, the most important areas to address are:
- Early-career employees (0–2 years)
- Employees working overtime
- Low-income roles and high-risk departments (e.g., Sales)

Focusing on these groups is likely to have the greatest impact on reducing overall attrition.

### Expected Benefits ###

Implementing these recommendations can lead to:
- reduced employee turnover and recruitment costs
- improved employee satisfaction and engagement
- greater workforce stability
- more effective, data-driven HR decisions

In conclusion, employee attrition can be reduced by focusing on the groups with the highest risk and addressing key factors such as workload, compensation, and job satisfaction. Rather than applying a single general solution, organizations should use targeted and data-driven strategies to improve retention outcomes.

