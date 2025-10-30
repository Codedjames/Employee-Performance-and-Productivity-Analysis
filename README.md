# Employee Performance and Productivity Analysis

### Project Overview

The **Employee Performance and Productivity Analysis** is a Power BI report designed to provide HR teams and business leaders with actionable insights into employee performance, productivity, satisfaction, and compensation.

The goal of this project is to help organizations make data-driven workforce decisions — from identifying top-performing departments to understanding satisfaction trends, pay equity, and turnover risks.

---

### Dataset Description

The dataset contains detailed employee records across multiple HR dimensions, including:

`Employee_ID`: Unique identifier for each employee.
`Department`: The department in which the employee works (e.g., Sales, HR, IT).
`Gender`: Gender of the employee (Male, Female, Other).
`Age`: Employee's age (between 22 and 60).
`Job_Title`: The role held by the employee (e.g., Manager, Analyst, Developer).
`Hire_Date`: The date the employee was hired.
`Years_At_Company`: The number of years the employee has been working for the company.
`Education_Level`: Highest educational qualification (High School, Bachelor, Master, PhD).
`Performance_Score`: Employee's performance rating (1 to 5 scale).
`Monthly_Salary`: The employee's monthly salary in USD, correlated with job title and performance score.
`Work_Hours_Per_Week`: Number of hours worked per week.
`Projects_Handled`: Total number of projects handled by the employee.
`Overtime_Hours`: Total overtime hours worked in the last year.
`Sick_Days`: Number of sick days taken by the employee.
`Remote_Work_Frequency`: Percentage of time worked remotely (0%, 25%, 50%, 75%, 100%).
`Team_Size`: Number of people in the employee's team.
`Training_Hours`: Number of hours spent in training.
`Promotions`: Number of promotions received during their tenure.
`Employee_Satisfaction_Score`: Employee satisfaction rating (1.0 to 5.0 scale).
`Resigned`: Boolean value indicating if the employee has resigned.

Each record represents an individual employee.

---

### Data Preparation

The data was cleaned and transformed using **Power Query** in Power BI. Key preparation steps included:

