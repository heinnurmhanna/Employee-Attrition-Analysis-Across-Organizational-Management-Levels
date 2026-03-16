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

The analysis uses the IBM HR Analytics Employee Attrition dataset, available on Kaggle. The dataset contains employee information related to demographics, job characteristics, salary, job satisfaction, and employment history. The dataset is commonly used in HR analytics and provides a suitable foundation for studying employee attrition patterns.

## 2.2 Key Variables Used in the Analysis ##

Important variables for this study include:
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
