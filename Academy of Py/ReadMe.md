
# Pycity Schools Analysis
### Observed Trend 1: Bailey High, Figueroa High, Ford High, Hernandez High, Huang High, Johnson High and Rodriguez High are schools that have the relatively low passing rate of math

### Observed Trend 2: Small and Medium size school tend to have higher overall passing rate than Large size ones do

### Observed Trend 3: Based upon the data given, charter schools tend to have higher overall passing rate than district schools do


```python
# Dependencies
import pandas as pd

# Load csv files
schools_file = 'schools_complete.csv'
students_file = 'students_complete.csv'

# Read school file with pandas
school_df = pd.read_csv(schools_file)

# Check columns of schools data 
school_df.columns

# Rename columns in school dataset
school_ds = school_df.rename(columns={'name' : 'School','type' : 'Type','size' : 'Size','budget' : 'Budget'})

# Read student file with pandas
student_df = pd.read_csv(students_file)

# Check columns of students data 
student_df.columns

# Rename columns in student dataset
student_ds = student_df.rename(columns={'name' : 'Student','gender' : 'Gender','grade' : 'Grade',
                           'school' : 'School','reading_score' : 'Reading_Score', 'math_score' : 'Math_Score'})

# Merge school and student dataframes
smry_table = pd.merge(school_ds, student_ds, on=('School'))
```

# District Summary


```python
# Find the total schools, students and budget from school dataframe
Total_Schools = smry_table["School"].nunique()
Total_Students = smry_table["Student ID"].nunique()
Total_Budget = school_ds["Budget"].sum()

# Find average Math and Reading score
avg_math_score = smry_table["Math_Score"].mean()
avg_reading_score = smry_table["Reading_Score"].mean()

# Calculate the total count of passing Math, reading and overall passing count
count_passing_math = smry_table[smry_table["Math_Score"] > 60].count()["School"]
count_passing_reading = smry_table[smry_table["Reading_Score"] > 60].count()["School"]
overall_passing_count = smry_table[(smry_table["Math_Score"] > 60) & (smry_table["Reading_Score"] > 60)].count()["School"]

# Calculate Percentage Passing Math / Reading and overall passing both
percent_passing_math = (count_passing_math / Total_Students) * 100
percent_passing_reading = (count_passing_reading / Total_Students) * 100
percent_passing_both = (overall_passing_count / Total_Students) * 100

# Convert to data frame
District_Summary = pd.DataFrame({"Total Schools":[Total_Schools], "Total Students":[Total_Students],
                                "Total Budget":[Total_Budget], "Average Math Score":[avg_math_score],
                                 "Average Reading Score":[avg_reading_score], "% Passing Math":percent_passing_math,
                                 "% Passing Reading":percent_passing_reading, "Overall Passing Rate": percent_passing_both})

# Data munging
District_Summary = District_Summary[["Total Schools", "Total Students", "Total Budget", "Average Math Score",
                                 "Average Reading Score", "% Passing Math", "% Passing Reading", "Overall Passing Rate"]]
# District Summary Table
District_Summary
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>78.985371</td>
      <td>81.87784</td>
      <td>90.906306</td>
      <td>100.0</td>
      <td>90.906306</td>
    </tr>
  </tbody>
</table>
</div>



# School Summary


```python
# Determine the school type
school_types = school_ds.set_index(["School"])["Type"]

# Calculate total student count per school
student_cnt_per_school = smry_table["School"].value_counts()

# Calculate Per Student Budget
per_school_budget = smry_table.groupby(["School"]).mean()["Budget"]
per_student_budget = per_school_budget/ student_cnt_per_school

# calculate average math, reading scores and overall passing rate
avg_math_score = smry_table.groupby(["School"]).mean()["Math_Score"]
avg_reading_score = smry_table.groupby(["School"]).mean()["Reading_Score"]

