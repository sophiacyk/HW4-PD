
***Observable trends*** 
1. Reading is easier than Math for all schools. The average score and the pass rate of Reading are both higher than Math.
2. The school performance is related to the school size, not related to the spending per student.  
3. Charter schools have better performance. The top 5 performance schools are all charter schools 


```python
# Import Dependencies
import pandas as pd
import numpy as np
```


```python
# Create dataframes from the raw data
schools_csv = "raw_data/schools_complete.csv"
students_csv = "raw_data/students_complete.csv"
schools_df = pd.read_csv(schools_csv)
students_df = pd.read_csv(students_csv)
```

**District Summary**


```python
# Get Math pass rate
math_pass=students_df.loc[students_df["math_score"]>=70]
math_pass_rate=math_pass["Student ID"].count()/students_df["Student ID"].count()

# Get Reading pass rate
reading_pass=students_df.loc[students_df["reading_score"]>=70]
reading_pass_rate=reading_pass["Student ID"].count()/students_df["Student ID"].count()

#Get budget per student
schools_df["budget per student"]=schools_df["budget"]/schools_df["size"]

snapshot={"Total Schools":[schools_df["School ID"].count()],"Total Students":students_df["Student ID"].count(),
          "Total Budget":schools_df["budget"].sum(),"Average Math Score":students_df["math_score"].mean(),
          "Average Reading Score":students_df["reading_score"].mean(),"% Passing Math":math_pass_rate,
          "% Passing Reading":reading_pass_rate,"Overall Passing Rate":(math_pass_rate+reading_pass_rate)*0.5}

snapshot_df=pd.DataFrame(snapshot)

snapshot_df.loc[:,"Total Budget"]=snapshot_df.loc[:,"Total Budget"].apply('${:.0f}'.format)
snapshot_df.loc[:,"Average Math Score"]=snapshot_df.loc[:,"Average Math Score"].apply('{:.02f}'.format)
snapshot_df.loc[:,"Average Reading Score"]=snapshot_df.loc[:,"Average Reading Score"].apply('{:.02f}'.format)


snapshot_df.loc[:,"% Passing Math"]=snapshot_df.loc[:,"% Passing Math"].apply('{:.02%}'.format)
snapshot_df.loc[:,"% Passing Reading"]=snapshot_df.loc[:,"% Passing Reading"].apply('{:.02%}'.format)
snapshot_df.loc[:,"Overall Passing Rate"]=snapshot_df.loc[:,"Overall Passing Rate"].apply('{:.02%}'.format)

#**District Summary**

snapshot_df=snapshot_df[["Total Schools", "Total Students","Total Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","Overall Passing Rate"]]
snapshot_df

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
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
      <td>$24649428</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>74.98%</td>
      <td>85.81%</td>
      <td>80.39%</td>
    </tr>
  </tbody>
</table>
</div>



**School Summary**

Step1: I combine all data into the DataFrame, school_summary; 
Step2: Get the top 5 performance school 



```python
# Get student counts
Grouped_schools=pd.groupby(students_df,"school")
student_number=Grouped_schools["Student ID"].count()

# Get Average Math Score and Average Reading Score
average_score=Grouped_schools.aggregate({"reading_score":'mean',"math_score":'mean'})
average_score.columns=["Average Reading Score","Average Math Score"]
average_score["name"]=average_score.index

average_score["Average Reading Score"]=average_score["Average Reading Score"].round(2)
average_score["Average Math Score"]=average_score["Average Math Score"].round(2)

# Get Pass Rate
# # math score pass rate
grouped_math_pass=pd.groupby(math_pass,"school")
# # reading score pass rate
grouped_reading_pass=pd.groupby(reading_pass,"school")
# overall score pass rate
grouped_math_pass_rate=grouped_math_pass["Student ID"].count()/student_number*100
grouped_reading_pass_rate=grouped_reading_pass["Student ID"].count()/student_number*100
overall_rate=0.5*(grouped_math_pass_rate+grouped_reading_pass_rate)
# combine three rates into a DataFrame
schools_perf=pd.concat([grouped_reading_pass_rate, grouped_math_pass_rate,overall_rate], axis=1)
schools_perf.columns=["Reading Pass Rate (%)","Math Pass Rate (%)","Overall Pass Rate (%)"]

