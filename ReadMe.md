![built with Python3](https://img.shields.io/badge/built%20with-Python3-blue.svg)    ![built with SQLite](https://img.shields.io/badge/built%20with-SQLite-red.svg)     

# High School Academic Performance Analysis

![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/hnws_sun0601_CG_Graduation1.jpg)

<p align="center">
  • <a href="#findings">Findings</a>
  • <a href="#syntax-detail">Syntax Detail</a>
  • <a href="#technology-Used">Technology Used</a>
</p>

A analysis helping parents to pick the best school based on a 28,000+ data sets, in the aspects of:

* Academic performance, 

* Educational spending, 

* Specific school, 

* School type, 

* School size,

* Math and Reading score in detail

## Findings 

![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/Summary.png)

1. **Per Student Budget does not really coordinates with the academic performance;** Although **<u>District  Schools</u>** typically have greater budget than Charter Schools do,  but their **<u>passing rate are 40% lower</u>** as well as their **<u>overall scores are 9 - 10 points lower</u>** than Charter Schools are

![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/Detail.png)

2. **In terms of Math and Reading, all Charter Schools beat  District Schools in academic performance;** All **<u>District Schools</u>** generate <u>**poor reading score**</u> and **<u>Stewart High has the best performance</u>** among all schools regardless the school type


![alt text](https://raw.githubusercontent.com/david880110/High-School-Academic-Performance-Analysis/master/image/99_Student.png)

3. **Most of the All 99 Students come from Vargas High, Floyd High and Thomas High;** Based on the school size, Greene High generated the most Math 99 students, Vargas High generated the most Reading 99 students

## Syntax Detail

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

INNER JOIN  students_table b
on a.school_name = b.school_name

JOIN
(SELECT
 CAST(sum(mathover70) as float)/CAST(count(mathover70) as float) as Math_Passing_Rate
 FROM(    
SELECT
 student_id
 ,math_score
 , case when math_score > 70 then 1
		else 0
end as mathover70
FROM 
students_table))

JOIN
(SELECT
CAST(sum(readingover70) as float)/CAST(count(readingover70) as float) as Reading_Passing_Rate 
FROM(
SELECT
student_id ,reading_score, 
case when reading_score > 70 then 1
		else 0
end as readingover70
FROM 
students_table));
```

| Total_Schools | Total_Students | Total_Budget | Average_Math_Score | Average_Reading_Score | Math_Passing_Rate | Reading_Passing_Rate | Overall_Passing_Rate |
|:-------------:|:--------------:|:------------:|:------------------:|:---------------------:|:-----------------:|:--------------------:|:--------------------:|
|       11      |      27712     |  57663546353 |       82.1648      |        82.19028       |      0.836894     |       0.751876       |       0.794385       |

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

FROM 

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

INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)
GROUP BY School_name
ORDER BY Per_Student_Budget
;
```

|      School_name      | School_Type | Total_Budget | Total_Student | Per_Student_Budget | Average_Math_Score | Average_Reading_Score | Average_Overall_Score | Overall_Passing_Rate |
|:---------------------:|:-----------:|:------------:|:-------------:|:------------------:|:------------------:|:---------------------:|:---------------------:|:--------------------:|
|    Long High School   |   Charter   |   231503408  |      628      |       368636       |      83.06847      |        93.81051       |        88.43949       |          89%         |
|    Hood High School   |   Charter   |   537967800  |      930      |       578460       |      83.57419      |        94.07742       |        88.82581       |          89%         |
|  Stewart High School  |   Charter   |   894528832  |      1208     |       740504       |      83.77401      |        94.12003       |        88.94702       |          91%         |
|  Thompson High School |   Charter   |  1134977580  |      1353     |       838860       |      83.51589      |        94.1153        |        88.81559       |          91%         |
|   Floyd High School   |   Charter   |  2576406912  |      2104     |       1224528      |      83.02804      |        93.96625       |        88.49715       |          90%         |
|   Vargas High School  |   Charter   |  3902355035  |      2479     |       1574165      |      83.57644      |        93.96168       |        88.76906       |          90%         |
|    Webb High School   |   District  |  5972068832  |      3074     |       1942768      |      81.41542      |        76.55823       |        78.98682       |          49%         |
|   Farmer High School  |   District  |  7666242732  |      3429     |       2235708      |      81.59405      |        77.0175        |        79.30577       |          52%         |
|   Lopez High School   |   District  |  7708776704  |      3428     |       2248768      |      81.72579      |        76.87515       |        79.30047       |          51%         |
| Patterson High School |   District  |  12675265218 |      4389     |       2887962      |      81.48143      |        76.64457       |         79.063        |          50%         |
|   Greene High School  |   District  |  14363453300 |      4690     |       3062570      |      81.69552      |        76.80746       |        79.25149       |          51%         |

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

FROM 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)
GROUP BY School_name
ORDER BY c.Average_Overall_Score
limit 5
;
```

|      School_name      | School_type | Total_Budget | Per_Student_Budget | Average_Math_Score | Average_Reading_Score | Average_Overall_Score | Overall_Passing_Rate |
|:---------------------:|:-----------:|:------------:|:------------------:|:------------------:|:---------------------:|:---------------------:|:--------------------:|
|    Webb High School   |   District  |   5.97E+09   |       1942768      |      81.41542      |        76.55823       |        78.98682       |          49%         |
| Patterson High School |   District  |   1.27E+10   |       2887962      |      81.48143      |        76.64457       |         79.063        |          50%         |
|   Greene High School  |   District  |   1.44E+10   |       3062570      |      81.69552      |        76.80746       |        79.25149       |          51%         |
|   Lopez High School   |   District  |   7.71E+09   |       2248768      |      81.72579      |        76.87515       |        79.30047       |          51%         |
|   Farmer High School  |   District  |   7.67E+09   |       2235708      |      81.59405      |        77.0175        |        79.30577       |          52%         |

### (4) Math Scores by Grade

```sql
SELECT 

*

FROM 

(SELECT 
school_name,
avg(math_score) as '9th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '9th'
GROUP BY school_name) c

JOIN 

(SELECT 
school_name,
avg(math_score) as '10th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '10th'
GROUP BY school_name)
using (school_name)

JOIN 

(SELECT 
school_name,
avg(math_score) as '11th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '11th'
GROUP BY school_name)
using (school_name)

JOIN 

(SELECT 
school_name,
avg(math_score) as '12th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '12th'
GROUP BY school_name)
using (school_name)
;
```

|      school_name      |    9th   |   10th   |   11th   |   12th   |
|:---------------------:|:--------:|:--------:|:--------:|:--------:|
|   Farmer High School  |  82.0167 | 81.55696 | 81.43085 | 81.30319 |
|   Floyd High School   | 83.21698 | 82.91197 | 82.98643 | 83.00204 |
|   Greene High School  | 81.61424 | 80.94024 | 82.14902 | 82.29346 |
|    Hood High School   | 84.63291 | 83.25413 | 83.77723 | 82.53723 |
|    Long High School   | 83.76316 | 81.97006 | 84.05263 | 82.61146 |
|   Lopez High School   | 82.01149 | 81.55498 | 81.52406 |  81.7915 |
| Patterson High School | 81.15685 | 81.79703 | 81.57627 | 81.37435 |
|  Stewart High School  |   83.75  | 84.24013 | 83.34661 | 83.68512 |
|  Thompson High School | 83.37709 | 83.13279 | 83.83113 | 83.81173 |
|   Vargas High School  | 83.81392 | 83.08791 | 83.39641 | 84.06803 |
|    Webb High School   | 81.05882 | 81.46844 | 81.34925 | 81.85479 |

### (5) Reading Scores by Grade

```sql
SELECT 

*

FROM 

(SELECT 
school_name,
avg(reading_score) as '9th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '9th'
GROUP BY school_name) c

JOIN 

(SELECT 
school_name,
avg(reading_score) as '10th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '10th'
GROUP BY school_name)
using (school_name)

JOIN 

(SELECT 
school_name,
avg(reading_score) as '11th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '11th'
GROUP BY school_name)
using (school_name)

JOIN 

(SELECT 
school_name,
avg(reading_score) as '12th'
FROM 
schools_table a
JOIN
students_table b
using (school_name)
WHERE grade = '12th'
GROUP BY school_name)
using (school_name)
;
```

|      school_name      |    9th   |   10th   |   11th   |   12th   |
|:---------------------:|:--------:|:--------:|:--------:|:--------:|
|   Farmer High School  | 77.70045 | 76.52093 | 76.55319 | 77.34441 |
|   Floyd High School   | 93.93396 | 93.85915 | 93.95543 | 94.13673 |
|   Greene High School  | 77.11894 | 76.89561 | 76.50882 | 76.61121 |
|    Hood High School   | 94.32068 | 94.09901 | 94.10891 | 93.70213 |
|    Long High School   | 93.78947 | 93.53293 | 94.03289 | 93.91083 |
|   Lopez High School   | 76.58098 |  77.6444 | 77.69701 | 75.36763 |
| Patterson High School | 76.66058 | 76.14598 | 76.82097 | 77.11679 |
|  Stewart High School  | 94.18681 | 94.17105 | 93.89641 | 94.17647 |
|  Thompson High School | 94.13687 | 94.25203 | 93.93046 | 94.10802 |
|   Vargas High School  | 93.92284 | 93.94368 | 94.00996 | 93.98639 |
|    Webb High School   | 76.16567 | 76.70653 | 77.19851 | 76.20509 |

### (6) Scores by School Spending

```sql
SELECT 

'Less than $1,000,000' as Spending_Ranges_Per_Student,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE Per_Student_Budget < 1000000

union

SELECT 

'Between $1,000,000 - $2,000,000' as Spending_Ranges_Per_Student,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE Per_Student_Budget between 1000000 and 2000000

union

SELECT 

'Greater than $2,000,000' as Spending_Ranges_Per_Student,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

(SELECT count(b.student_name) as all_count,
a.School_name,
type as School_type,
sum(budget) as Total_Budget,
sum(budget)/count(student_id) as Per_Student_Budget,
avg(math_score) as Average_Math_Score,
avg(reading_score) as Average_Reading_Score,
(avg(math_score) + avg(reading_score))/2 as Average_Overall_Score

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE Per_Student_Budget > 2000000
;
```

|   Spending_Ranges_Per_Student   | Average_Math_Score | Average_Reading_Score | Average_Overall_Score | Overall_Passing_Rate |
|:-------------------------------:|:------------------:|:---------------------:|:---------------------:|:--------------------:|
| Between $1,000,000 - $2,000,000 |       82.6733      |        88.16205       |        85.41768       |       76.33333       |
|     Greater than $2,000,000     |       81.6242      |        76.83617       |        79.23018       |          51          |
|       Less than $1,000,000      |      83.48314      |        94.03082       |        88.75698       |          90          |

### (7) Scores by School Size

```sql
SELECT 

'Small (<1000)' as School_Size,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

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
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE Total_Student < 1000

union

SELECT 

'Medium (1000-2000)' as School_Size,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

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
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE Total_Student between 1000 and 2000

union

SELECT 

'Large (2000-5000)' as School_Size,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

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
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE Total_Student > 2000
;
```

|     School_Size    | Average_Math_Score | Average_Reading_Score | Average_Overall_Score | Overall_Passing_Rate |
|:------------------:|:------------------:|:---------------------:|:---------------------:|:--------------------:|
|  Large (2000-5000) |      82.07381      |        81.69012       |        81.88197       |       61.85714       |
| Medium (1000-2000) |      83.64495      |        94.11767       |        88.88131       |          91          |
|    Small (<1000)   |      83.32133      |        93.94396       |        88.63265       |          89          |

### (8) Scores by School Type

```sql
SELECT 

'Charter' as School_Type,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

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
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE School_Type = 'Charter'

union

SELECT 

'District' as School_Type,
avg(c.Average_Math_Score) as Average_Math_Score,
avg(c.Average_Reading_Score) as Average_Reading_Score,
avg(c.Average_Overall_Score) as Average_Overall_Score,
avg((pass_count*100 / all_count) ||'%') as Overall_Passing_Rate

FROM 

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
INNER JOIN  students_table b
on a.school_name = b.school_name

GROUP BY a.School_name) c

JOIN 

(SELECT count(b.student_name) as pass_count,
a.School_name

FROM schools_table a
INNER JOIN  students_table b
on a.school_name = b.school_name

WHERE reading_score > 70
and math_score > 70

GROUP BY a.School_name
ORDER BY a.School_name) d

using (School_name)

WHERE School_Type = 'District'
;
```

| School_Type | Average_Math_Score | Average_Reading_Score | Average_Overall_Score | Overall_Passing_Rate |
|:-----------:|:------------------:|:---------------------:|:---------------------:|:--------------------:|
|   Charter   |      83.42284      |        94.00853       |        88.71569       |          90          |
|   District  |      81.58244      |        76.78058       |        79.18151       |         50.6         |

## Technology Used

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/python%20logo.png" width="240" height="50"/>

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/sqlite%20logo.png" width="240" height="80"/>

<img src="https://raw.githubusercontent.com/david880110/tech-logo/master/tableau%20logo.png" width="240" height="60"/>