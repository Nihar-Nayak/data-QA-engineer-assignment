# Data Quality Testing Assignment

Test your ability to validate the correctness of data transformation and business logic in the context of user activity classification and churn detection.

## Scenario:

You are tasked with validating the accuracy of user classification based on their points activity data over time. You are provided with three CSV files:

1. Input File (points_activity.csv): This file contains historical points activity for various users. Each record represents a points accrual or redemption activity, along with its corresponding date. This data will serve as the foundation for determining user activity over time.
2. Intermediate File (user_classification.csv): This file represents the monthly classification of users. Based on their recent points activity, users are categorized as either active, churned, or newly churned. You will validate whether this classification aligns with the business rules.
3. Output File (monthly_user_stats.csv): This is a summary report showing the count of active users, newly churned users, and churn rates for each tenant by month. This file should accurately reflect the classification from the intermediate file.

Your task is to validate whether the output file and the intermediate classifications correctly follow the business rules for determining user status. Some intentional discrepancies have been introduced to test your ability to spot errors and validate the data logic.

## Business Rules:

- Active User: A user who has points activity (either accrual or redemption) within the last two months.
- Churned User: A user who has not had points activity in the last two months but was active prior to that.
- Newly Churned User: A user who has become churned for the first time in the current month.
- Note: Active users are considered only if they engage in either accrual (earning points) or redemption (spending points) activities in the last two months.

## Data Provided

### Input (points_activity.csv)

This table records the historical points activity for each user. Each row represents a points-related activity for a given user.

| Field | Description |
| --- | --- |
| `user_id` | Unique identifier for each user. This allows tracking of points activity on a per-user basis. |
| `tenant_id` | Identifier for the tenant (e.g., business entity, or organization) to which the user belongs. |
| `activity_completed_at` | The date and time when the activity (points accrual or redemption) was completed. This field is essential for determining user activity over time. |
| `activity_type` | Describes the type of points-related activity: `Accrual` (adding points) or `Redemption` (spending points). This helps classify user behaviour. |
| `amount` | The number of points earned or redeemed in this transaction. Positive values represent accruals, and negative values (or redemptions) reduce points. |

```
user_id,tenant_id,activity_completed_at,activity_type,amount
user_id_5,tenant_id_1,2023-11-10,Accrual,100
user_id_6,tenant_id_1,2023-11-12,Redemption,50
user_id_5,tenant_id_1,2024-03-17,Redemption,75
user_id_2,tenant_id_1,2024-01-15,Redemption,200
user_id_4,tenant_id_1,2024-01-17,Redemption,150
user_id_7,tenant_id_1,2024-02-02,Accrual,80
user_id_7,tenant_id_1,2024-02-04,Redemption,60
user_id_3,tenant_id_1,2024-04-15,Redemption,90
user_id_3,tenant_id_1,2024-04-16,Redemption,40
user_id_4,tenant_id_1,2024-03-18,Redemption,120
user_id_1,tenant_id_1,2024-06-01,Redemption,300
user_id_1,tenant_id_1,2024-06-02,Accrual,110
user_id_7,tenant_id_1,2024-06-02,Write Off,30

```

### Intermediate (user_classification.csv)

This table contains the monthly classification of users based on their points activity, determining whether they are active, churned, or newly churned.

| Field | Description |
| --- | --- |
| `tenant_id` | The tenant (e.g., business entity, or organization) to which the user belongs. |
| `user_id` | Unique identifier for each user. This helps link the user to their activity and classification. |
| `month_date` | The date (month and year) for which the user's classification applies. This is the "current" month used to calculate activity or churn. |
| `is_newly_churned` | Boolean flag (`TRUE` or `FALSE`) indicating if the user has churned for the first time in the current month. |
| `is_active` | Boolean flag (`TRUE` or `FALSE`) indicating if the user is currently active, meaning they had points activity within the last two months. |
| `is_churned` | Boolean flag (`TRUE` or `FALSE`) indicating if the user is churned (i.e., no activity in the last two months, but prior activity exists). |

```
tenant_id,user_id,month_date,is_newly_churned,is_active,is_churned
tenant_id_1,user_id_2,2024-01-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_4,2024-01-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_5,2024-01-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_6,2024-01-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_2,2024-02-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_4,2024-02-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_5,2024-02-01,TRUE,FALSE,TRUE
tenant_id_1,user_id_6,2024-02-01,TRUE,FALSE,TRUE
tenant_id_1,user_id_7,2024-02-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_2,2024-03-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_4,2024-03-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_5,2024-03-01,FALSE,FALSE,TRUE
tenant_id_1,user_id_6,2024-03-01,TRUE,FALSE,TRUE
tenant_id_1,user_id_7,2024-03-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_2,2024-04-01,TRUE,FALSE,TRUE
tenant_id_1,user_id_3,2024-04-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_4,2024-04-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_5,2024-04-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_6,2024-04-01,FALSE,FALSE,TRUE
tenant_id_1,user_id_7,2024-04-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_2,2024-05-01,FALSE,FALSE,TRUE
tenant_id_1,user_id_3,2024-05-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_4,2024-05-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_5,2024-05-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_6,2024-05-01,FALSE,FALSE,TRUE
tenant_id_1,user_id_7,2024-05-01,FALSE,FALSE,TRUE
tenant_id_1,user_id_1,2024-06-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_2,2024-06-01,FALSE,FALSE,TRUE
tenant_id_1,user_id_3,2024-06-01,FALSE,TRUE,FALSE
tenant_id_1,user_id_4,2024-06-01,TRUE,FALSE,TRUE
tenant_id_1,user_id_5,2024-06-01,TRUE,FALSE,TRUE
tenant_id_1,user_id_6,2024-06-01,FALSE,FALSE,TRUE
tenant_id_1,user_id_7,2024-06-01,FALSE,FALSE,TRUE

```

### Final (monthly_user_stats.csv)

This table summarises user activity at the tenant level for each month, including counts of active users, newly churned users, and the churn rate.

| Field | Description |
| --- | --- |
| `tenant_id` | Identifier for the tenant (e.g., business entity, or organization) for which the activity summary is being generated. |
| `month_date` | The date (month and year) representing the reporting period for the summary data. |
| `active_users` | The count of users who are classified as active for the specified month, based on their points activity in the last two months. |
| `new_churned_users` | The count of users who have become churned for the first time during the specified month. |
| `churn_rate` | The churn rate for the specified month. This is typically calculated as `(newly churned users / last month's active users)` and reflects user turnover. |

```
tenant_id,month_date,active_users,new_churned_users,churn_rate
tenant_id_1,2024-01-01,4,0,0.00
tenant_id_1,2024-02-01,3,2,0.50
tenant_id_1,2024-03-01,3,0,0.00
tenant_id_1,2024-04-01,4,1,0.33
tenant_id_1,2024-05-01,3,0,0.00
tenant_id_1,2024-06-01,2,2,0.67
```

## Expected Deliverables:

1. SQL Query or Script:
    - You should provide a SQL query or script that identifies users where the classification in the output file does not match the business rules.
    - Include screenshots showing the discrepancies identified.
2. Discrepancy Report:
    - Provide a list of incorrect records found with explanations.
    - Present the results in a format of your choice (Excel, Markdown, etc.).
    - Briefly explain why the classification is wrong and which records are erroneous.
3. Automation Suggestion:
    - Suggest an automation strategy that could prevent such discrepancies in the future.
    - Outline how you would integrate this check into a CI/CD pipeline or schedule it for regular validation.
