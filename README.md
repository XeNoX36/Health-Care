# healthcare Analysis SQL-Project

## Schema
  
```sql
select *
from hospital
```
## 1. Total revenue per day

```sql  
select day(date) as day, (sum(Medication_Revenue) + sum(Consultation_Revenue)) as Total
from hospital
group by Date
order by Total desc
-- 11th had the max revenue per day
```
  
## 2. Revenue generated per doctor type
```sql
select Doctor_Type, 
(sum(Medication_Revenue) + sum(Consultation_Revenue)) as Total
from hospital
group by Doctor_Type
order by Total desc
-- Anchors generate the most revenue
```

## 3. Total lab cost per day/month
```sql
select day(Date), 
(sum(Lab_Cost)) as Total
from hospital
group by Date
order by Total desc
-- 6th had the max lab cost
```

## 4. No of patient per doctor type
```sql
select Doctor_Type, COUNT(Patient_ID) as No_of_patients
from hospital
group by Doctor_Type
order by No_of_patients desc
-- Anchors attend to the most No. of patients
```
## 5. Revenue generated per financial class
```sql
select Financial_Class, 
round(sum(Medication_Revenue) + sum(Consultation_Revenue), 2) as Total_Revenue
from hospital
group by Financial_Class
order by Total_Revenue desc
```

# 6. No of patients seen per day, find the max
```sql
select day(date) as day, COUNT(patient_id) as no_of_patients
from hospital
group by Date
order by no_of_patients desc
-- 11th was the max
```

## 7. Duration of treatment (entry-completion time)

## per patient
```sql
select Patient_ID, datediff(hour, Entry_Time, Completion_Time) as duration
from hospital
group by Patient_ID, Entry_Time, Completion_Time
order by duration desc
```
## per financial class
```sql
select Financial_Class, sum(datediff(hour, Entry_Time, Completion_Time)) as duration
from hospital
group by Financial_Class
order by duration desc
```
## 8. Doctor efficiency (total revenues/total no of patients)
```sql
with cte1 as(
select Doctor_Type, 
(sum(Medication_Revenue) + sum(Consultation_Revenue)) as Total
from hospital
group by Doctor_Type
),
cte2 as(
select Doctor_Type, COUNT(Patient_ID) as No_of_patients
from hospital
group by Doctor_Type
)
select cte1.Doctor_Type, 
round(CASE
	when cte1.Doctor_Type = 'ANCHOR' then Total/No_of_patients
	when cte1.Doctor_Type = 'FLOATING' then Total/No_of_patients
	when cte1.Doctor_Type = 'LOCUM' then Total/No_of_patients
END, 2) as Doctor_efficiency
from cte1
join cte2
		on cte1.Doctor_Type = cte2.Doctor_Type
		order by Doctor_efficiency desc
-- though ANCHOR generates the highest total revenue, LOCUM generates
-- the most revenues per individual paitient i.e LOCUM as more productivity
```

## 9. Max, Min Medication Revenue per Doctor type, financial class
```sql
with cte as(
select Doctor_Type, sum(Medication_Revenue) as Total_Med_Rev
from hospital
group by Doctor_Type
)
select Doctor_Type, Total_Med_Rev,
row_number() over(order by Total_Med_Rev desc) as ranking
from cte
```

## 10. financial class with most patient entries
```sql
select Financial_Class, COUNT(Entry_Time) as No_oF_entries
from hospital
group by Financial_Class
order by No_oF_entries desc
-- INSURANCE as the most patient entries
```

## 11. Time with most patient entries
```sql
select Entry_Time, COUNT(Entry_Time) as No_oF_entries
from hospital
group by Entry_Time
order by No_oF_entries desc
--18:07:47 (6:07PM) as the most patient entries
```