```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:2: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:15: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      from ipykernel import kernelapp as app
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:17: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
    


```python
schools_perf["name"]=schools_perf.index
schools_perf.head().round(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Reading Pass Rate (%)</th>
      <th>Math Pass Rate (%)</th>
      <th>Overall Pass Rate (%)</th>
      <th>name</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.93</td>
      <td>66.68</td>
      <td>74.31</td>
      <td>Bailey High School</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>97.04</td>
      <td>94.13</td>
      <td>95.59</td>
      <td>Cabrera High School</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>80.74</td>
      <td>65.99</td>
      <td>73.36</td>
      <td>Figueroa High School</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>79.30</td>
      <td>68.31</td>
      <td>73.80</td>
      <td>Ford High School</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>97.14</td>
      <td>93.39</td>
      <td>95.27</td>
      <td>Griffin High School</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Combine the Performance DataFrame into the schoo_summary DataFrame
school_summary=pd.merge(schools_df,schools_perf,on="name")
school_summary=pd.merge(school_summary,average_score,on="name")

```

**Top Performing Schools (By Passing Rate)**


```python
#sort the school_summary to get the top 5
#format the DataFrame per requirement
top_schools = school_summary.sort_values("Overall Pass Rate (%)",ascending=False)
top_schools=top_schools[["name","type","size","budget","budget per student","Average Math Score","Average Reading Score","Math Pass Rate (%)","Reading Pass Rate (%)","Overall Pass Rate (%)"]]
top_schools.columns=["School Name","School Type","Total Students","Total School Budget","Per Student Budget","Average Math Score","Average Reading Score","Math Pass Rate","Reading Pass Rate","Overall Pass Rate"]

top_schools.head().round(2)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>Math Pass Rate</th>
      <th>Reading Pass Rate</th>
      <th>Overall Pass Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.06</td>
      <td>83.98</td>
      <td>94.13</td>
      <td>97.04</td>
      <td>95.59</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.42</td>
      <td>83.85</td>
      <td>93.27</td>
      <td>97.31</td>
      <td>95.29</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.84</td>
      <td>84.04</td>
      <td>94.59</td>
      <td>95.95</td>
      <td>95.27</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.35</td>
      <td>83.82</td>
      <td>93.39</td>
      <td>97.14</td>
      <td>95.27</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.27</td>
      <td>83.99</td>
      <td>93.87</td>
      <td>96.54</td>
      <td>95.20</td>
    </tr>
  </tbody>
</table>
</div>



**Math & Reading Scores by Grade**


```python
students_school=pd.groupby(students_df,["school","grade"])
students_school.aggregate({"reading_score":'mean',"math_score":'mean'}).round(2)

```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      """Entry point for launching an IPython kernel.
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>reading_score</th>
      <th>math_score</th>
    </tr>
    <tr>
      <th>school</th>
      <th>grade</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">Bailey High School</th>
      <th>10th</th>
      <td>80.91</td>
      <td>77.00</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.95</td>
      <td>77.52</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.91</td>
      <td>76.49</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.30</td>
      <td>77.08</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Cabrera High School</th>
      <th>10th</th>
      <td>84.25</td>
      <td>83.15</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.79</td>
      <td>82.77</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.29</td>
      <td>83.28</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.68</td>
      <td>83.09</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Figueroa High School</th>
      <th>10th</th>
      <td>81.41</td>
      <td>76.54</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.64</td>
      <td>76.88</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>81.38</td>
      <td>77.15</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.20</td>
      <td>76.40</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Ford High School</th>
      <th>10th</th>
      <td>81.26</td>
      <td>77.67</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.40</td>
      <td>76.92</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.66</td>
      <td>76.18</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.63</td>
      <td>77.36</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Griffin High School</th>
      <th>10th</th>
      <td>83.71</td>
      <td>84.23</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.29</td>
      <td>83.84</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.01</td>
      <td>83.36</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.37</td>
      <td>82.04</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Hernandez High School</th>
      <th>10th</th>
      <td>80.66</td>
      <td>77.34</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>81.40</td>
      <td>77.14</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.86</td>
      <td>77.19</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.87</td>
      <td>77.44</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Holden High School</th>
      <th>10th</th>
      <td>83.32</td>
      <td>83.43</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.82</td>
      <td>85.00</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.70</td>
      <td>82.86</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.68</td>
      <td>83.79</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Huang High School</th>
      <th>10th</th>
      <td>81.51</td>
      <td>75.91</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>81.42</td>
      <td>76.45</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.31</td>
      <td>77.23</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.29</td>
      <td>77.03</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Johnson High School</th>
      <th>10th</th>
      <td>80.77</td>
      <td>76.69</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.62</td>
      <td>77.49</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>81.23</td>
      <td>76.86</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>81.26</td>
      <td>77.19</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Pena High School</th>
      <th>10th</th>
      <td>83.61</td>
      <td>83.37</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.34</td>
      <td>84.33</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.59</td>
      <td>84.12</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.81</td>
      <td>83.63</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Rodriguez High School</th>
      <th>10th</th>
      <td>80.63</td>
      <td>76.61</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>80.86</td>
      <td>76.40</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>80.38</td>
      <td>77.69</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>80.99</td>
      <td>76.86</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Shelton High School</th>
      <th>10th</th>
      <td>83.44</td>
      <td>82.92</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.37</td>
      <td>83.38</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>82.78</td>
      <td>83.78</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>84.12</td>
      <td>83.42</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Thomas High School</th>
      <th>10th</th>
      <td>84.25</td>
      <td>83.09</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.59</td>
      <td>83.50</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>83.83</td>
      <td>83.50</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.73</td>
      <td>83.59</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wilson High School</th>
      <th>10th</th>
      <td>84.02</td>
      <td>83.72</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>83.76</td>
      <td>83.20</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.32</td>
      <td>83.04</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.94</td>
      <td>83.09</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">Wright High School</th>
      <th>10th</th>
      <td>83.81</td>
      <td>84.01</td>
    </tr>
    <tr>
      <th>11th</th>
      <td>84.16</td>
      <td>83.84</td>
    </tr>
    <tr>
      <th>12th</th>
      <td>84.07</td>
      <td>83.64</td>
    </tr>
    <tr>
      <th>9th</th>
      <td>83.83</td>
      <td>83.26</td>
    </tr>
  </tbody>
</table>
</div>



**Scores and Passing Rate by School Spending**


```python
#   * Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:
max_budget=max(school_summary["budget per student"])
min_budget=min(school_summary["budget per student"])
bin_gap=(max_budget-min_budget)/4

# Create the bins in which Data will be held
bins = [min_budget-10, min_budget+bin_gap, min_budget+bin_gap*2, min_budget+bin_gap*3, max_budget+10]

# Create the names for the four bins
group_names = ['Low', 'Okay', 'Moderate', 'High']
budget_cut=pd.Series(pd.cut(school_summary["budget per student"], bins, labels=group_names))
school_summary["spending range"]=budget_cut

#   * Average Math Score  -> problems using concat
#   * Average Reading Score
pd.groupby(school_summary,["spending range"]).aggregate({"Average Reading Score":'mean',"Average Math Score":'mean'}).round(2)
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:16: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      app.launch_new_instance()
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Reading Score</th>
      <th>Average Math Score</th>
    </tr>
    <tr>
      <th>spending range</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Low</th>
      <td>83.94</td>
      <td>83.45</td>
    </tr>
    <tr>
      <th>Okay</th>
      <td>83.88</td>
      <td>83.60</td>
    </tr>
    <tr>
      <th>Moderate</th>
      <td>82.42</td>
      <td>80.20</td>
    </tr>
    <tr>
      <th>High</th>
      <td>81.37</td>
      <td>77.87</td>
    </tr>
  </tbody>
</table>
</div>




```python
#   * % Passing Math
#   * % Passing Reading
#   * Overall Passing Rate (Average of the above two)

pd.groupby(school_summary,["spending range"]).aggregate({"Reading Pass Rate (%)":'mean',"Math Pass Rate (%)":'mean',"Overall Pass Rate (%)":'mean'}).round(2)

```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:5: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      """
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Reading Pass Rate (%)</th>
      <th>Math Pass Rate (%)</th>
      <th>Overall Pass Rate (%)</th>
    </tr>
    <tr>
      <th>spending range</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Low</th>
      <td>96.61</td>
      <td>93.46</td>
      <td>95.04</td>
    </tr>
    <tr>
      <th>Okay</th>
      <td>95.90</td>
      <td>94.23</td>
      <td>95.07</td>
    </tr>
    <tr>
      <th>Moderate</th>
      <td>89.54</td>
      <td>80.04</td>
      <td>84.79</td>
    </tr>
    <tr>
      <th>High</th>
      <td>83.00</td>
      <td>70.35</td>
      <td>76.67</td>
    </tr>
  </tbody>
</table>
</div>



**Scores by School Size**


```python
# * Repeat the above breakdown, but this time group schools based on a reasonable approximation of school size (Small, Medium, Large).
#   * Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:
max_size=max(school_summary["size"])
min_size=min(school_summary["size"])
bin_gap=int((max_size-min_size)/3)
bin_gap

# Create the bins in which Data will be held
bins = [min_size-10, min_size+bin_gap, min_size+bin_gap*2, max_size+10]

# Create the names for the four bins
group_names = ['Small', 'Medium', 'Large']
size_cut=pd.Series(pd.cut(school_summary["size"], bins, labels=group_names))
school_summary["size range"]=size_cut

```


```python
#   * Average Math Score  -> problems using concat
#   * Average Reading Score
pd.groupby(school_summary,["size range"]).aggregate({"Average Reading Score":'mean',"Average Math Score":'mean'})

#   * % Passing Math
#   * % Passing Reading
#   * Overall Passing Rate (Average of the above two)

size_passing_rate=pd.groupby(school_summary,["size range"]).aggregate({"Reading Pass Rate (%)":'mean',"Math Pass Rate (%)":'mean',"Overall Pass Rate (%)":'mean'})

size_passing_rate=size_passing_rate.round(2)
size_passing_rate.head()
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:3: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      This is separate from the ipykernel package so we can avoid doing imports until
    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:9: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      if __name__ == '__main__':
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Reading Pass Rate (%)</th>
      <th>Math Pass Rate (%)</th>
      <th>Overall Pass Rate (%)</th>
    </tr>
    <tr>
      <th>size range</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small</th>
      <td>96.59</td>
      <td>93.59</td>
      <td>95.09</td>
    </tr>
    <tr>
      <th>Medium</th>
      <td>84.47</td>
      <td>73.46</td>
      <td>78.97</td>
    </tr>
    <tr>
      <th>Large</th>
      <td>81.06</td>
      <td>66.46</td>
      <td>73.76</td>
    </tr>
  </tbody>
</table>
</div>




```python
# **Scores by School Type**
# * Repeat the above breakdown, but this time group schools based on school type (Charter vs. District).
score_size=pd.groupby(school_summary,["type"]).aggregate({"Reading Pass Rate (%)":'mean',"Math Pass Rate (%)":'mean',"Overall Pass Rate (%)":'mean'}).round(2)
score_size.head()
```

    C:\ProgramData\Anaconda3\lib\site-packages\ipykernel_launcher.py:3: FutureWarning: pd.groupby() is deprecated and will be removed; Please use the Series.groupby() or DataFrame.groupby() methods
      This is separate from the ipykernel package so we can avoid doing imports until
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Reading Pass Rate (%)</th>
      <th>Math Pass Rate (%)</th>
      <th>Overall Pass Rate (%)</th>
    </tr>
    <tr>
      <th>type</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>96.59</td>
      <td>93.62</td>
      <td>95.10</td>
    </tr>
    <tr>
      <th>District</th>
      <td>80.80</td>
      <td>66.55</td>
      <td>73.67</td>
    </tr>
  </tbody>
</table>
</div>




```python
!jupyter nbconvert --to markdown main.ipynb
```

    [NbConvertApp] Converting notebook main.ipynb to markdown
    [NbConvertApp] Writing 24796 bytes to main.md
    


```python
!rename "main.md" "README.md"
```

    A duplicate file name exists, or the file
    cannot be found.
    


```python
!jupyter nbconvert --to html main.ipynb
```

    [NbConvertApp] Converting notebook main.ipynb to html
    [NbConvertApp] Writing 307792 bytes to main.html
    


```python
!rename "main.html" "README.html"
```

    A duplicate file name exists, or the file
    cannot be found.
    