```
let
    Source = Excel.Workbook(File.Contents("C:\Users\DELL\Desktop\DataDNA - Employee Performance and Productivity dataset\Onyx Data - DataDNA Dataset Challenge - Employee Performance and Productivity Dataset - October 2024.xlsx"), null, true),
    #"Onyx Data - DataDNA Dataset Cha_Sheet" = Source{[Item="Onyx Data - DataDNA Dataset Cha",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(#"Onyx Data - DataDNA Dataset Cha_Sheet", [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Employee_ID", Int64.Type}, {"Department", type text}, {"Gender", type text}, {"Age", Int64.Type}, {"Job_Title", type text}, {"Hire_Date", type datetime}, {"Years_At_Company", Int64.Type}, {"Education_Level", type text}, {"Performance_Score", Int64.Type}, {"Monthly_Salary", Int64.Type}, {"Work_Hours_Per_Week", Int64.Type}, {"Projects_Handled", Int64.Type}, {"Overtime_Hours", Int64.Type}, {"Sick_Days", Int64.Type}, {"Remote_Work_Frequency", Int64.Type}, {"Team_Size", Int64.Type}, {"Training_Hours", Int64.Type}, {"Promotions", Int64.Type}, {"Employee_Satisfaction_Score", type number}, {"Resigned", type logical}}),
    #"Extracted Date" = Table.TransformColumns(#"Changed Type",{{"Hire_Date", DateTime.Date, type date}}),
    #"Added Custom" = Table.AddColumn(#"Extracted Date", "Age group", each if [Age] >= 22 and [Age] <= 30 then "22 - 30"
else if [Age] >= 31 and [Age] <= 39 then "31 - 39"
else if [Age] >= 40 and [Age] <= 47 then "40 - 47"
else if [Age] >= 48 and [Age] <= 56 then "48 - 56"
else  "57+"),
    #"Reordered Columns" = Table.ReorderColumns(#"Added Custom",{"Employee_ID", "Department", "Gender", "Age", "Age group", "Job_Title", "Hire_Date", "Years_At_Company", "Education_Level", "Performance_Score", "Monthly_Salary", "Work_Hours_Per_Week", "Projects_Handled", "Overtime_Hours", "Sick_Days", "Remote_Work_Frequency", "Team_Size", "Training_Hours", "Promotions", "Employee_Satisfaction_Score", "Resigned"}),
    #"Added Custom1" = Table.AddColumn(#"Reordered Columns", "Monthly_Salary_group", each if [Monthly_Salary] >= 3850 and [Monthly_Salary] <= 5850 then "3850 - 5850"
else if [Monthly_Salary] >= 6000 and [Monthly_Salary] <= 7500 then "6000 - 7500"
else "7700 - 9000"),
    #"Reordered Columns1" = Table.ReorderColumns(#"Added Custom1",{"Employee_ID", "Department", "Gender", "Age", "Age group", "Job_Title", "Hire_Date", "Years_At_Company", "Education_Level", "Performance_Score", "Monthly_Salary", "Monthly_Salary_group", "Work_Hours_Per_Week", "Projects_Handled", "Overtime_Hours", "Sick_Days", "Remote_Work_Frequency", "Team_Size", "Training_Hours", "Promotions", "Employee_Satisfaction_Score", "Resigned"}),
    #"Added Custom2" = Table.AddColumn(#"Reordered Columns1", "Monthly_Salary_range", each if [Monthly_Salary_group] = "3850 - 5850" then "Low Earners"
else if [Monthly_Salary_group] = "6000 - 7500" then "Medium Earners"
else "High Earners"),
    #"Reordered Columns2" = Table.ReorderColumns(#"Added Custom2",{"Employee_ID", "Department", "Gender", "Age", "Age group", "Job_Title", "Hire_Date", "Years_At_Company", "Education_Level", "Performance_Score", "Monthly_Salary", "Monthly_Salary_group", "Monthly_Salary_range", "Work_Hours_Per_Week", "Projects_Handled", "Overtime_Hours", "Sick_Days", "Remote_Work_Frequency", "Team_Size", "Training_Hours", "Promotions", "Employee_Satisfaction_Score", "Resigned"}),
    #"Inserted Year" = Table.AddColumn(#"Reordered Columns2", "Year", each Date.Year([Hire_Date]), Int64.Type),
    #"Reordered Columns3" = Table.ReorderColumns(#"Inserted Year",{"Employee_ID", "Department", "Gender", "Age", "Age group", "Job_Title", "Hire_Date", "Year", "Years_At_Company", "Education_Level", "Performance_Score", "Monthly_Salary", "Monthly_Salary_group", "Monthly_Salary_range", "Work_Hours_Per_Week", "Projects_Handled", "Overtime_Hours", "Sick_Days", "Remote_Work_Frequency", "Team_Size", "Training_Hours", "Promotions", "Employee_Satisfaction_Score", "Resigned"}),
    #"Renamed Columns" = Table.RenameColumns(#"Reordered Columns3",{{"Year", "Hire_Year"}}),
    #"Inserted Month Name" = Table.AddColumn(#"Renamed Columns", "Month Name", each Date.MonthName([Hire_Date]), type text),
    #"Extracted First Characters" = Table.TransformColumns(#"Inserted Month Name", {{"Month Name", each Text.Start(_, 3), type text}}),
    #"Reordered Columns4" = Table.ReorderColumns(#"Extracted First Characters",{"Employee_ID", "Department", "Gender", "Age", "Age group", "Job_Title", "Hire_Date", "Hire_Year", "Month Name", "Years_At_Company", "Education_Level", "Performance_Score", "Monthly_Salary", "Monthly_Salary_group", "Monthly_Salary_range", "Work_Hours_Per_Week", "Projects_Handled", "Overtime_Hours", "Sick_Days", "Remote_Work_Frequency", "Team_Size", "Training_Hours", "Promotions", "Employee_Satisfaction_Score", "Resigned"}),
    #"Renamed Columns1" = Table.RenameColumns(#"Reordered Columns4",{{"Month Name", "Hire_Month"}}),
    #"Added Custom3" = Table.AddColumn(#"Renamed Columns1", "Productivity_Index", each (([Projects_Handled] + ([Overtime_Hours]/[Work_Hours_Per_Week]) * ([Performance_Score]/5)) /100 * 100)),
    #"Removed Columns" = Table.RemoveColumns(#"Added Custom3",{"Productivity_Index"}),
    #"Added Custom4" = Table.AddColumn(#"Removed Columns", "Resigned_num", each if [Resigned] = false then 0 else 1),
    #"Removed Columns1" = Table.RemoveColumns(#"Added Custom4",{"Resigned_num"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Removed Columns1",{{"Resigned", Int64.Type}}),
    #"Renamed Columns2" = Table.RenameColumns(#"Changed Type1",{{"Monthly_Salary_range", "Earning Capacity"}})
in
    #"Renamed Columns2"
```
I created the following DAX measures to help give more context to the insights: 
- Active Employees:
  ```
  Active Employees = CALCULATE(count('Productivity and Performance'[Employee_ID]), 'Productivity and Performance'[Resigned] = 0)
  ```