# calculate % Passing Math and % Passing Reading
school_passing_math =  smry_table[smry_table["Math_Score"] > 60].groupby("School").count()["Student"]
school_passing_reading =  smry_table[smry_table["Reading_Score"] > 60].groupby("School").count()["Student"]

percent_passing_math = school_passing_math / student_cnt_per_school * 100
percent_passing_reading = school_passing_reading / student_cnt_per_school * 100
overall_passing_rate = (percent_passing_math + percent_passing_reading) / 2

# Convert to data frame
school_smry = pd.DataFrame({"School Type":school_types, "Total Students":student_cnt_per_school,
                               "Total School Budget":per_school_budget, "Per Student Budget":per_student_budget,
                                "Average Math Score":avg_math_score, "Average Reading Score":avg_reading_score,
                                 "% Passing Math":percent_passing_math, "% Passing Reading":percent_passing_reading,
                                 "Overall Passing Rate":overall_passing_rate})            

# Data munging
school_smry = school_smry[["School Type","Total Students","Total School Budget","Per Student Budget",
                                "Average Math Score","Average Reading Score","% Passing Math","% Passing Reading",
                                 "Overall Passing Rate"]]

school_smry["Total School Budget"] = school_smry["Total School Budget"].map("${:,.2f}".format)
school_smry["Per Student Budget"] = school_smry["Per Student Budget"].map("${:,.2f}".format)

# Display the data frame
school_smry
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4976</td>
      <td>$3,124,928.00</td>
      <td>$628.00</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>87.439711</td>
      <td>100.0</td>
      <td>93.719855</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>$1,081,356.00</td>
      <td>$582.00</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411.00</td>
      <td>$639.00</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>86.436080</td>
      <td>100.0</td>
      <td>93.218040</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>$1,763,916.00</td>
      <td>$644.00</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>87.221614</td>
      <td>100.0</td>
      <td>93.610807</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>$917,500.00</td>
      <td>$625.00</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>$3,022,020.00</td>
      <td>$652.00</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>86.450917</td>
      <td>100.0</td>
      <td>93.225458</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087.00</td>
      <td>$581.00</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>$1,910,635.00</td>
      <td>$655.00</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>86.835790</td>
      <td>100.0</td>
      <td>93.417895</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>$3,094,650.00</td>
      <td>$650.00</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>86.704474</td>
      <td>100.0</td>
      <td>93.352237</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858.00</td>
      <td>$609.00</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>$2,547,363.00</td>
      <td>$637.00</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>86.446612</td>
      <td>100.0</td>
      <td>93.223306</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1761</td>
      <td>$1,056,600.00</td>
      <td>$600.00</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>$1,043,130.00</td>
      <td>$638.00</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>$1,319,574.00</td>
      <td>$578.00</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1800</td>
      <td>$1,049,400.00</td>
      <td>$583.00</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>



# Bottom Performing Schools (By Passing Rate)


```python
# Bottom 5 performing schools(By passing rates)
Bottom_Schools = school_smry.sort_values(["Overall Passing Rate"], ascending = True)
Bottom_Schools.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>$1,884,411.00</td>
      <td>$639.00</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>86.436080</td>
      <td>100.0</td>
      <td>93.218040</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>$2,547,363.00</td>
      <td>$637.00</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>86.446612</td>
      <td>100.0</td>
      <td>93.223306</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>$3,022,020.00</td>
      <td>$652.00</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>86.450917</td>
      <td>100.0</td>
      <td>93.225458</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>$3,094,650.00</td>
      <td>$650.00</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>86.704474</td>
      <td>100.0</td>
      <td>93.352237</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>$1,910,635.00</td>
      <td>$655.00</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>86.835790</td>
      <td>100.0</td>
      <td>93.417895</td>
    </tr>
  </tbody>
</table>
</div>



# Math Scores by Grade


```python
# Calculate math score by grade
nineth_grade_score_m =  smry_table[smry_table["Grade"] == "9th"].groupby("School").mean()["Math_Score"]
tenth_grade_score_m =  smry_table[smry_table["Grade"] == "10th"].groupby("School").mean()["Math_Score"]
eleventh_grade_score_m =  smry_table[smry_table["Grade"] == "11th"].groupby("School").mean()["Math_Score"]
twelveth_grade_score_m =  smry_table[smry_table["Grade"] == "12th"].groupby("School").mean()["Math_Score"]

