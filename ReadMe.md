# High School Academic Performance Analysis

![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/hnws_sun0601_CG_Graduation1.jpg)

<p align="center">
  • <a href="#findings">Findings</a>
  • <a href="#technology-Used">Technology Used</a>
</p>

Helping the  school board and mayor make strategic decisions regarding future school budgets and priorities

## Findings 

### (1) District Summary

'
SELECT 

count(DISTINCT(school_id)) as Total_Schools,
count(student_id) as Total_Students,
sum(budget) as Total_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
math_passing_rate,
reading_passing_rate,
(math_passing_rate + reading_passing_rate)/2 as Overall_Passing_Rate

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

join

(select
cast(sum(mathover70) as float)/cast(count(mathover70) as float) as Math_Passing_Rate
from(
select
 student_id
 ,math_score
 , case when math_score > 70 then 1
		else 0
end as mathover70
from 
students_table))

join

(select
cast(sum(readingover70) as float)/cast(count(readingover70) as float) as Reading_Passing_Rate
from(
select
student_id ,reading_score, 
case when reading_score > 70 then 1
		else 0
end as readingover70
from 
students_table));
'

### (2) School Summary

### (3) Top Performing Schools (By Passing Rate)

### (4) Top Performing Schools (By Passing Rate)

### (5) Math Scores by Grade

### (6) Reading Scores by Grade

### (7) Scores by School Spending

### (8) Scores by School Size

### (9) Scores by School Type

## Technology Used

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/python%20logo.png" width="240" height="50"/>

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/sqlite%20logo.png" width="240" height="80"/>

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/tableau%20logo.png" width="240" height="60"/>