- Average Age
  ```
  Average Age = AVERAGE('Productivity and Performance'[Age])
  ```
- Average Salary
  ```
  Average Salary = AVERAGE('Productivity and Performance'[Monthly_Salary])
  ```
- Avg Overtime Hours
  ```
  Avg Overtime Hours = AVERAGE('Productivity and Performance'[Overtime_Hours])
  ```
- Avg Performance Score
  ```
  Avg Performance Score = AVERAGE('Productivity and Performance'[Performance_Score])
  ```
- Avg Satisfaction Score
  ```
  Avg Satisfaction score = AVERAGE('Productivity and Performance'[Employee_Satisfaction_Score])
  ```
- Avg Work Hours
  ```
  Avg Work Hours = AVERAGE('Productivity and Performance'[Work_Hours_Per_Week])
  ```
- Departments
  ```
  Departments = DISTINCTCOUNT('Productivity and Performance'[Department])
  ```
- Females
  ```
  Female = CALCULATE(count('Productivity and Performance'[Employee_ID]), 'Productivity and Performance'[Gender] = "Female")
  ```
- Males
  ```
  Male = CALCULATE(count('Productivity and Performance'[Employee_ID]), 'Productivity and Performance'[Gender] = "Male")
  ```
- Max Salary
  ```
  Max Salary = MAX('Productivity and Performance'[Monthly_Salary])
  ```
- Min Salary
  ```
  Min Salary = MIN('Productivity and Performance'[Monthly_Salary])
  ```
- Number of Employees
  ```
  Number of Employees = count('Productivity and Performance'[Employee_ID])
  ```
- Others
  ```
  Others = CALCULATE(count('Productivity and Performance'[Employee_ID]), 'Productivity and Performance'[Gender] = "Other")
  ```
- Resigned Employees
  ```
  Resigned Employees = CALCULATE(count('Productivity and Performance'[Employee_ID]), 'Productivity and Performance'[Resigned] = 1)
  ```

---

### Dashboard Pages & Insights

#### 1️⃣ Overview

<img width="1007" height="721" alt="1" src="https://github.com/user-attachments/assets/6345fe6d-d1ea-479b-8a37-705c3271cec9" />

**Purpose:** Provides a high-level summary of the workforce composition.
**Key Insights:**

* Total Employees: **100K** (90K Active, 10K Resigned)
* Average Age: **41 years**
* Average Salary: **$6,400**
* Majority of employees fall within the **48-56** and **22–30** age groups.
* Gender distribution is nearly balanced
* Employee retention remains high with steady hiring over the years.

---

#### 2️⃣ Performance Evaluation

<img width="1006" height="720" alt="2" src="https://github.com/user-attachments/assets/8084094e-18aa-431c-a60e-e9a3f23d32ea" />

**Purpose:** Assess employee performance across multiple dimensions.
**Insights:**

* **Engineering** and **Operations** departments show the highest average performance scores (~3.02).
* **Master’s degree holders** slightly outperform other education groups.
* Performance improves with **longer tenure**, peaking at 10 years.
* Positive correlation between **performance** and **salary level**.