# Math score by grade in table form
math_score_by_grade_df_draft = pd.DataFrame({"9th":nineth_grade_score_m, "10th":tenth_grade_score_m,
                               "11th":eleventh_grade_score_m,"12th":twelveth_grade_score_m})            

# Data munging
math_score_by_grade_df = math_score_by_grade_df_draft[["9th", "10th", "11th", "12th"]]

# Display the data frame
math_score_by_grade_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.438495</td>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.787402</td>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.027251</td>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.187857</td>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.625455</td>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.859966</td>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.420755</td>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.590022</td>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.085578</td>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.264706</td>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
    </tr>
  </tbody>
</table>
</div>



# Reading Scores By Grade


```python
# Calculate reading score by grade
nineth_grade_score_r =  smry_table[smry_table["Grade"] == "9th"].groupby("School").mean()["Reading_Score"]
tenth_grade_score_r =  smry_table[smry_table["Grade"] == "10th"].groupby("School").mean()["Reading_Score"]
eleventh_grade_score_r =  smry_table[smry_table["Grade"] == "11th"].groupby("School").mean()["Reading_Score"]
twelveth_grade_score_r =  smry_table[smry_table["Grade"] == "12th"].groupby("School").mean()["Reading_Score"]

# Math score by grade in table form
reading_score_by_grade_df_draft = pd.DataFrame({"9th":nineth_grade_score_r, "10th":tenth_grade_score_r,
                               "11th":eleventh_grade_score_r,"12th":twelveth_grade_score_r})            

# Data munging
reading_score_by_grade_df = reading_score_by_grade_df_draft[["9th", "10th", "11th", "12th"]]

# Display the data frame
reading_score_by_grade_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.866860</td>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.677165</td>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.290284</td>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.260714</td>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.807273</td>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.993127</td>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.122642</td>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.728850</td>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.939778</td>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.833333</td>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
    </tr>
  </tbody>
</table>
</div>



# Scores by School Spending


```python
# Create spending bins and ranges
spending_bins = [0, 585, 615, 645, 675]
spending_ranges = ["<585", "585-615", "615-645", "645-675"]

# Adding "Spending Ranges (Per Student)" to "school_smry" table
school_smry["Spending Ranges (Per Student)"] = pd.cut(per_student_budget, spending_bins, labels = spending_ranges)

# Calculation
spending_math_score = school_smry.groupby(["Spending Ranges (Per Student)"]).mean()['Average Math Score']
spending_reading_score = school_smry.groupby(["Spending Ranges (Per Student)"]).mean()['Average Reading Score']
spending_passing_math =  school_smry.groupby(["Spending Ranges (Per Student)"]).mean()['% Passing Math']
spending_passing_reading =  school_smry.groupby(["Spending Ranges (Per Student)"]).mean()['% Passing Reading']
overall_passing_rate =  (spending_math_score + spending_reading_score) / 2

# Create Scores by School Spending table
scores_by_school_spending = pd.DataFrame({"Average Math Score":spending_math_score, "Average Reading Score":spending_reading_score,
                               "% Passing Math":spending_passing_math,"% Passing Reading":spending_passing_reading,
                                    "Overall Passing Rate":overall_passing_rate}) 

# Data munging
scores_by_school_spending = scores_by_school_spending[["Average Math Score", "Average Reading Score",
                               "% Passing Math","% Passing Reading","Overall Passing Rate"]]

