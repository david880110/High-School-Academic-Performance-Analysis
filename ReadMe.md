![built with Python3](https://img.shields.io/badge/built%20with-Python3-blue.svg)    ![built with SQLite](https://img.shields.io/badge/built%20with-SQLite-red.svg)     

# High School Academic Performance Analysis

![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/hnws_sun0601_CG_Graduation1.jpg)

<p align="center">
  • <a href="#findings">Findings</a>
  • <a href="#technology-Used">Technology Used</a>
</p>

Helping the  school board and mayor make strategic decisions regarding future school budgets and priorities

## Findings 

![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/Summary.png)

![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/Detail.png)


### (1) District Summary

```sql
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
```



### (2) School Summary

```sql
SELECT 

c.School_name,
c.School_type,
c.Total_Budget,
c.Total_Student,
c.Per_Student_Budget,
c.Average_Math_Score,
c.Average_Reading_Score,
c.Average_Overall_Score,
(pass_count*100 / all_count) ||'%' as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_Type,
sum(budget) as Total_Budget,
count(student_id) as Total_Student,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a

inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)
group by School_name
order by Per_Student_Budget
;
```





### (3) Top Performing Schools (By Passing Rate)

```sql
SELECT 

c.School_name,
c.School_type,
c.Total_Budget,
c.Per_Student_Budget,
c.Average_Math_Score,
c.Average_Reading_Score,
c.Average_Overall_Score,
(pass_count*100 / all_count) ||'%' as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)
group by School_name
order by c.Average_Overall_Score
limit 5
;
```



### (4) Math Scores by Grade

```sql
select 

*

from 

(SELECT 
school_name,
avg(math_score) as '9th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '9th'
group by school_name) c

join 

(SELECT 
school_name,
avg(math_score) as '10th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '10th'
group by school_name)
using (school_name)

join 

(SELECT 
school_name,
avg(math_score) as '11th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '11th'
group by school_name)
using (school_name)

join 

(SELECT 
school_name,
avg(math_score) as '12th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '12th'
group by school_name)
using (school_name)
;
```



### (5) Reading Scores by Grade

```sql
select 

*

from 

(SELECT 
school_name,
avg(reading_score) as '9th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '9th'
group by school_name) c

join 

(SELECT 
school_name,
avg(reading_score) as '10th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '10th'
group by school_name)
using (school_name)

join 

(SELECT 
school_name,
avg(reading_score) as '11th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '11th'
group by school_name)
using (school_name)

join 

(SELECT 
school_name,
avg(reading_score) as '12th'
from 
schools_table a
join
students_table b
using (school_name)
where grade = '12th'
group by school_name)
using (school_name)
;
```



### (6) Scores by School Spending

```sql
SELECT 

'Less than $1,000,000' as Spending_Ranges_Per_Student,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where Per_Student_Budget < 1000000

union

SELECT 

'Between $1,000,000 - $2,000,000' as Spending_Ranges_Per_Student,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where Per_Student_Budget between 1000000 and 2000000

union

SELECT 

'Greater than $2,000,000' as Spending_Ranges_Per_Student,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where Per_Student_Budget > 2000000
;
```



### (7) Scores by School Size

```sql
SELECT 

'Small (<1000)' as School_Size,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
count(student_id) as Total_Student,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where Total_Student < 1000

union

SELECT 

'Medium (1000-2000)' as School_Size,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
count(student_id) as Total_Student,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where Total_Student between 1000 and 2000

union

SELECT 

'Large (2000-5000)' as School_Size,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
count(student_id) as Total_Student,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where Total_Student > 2000
;
```



### (8) Scores by School Type

```sql
SELECT 

'Charter' as School_Type,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
count(student_id) as Total_Student,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where School_Type = 'Charter'

union

SELECT 

'District' as School_Type,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

from 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
count(student_id) as Total_Student,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

group by a.School_name) c

join 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
inner join  students_table b
on a.school_name = b.school_name

where reading_score > 70
and math_score > 70

group by a.School_name
order by a.School_name) d

using (School_name)

where School_Type = 'District'
;
```



## Technology Used

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/python%20logo.png" width="240" height="50"/>

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/sqlite%20logo.png" width="240" height="80"/>

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/tableau%20logo.png" width="240" height="60"/>