**Recommendation:**

* Recognize and reward long-tenured employees.
* Encourage mentorship programs from high-performing departments.

---

#### 3️⃣ Productivity Analysis

<img width="1005" height="718" alt="3" src="https://github.com/user-attachments/assets/31688db7-45c9-4916-9853-793a14bfcfd1" />

**Purpose:** Evaluate productivity through workload, remote work, and performance relationships.
**Insights:**

* Employees with **more years at the company** handle more projects (up to 29 on average).
* **Remote work frequency** doesn’t negatively impact performance.
* **Overtime hours** show a non-linear relationship with performance — moderate overtime yields the best results.

**Recommendation:**

* Promote flexible work policies (remote/hybrid) since performance remains stable.
* Avoid excessive overtime to prevent burnout and declining performance.

---

#### 4️⃣ Salary & Compensation Analysis

<img width="1007" height="719" alt="4" src="https://github.com/user-attachments/assets/5413eb3b-5389-4f13-97f3-5443a64d43ac" />

**Purpose:** Explore salary trends across demographics and performance.
**Insights:**

* Average salary is consistent across genders — a sign of **pay equity**.
* **PhD holders** earn the most on average (~$6,414).
* Salary generally increases with **years spent at the company** but dips slightly after 10 years.
* Departments such as **Legal** and **Operations** lead in compensation levels.

**Recommendation:**

* Maintain pay transparency and continue equity reviews.
* Explore retention incentives for senior employees to counter salary stagnation.

---

#### 5️⃣ Satisfaction Analysis

<img width="1007" height="719" alt="5" src="https://github.com/user-attachments/assets/f1c57b62-fe9b-49ac-b53b-6e4047dc1fcb" />

**Purpose:** Analyze employee satisfaction in relation to pay, training, and promotion.
**Insights:**

* Average Satisfaction Score: **3.0/5**, suggesting moderate satisfaction.
* Satisfaction remains consistent across age groups, gender, and education level
* Training hours are highest among **Bachelor’s** and **High School** graduates.
* Slightly higher satisfaction observed among **female employees**.
* Promotions positively correlate with satisfaction, though not strongly.

**Recommendation:**

* Conduct pulse surveys to understand deeper satisfaction drivers.
* Enhance career development programs and transparent promotion pathways.

---

#### 6️⃣ Further Analysis (Decomposition Tree)

<img width="1006" height="719" alt="6" src="https://github.com/user-attachments/assets/7659f88d-3436-46ca-8da4-db37dcf8f21d" />

**Purpose:** Enable interactive exploration of employee distribution across multiple dimensions.
**Breakdown Path:**
`Number of Employees → Department → Job Title → Education Level → Earning Capacity → Gender`

**Insights:**

* The **Marketing** and **Finance** departments hold the largest employee counts.
* **Medium earners** dominate across most job titles.
* Balanced gender distribution at every level reinforces company-wide diversity.

**Recommendation:**

* Use this dynamic breakdown for targeted diversity and inclusion initiatives.
* Analyze retention rates within each subgroup to uncover hidden attrition trends.

---

### Tools Used

* **Microsoft Power BI** – for data modeling and visualization.
* **Power Query** – for data cleaning and preparation.
* **DAX (Data Analysis Expressions)** – for calculated measures and metrics.

---

### Key Takeaways

* The workforce is balanced in gender and age distribution.
* Performance correlates with tenure, education, and moderate workload.
* Compensation is fair, though long-term salary growth may need review.
* Remote work has a neutral or positive effect on performance.
* Employee satisfaction remains steady but shows opportunity for improvement through training and promotions.

---

### Recommendations

1. **Enhance Employee Engagement:** Introduce recognition programs for tenure and productivity.
2. **Promote Career Development:** Increase access to training and mentorship opportunities.
3. **Review Workload Distribution:** Balance overtime hours to optimize productivity.
4. **Sustain Pay Equity:** Continue monitoring salary distribution across roles and genders.
5. **Improve Data Monitoring:** Automate HR dashboards for real-time workforce analytics.