# Display the data frame
scores_by_school_spending
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges (Per Student)</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;585</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.694607</td>
    </tr>
    <tr>
      <th>585-615</th>
      <td>83.599686</td>
      <td>83.885211</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>83.742449</td>
    </tr>
    <tr>
      <th>615-645</th>
      <td>79.079225</td>
      <td>81.891436</td>
      <td>91.257336</td>
      <td>100.0</td>
      <td>80.485330</td>
    </tr>
    <tr>
      <th>645-675</th>
      <td>76.997210</td>
      <td>81.027843</td>
      <td>86.663727</td>
      <td>100.0</td>
      <td>79.012526</td>
    </tr>
  </tbody>
</table>
</div>



# Scores By School Size


```python
# Create spending bins and ranges
size_bins = [0, 1000, 2000, 5000]
size_ranges = ["Small (<1000)", "Medium (1000-2000)", "Large (2000-5000)"]

# Adding "Spending Ranges (Per Student)" to "school_smry" table
school_smry["School Size"] = pd.cut(school_smry["Total Students"], size_bins, labels = size_ranges)

# Calculation
avg_math_score  = school_smry.groupby(["School Size"]).mean()['Average Math Score']
avg_reading_score  = school_smry.groupby(["School Size"]).mean()['Average Reading Score']
percent_passing_math =  school_smry.groupby(["School Size"]).mean()['% Passing Math']
percent_passing_reading =  school_smry.groupby(["School Size"]).mean()['% Passing Reading']
overall_passing_rate =  school_smry.groupby(["School Size"]).mean()['Overall Passing Rate']

# Create Scores by School Spending table
scores_by_school_size = pd.DataFrame({"Average Math Score":avg_math_score, "Average Reading Score":avg_reading_score,
                               "% Passing Math":percent_passing_math,"% Passing Reading":percent_passing_reading,
                                    "Overall Passing Rate":overall_passing_rate}) 

# Data munging
scores_by_school_size = scores_by_school_size[["Average Math Score", "Average Reading Score",
                               "% Passing Math","% Passing Reading","Overall Passing Rate"]]

# Display the data frame
scores_by_school_size
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small (&lt;1000)</th>
      <td>83.821598</td>
      <td>83.929843</td>
      <td>100.0000</td>
      <td>100.0</td>
      <td>100.00000</td>
    </tr>
    <tr>
      <th>Medium (1000-2000)</th>
      <td>83.374684</td>
      <td>83.864438</td>
      <td>100.0000</td>
      <td>100.0</td>
      <td>100.00000</td>
    </tr>
    <tr>
      <th>Large (2000-5000)</th>
      <td>77.746417</td>
      <td>81.344493</td>
      <td>88.4419</td>
      <td>100.0</td>
      <td>94.22095</td>
    </tr>
  </tbody>
</table>
</div>



# Scores By School Type


```python
# Calculation
avg_math_score  = school_smry.groupby(["School Type"]).mean()['Average Math Score']
avg_reading_score  = school_smry.groupby(["School Type"]).mean()['Average Reading Score']
percent_passing_math =  school_smry.groupby(["School Type"]).mean()['% Passing Math']
percent_passing_reading =  school_smry.groupby(["School Type"]).mean()['% Passing Reading']
overall_passing_rate =  school_smry.groupby(["School Type"]).mean()['Overall Passing Rate']

# Create Scores by School Spending table
scores_by_school_type = pd.DataFrame({"Average Math Score":avg_math_score, "Average Reading Score":avg_reading_score,
                               "% Passing Math":percent_passing_math,"% Passing Reading":percent_passing_reading,
                                    "Overall Passing Rate":overall_passing_rate}) 

# Data munging
scores_by_school_type = scores_by_school_type[["Average Math Score", "Average Reading Score",
                               "% Passing Math","% Passing Reading","Overall Passing Rate"]]

# Display the data frame
scores_by_school_type
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>100.000000</td>
      <td>100.0</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>86.790742</td>
      <td>100.0</td>
      <td>93.395371</td>
    </tr>
  </tbody>
</table>
</div>


