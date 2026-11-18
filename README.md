# Bank Customer Churn Analysis (Power BI)
 
An interactive Power BI dashboard that looks at why customers leave a bank. I built it as a data analyst project: cleaned the data, set up a star schema, wrote the DAX, and designed a three-page report that answers one question: **who is leaving, and what do they have in common?**
 
Out of 10,000 customers in the dataset, **2,037 (20.4%) exited**. The dashboard breaks that number down by geography, gender, age, products held and activity status so a retention team can see where to focus.
 
## What's in this repo
 
```
PowerBI Project/
├── Bank Churn Dashboard.pbix          # the full report (open in Power BI Desktop)
├── Bank Churn Dashboard.pbit          # template version of the same report
└── Bank Churn table datasets/
    ├── Bank_Churn.xlsx                # main fact table (10,000 rows)
    ├── CustomerInfo.xlsx              # customer IDs and surnames
    ├── Geography.xlsx                 # France, Spain, Germany
    ├── Gender.xlsx                    # gender lookup
    ├── CreditCard.xlsx                # credit card holder / non-holder
    ├── ActiveCustomer.xlsx            # active / inactive member
    └── ExitCustomer.xlsx              # exit / retain
```
 
## Data model
 
I used a star schema with `Bank_Churn` as the fact table and the smaller lookup tables around it.
 
| Relationship | Column |
| --- | --- |
| Bank_Churn → Geography | GeographyID |
| Bank_Churn → Gender | GenderID |
| Bank_Churn → CreditCard | HasCrCard → CreditID |
| Bank_Churn → ActiveCustomer | IsActiveMember → ActiveID |
| Bank_Churn → Exit | Exited → ExitID |
| CustomerInfo → Bank_Churn | CustomerId |
 
ID columns are hidden from the report view so only the readable fields show up when building visuals.
 
### Data cleaning (Power Query)
 
- Promoted headers and set proper data types (integers, decimals, dates)
- Removed duplicate rows on `RowNumber` and `CustomerId`
- Removed duplicate customers from `CustomerInfo` and replaced null IDs
- Converted `Bank DOJ` (date of joining) to a date type
## DAX
 
**Calculated columns**
 
| Column | Logic |
| --- | --- |
| Customer Age Group | Young (30 or under), Middle Age (31–50), Senior (over 50) |
| Tenure Category | New (3 years or less), Loyal (more than 3 years) |
| Balance Status | Zero balance vs has balance |
 
**Measures**
 
```DAX
Total_Customers  = COUNT(CustomerInfo[CustomerId])
Exited_Customers = CALCULATE(COUNT(Bank_Churn[CustomerId]), Bank_Churn[Exited] = 1)
Retained_Customers = [Total_Customers] - [Exited_Customers]
Retention_Rate   = DIVIDE([Total_Customers] - [Exited_Customers], [Total_Customers], 0)
Exited_Rate      = DIVIDE([Exited_Customers], [Total_Customers], 0)
Active_Member_per = DIVIDE(SUM(Bank_Churn[IsActiveMember]), [Total_Customers], 0)
Average_Balance  = AVERAGE(Bank_Churn[Balance])
Average_Salary   = AVERAGE(Bank_Churn[EstimatedSalary])
```
 
I also added a few helper measures (`Active_Member`, `Inactive`, `Credit_holder`, `Non_credit_holders`) for the funnel and pie charts.
 
## Dashboard pages
 
**1. Customer Overview**
Cards for total customers, retention rate and active member %. A donut chart for gender split, a bar chart for customers by country, and a treemap for age groups. Slicers for geography, gender and age.
 
**2. Churn Analysis**
Exited vs retained, churn by geography and gender (stacked columns), a funnel from active to inactive to exited, and a line chart of average balance by age.
 
**3. Product & Credit Analysis**
Credit card holders vs non-holders, exits by number of products, a matrix of exit rate by geography, and a gauge for retention rate.
 
There are also two tooltip pages (credit score by gender, balance by location) that show up when you hover over visuals.
 
## What the data says
 
These numbers come from the dataset itself (10,000 customers, overall churn 20.4%):
 
- **Germany churns the most.** 32.4% of German customers left, roughly double France (16.2%) and Spain (16.7%).
- **Women leave more than men.** 25.1% vs 16.5%.
- **Age matters a lot.** Seniors (over 50) churn at 44.6%, middle-aged customers at 19.6%, and young customers (30 or under) at just 7.5%.
- **Inactive members are a warning sign.** 26.9% of inactive members left vs 14.3% of active ones. Only about half the customer base (51.5%) is active.
- **Number of products has a strange pattern.** Customers with 2 products churn the least (7.6%), but those with 1 product churn at 27.7%, and almost everyone with 3 or 4 products left (82.7% and 100%, though those groups are small: 266 and 60 customers).
- **Credit card ownership doesn't seem to matter.** Exit rates are about the same for holders (20.2%) and non-holders (20.8%).
**Where I'd focus retention efforts:** German customers, older customers, inactive members, and customers sitting on a single product. The 3 to 4 product group deserves a closer look to understand why they leave.
 

## Tools used
 
Power BI Desktop, Power Query, DAX
 
## About me
 
I'm Narayana Reddy, a data science student working toward a Data Analyst role. You can find my other projects on 
- [narayanareddy7129](https://github.com/narayanareddy7129)
- [LinkedIn](https://www.linkedin.com/in/<muvva-lakshmi-narayana-reddy-5933a3299>)
 
