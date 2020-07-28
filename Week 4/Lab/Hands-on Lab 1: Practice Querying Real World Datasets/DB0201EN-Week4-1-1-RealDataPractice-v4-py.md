<a href="https://cognitiveclass.ai"><img src = "https://ibm.box.com/shared/static/ugcqz6ohbvff804xp84y4kqnvvk3bq1g.png" width = 300, align = "center"></a>

<h1 align=center><font size = 5>Lab: Working with a real world data-set using SQL and Python</font></h1>

# Introduction

This notebook shows how to work with a real world dataset using SQL and Python. In this lab you will:
1. Understand the dataset for Chicago Public School level performance 
1. Store the dataset in an Db2 database on IBM Cloud instance
1. Retrieve metadata about tables and columns and query data from mixed case columns
1. Solve example problems to practice your SQL skills including using built-in database functions

## Chicago Public Schools - Progress Report Cards (2011-2012) 

The city of Chicago released a dataset showing all school level performance data used to create School Report Cards for the 2011-2012 school year. The dataset is available from the Chicago Data Portal: https://data.cityofchicago.org/Education/Chicago-Public-Schools-Progress-Report-Cards-2011-/9xs2-f89t

This dataset includes a large number of metrics. Start by familiarizing yourself with the types of metrics in the database: https://data.cityofchicago.org/api/assets/AAD41A13-BE8A-4E67-B1F5-86E711E09D5F?download=true

__NOTE__: Do not download the dataset directly from City of Chicago portal. Instead download a more database friendly version from the link below.
Now download a static copy of this database and review some of its contents:
https://ibm.box.com/shared/static/f9gjvj1gjmxxzycdhplzt01qtz0s7ew7.csv



### Store the dataset in a Table
In many cases the dataset to be analyzed is available as a .CSV (comma separated values) file, perhaps on the internet. To analyze the data using SQL, it first needs to be stored in the database.

While it is easier to read the dataset into a Pandas dataframe and then PERSIST it into the database as we saw in the previous lab, it results in mapping to default datatypes which may not be optimal for SQL querying. For example a long textual field may map to a CLOB instead of a VARCHAR. 

Therefore, __it is highly recommended to manually load the table using the database console LOAD tool, as indicated in Week 2 Lab 1 Part II__. The only difference with that lab is that in Step 5 of the instructions you will need to click on create "(+) New Table" and specify the name of the table you want to create and then click "Next". 

##### Now open the Db2 console, open the LOAD tool, Select / Drag the .CSV file for the CHICAGO PUBLIC SCHOOLS dataset and load the dataset into a new table called __SCHOOLS__.

<a href="https://cognitiveclass.ai"><img src = "https://ibm.box.com/shared/static/uc4xjh1uxcc78ks1i18v668simioz4es.jpg"></a>

### Connect to the database
Let us now load the ipython-sql  extension and establish a connection with the database


```python
%load_ext sql
```


```python
# Enter the connection string for your Db2 on Cloud database instance below
# %sql ibm_db_sa://my-username:my-password@my-hostname:my-port/my-db-name
%sql ibm_db_sa://my-username:my-password@my-hostname:my-port/my-db-name
```




    'Connected: nrx71347@BLUDB'



### Query the database system catalog to retrieve table metadata

##### You can verify that the table creation was successful by retrieving the list of all tables in your schema and checking whether the SCHOOLS table was created


```python
# type in your query to retrieve list of all tables in the database for your db2 schema (username)
%sql select TABSCHEMA, TABNAME, CREATE_TIME from SYSCAT.TABLES where LCASE(TABSCHEMA)='nrx71347'
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>tabschema</th>
        <th>tabname</th>
        <th>create_time</th>
    </tr>
    <tr>
        <td>NRX71347</td>
        <td>SCHOOLS</td>
        <td>2020-07-28 09:51:04.790689</td>
    </tr>
    <tr>
        <td>NRX71347</td>
        <td>INSTRUCTOR</td>
        <td>2020-07-27 21:16:56.747551</td>
    </tr>
    <tr>
        <td>NRX71347</td>
        <td>INTERNATIONAL_STUDENT_TEST_SCORES</td>
        <td>2020-07-27 21:28:06.591371</td>
    </tr>
    <tr>
        <td>NRX71347</td>
        <td>CHICAGO_SOCIOECONOMIC_DATA</td>
        <td>2020-07-27 21:55:45.986883</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
In Db2 the system catalog table called SYSCAT.TABLES contains the table metadata
-->

Double-click __here__ for the solution.

<!-- Solution:

%sql select TABSCHEMA, TABNAME, CREATE_TIME from SYSCAT.TABLES where TABSCHEMA='YOUR-DB2-USERNAME'

or, you can retrieve list of all tables where the schema name is not one of the system created ones:

%sql select TABSCHEMA, TABNAME, CREATE_TIME from SYSCAT.TABLES \
      where TABSCHEMA not in ('SYSIBM', 'SYSCAT', 'SYSSTAT', 'SYSIBMADM', 'SYSTOOLS', 'SYSPUBLIC')
      
or, just query for a specifc table that you want to verify exists in the database
%sql select * from SYSCAT.TABLES where TABNAME = 'SCHOOLS'

-->

### Query the database system catalog to retrieve column metadata

##### The SCHOOLS table contains a large number of columns. How many columns does this table have?


```python
# type in your query to retrieve the number of columns in the SCHOOLS table
%sql select count(*) from SYSCAT.COLUMNS where TABNAME = 'SCHOOLS'
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>1</th>
    </tr>
    <tr>
        <td>78</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
In Db2 the system catalog table called SYSCAT.COLUMNS contains the column metadata
-->

Double-click __here__ for the solution.

<!-- Solution:

%sql select count(*) from SYSCAT.COLUMNS where TABNAME = 'SCHOOLS'

-->

Now retrieve the the list of columns in SCHOOLS table and their column type (datatype) and length.


```python
# type in your query to retrieve all column names in the SCHOOLS table along with their datatypes and length
%sql select colname, typename, length from SYSCAT.COLUMNS where TABNAME = 'SCHOOLS'
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>colname</th>
        <th>typename</th>
        <th>length</th>
    </tr>
    <tr>
        <td>School_ID</td>
        <td>INTEGER</td>
        <td>4</td>
    </tr>
    <tr>
        <td>NAME_OF_SCHOOL</td>
        <td>VARCHAR</td>
        <td>65</td>
    </tr>
    <tr>
        <td>Elementary, Middle, or High School</td>
        <td>VARCHAR</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Street_Address</td>
        <td>VARCHAR</td>
        <td>30</td>
    </tr>
    <tr>
        <td>City</td>
        <td>VARCHAR</td>
        <td>7</td>
    </tr>
    <tr>
        <td>State</td>
        <td>VARCHAR</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ZIP_Code</td>
        <td>INTEGER</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Phone_Number</td>
        <td>VARCHAR</td>
        <td>14</td>
    </tr>
    <tr>
        <td>Link</td>
        <td>VARCHAR</td>
        <td>78</td>
    </tr>
    <tr>
        <td>Network_Manager</td>
        <td>VARCHAR</td>
        <td>40</td>
    </tr>
    <tr>
        <td>Collaborative_Name</td>
        <td>VARCHAR</td>
        <td>34</td>
    </tr>
    <tr>
        <td>Adequate_Yearly_Progress_Made_</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>Track_Schedule</td>
        <td>VARCHAR</td>
        <td>12</td>
    </tr>
    <tr>
        <td>CPS_Performance_Policy_Status</td>
        <td>VARCHAR</td>
        <td>16</td>
    </tr>
    <tr>
        <td>CPS_Performance_Policy_Level</td>
        <td>VARCHAR</td>
        <td>15</td>
    </tr>
    <tr>
        <td>HEALTHY_SCHOOL_CERTIFIED</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>Safety_Icon</td>
        <td>VARCHAR</td>
        <td>11</td>
    </tr>
    <tr>
        <td>SAFETY_SCORE</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Family_Involvement_Icon</td>
        <td>VARCHAR</td>
        <td>11</td>
    </tr>
    <tr>
        <td>Family_Involvement_Score</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>Environment_Icon</td>
        <td>VARCHAR</td>
        <td>11</td>
    </tr>
    <tr>
        <td>Environment_Score</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Instruction_Icon</td>
        <td>VARCHAR</td>
        <td>11</td>
    </tr>
    <tr>
        <td>Instruction_Score</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Leaders_Icon</td>
        <td>VARCHAR</td>
        <td>11</td>
    </tr>
    <tr>
        <td>Leaders_Score</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>Teachers_Icon</td>
        <td>VARCHAR</td>
        <td>11</td>
    </tr>
    <tr>
        <td>Teachers_Score</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>Parent_Engagement_Icon</td>
        <td>VARCHAR</td>
        <td>7</td>
    </tr>
    <tr>
        <td>Parent_Engagement_Score</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>Parent_Environment_Icon</td>
        <td>VARCHAR</td>
        <td>7</td>
    </tr>
    <tr>
        <td>Parent_Environment_Score</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>AVERAGE_STUDENT_ATTENDANCE</td>
        <td>VARCHAR</td>
        <td>6</td>
    </tr>
    <tr>
        <td>Rate_of_Misconducts__per_100_students_</td>
        <td>DECIMAL</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Average_Teacher_Attendance</td>
        <td>VARCHAR</td>
        <td>6</td>
    </tr>
    <tr>
        <td>Individualized_Education_Program_Compliance_Rate</td>
        <td>VARCHAR</td>
        <td>7</td>
    </tr>
    <tr>
        <td>Pk_2_Literacy__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Pk_2_Math__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr3_5_Grade_Level_Math__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr3_5_Grade_Level_Read__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr3_5_Keep_Pace_Read__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr3_5_Keep_Pace_Math__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr6_8_Grade_Level_Math__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr6_8_Grade_Level_Read__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr6_8_Keep_Pace_Math_</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr6_8_Keep_Pace_Read__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr_8_Explore_Math__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Gr_8_Explore_Read__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ISAT_Exceeding_Math__</td>
        <td>DECIMAL</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ISAT_Exceeding_Reading__</td>
        <td>DECIMAL</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ISAT_Value_Add_Math</td>
        <td>DECIMAL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ISAT_Value_Add_Read</td>
        <td>DECIMAL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ISAT_Value_Add_Color_Math</td>
        <td>VARCHAR</td>
        <td>6</td>
    </tr>
    <tr>
        <td>ISAT_Value_Add_Color_Read</td>
        <td>VARCHAR</td>
        <td>6</td>
    </tr>
    <tr>
        <td>Students_Taking__Algebra__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Students_Passing__Algebra__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>9th Grade EXPLORE (2009)</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>9th Grade EXPLORE (2010)</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>10th Grade PLAN (2009)</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>10th Grade PLAN (2010)</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Net_Change_EXPLORE_and_PLAN</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>11th Grade Average ACT (2011)</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Net_Change_PLAN_and_ACT</td>
        <td>VARCHAR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>College_Eligibility__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>Graduation_Rate__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>College_Enrollment_Rate__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>COLLEGE_ENROLLMENT</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>General_Services_Route</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Freshman_on_Track_Rate__</td>
        <td>VARCHAR</td>
        <td>4</td>
    </tr>
    <tr>
        <td>X_COORDINATE</td>
        <td>DECIMAL</td>
        <td>13</td>
    </tr>
    <tr>
        <td>Y_COORDINATE</td>
        <td>DECIMAL</td>
        <td>13</td>
    </tr>
    <tr>
        <td>Latitude</td>
        <td>DECIMAL</td>
        <td>18</td>
    </tr>
    <tr>
        <td>Longitude</td>
        <td>DECIMAL</td>
        <td>18</td>
    </tr>
    <tr>
        <td>COMMUNITY_AREA_NUMBER</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>COMMUNITY_AREA_NAME</td>
        <td>VARCHAR</td>
        <td>22</td>
    </tr>
    <tr>
        <td>Ward</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Police_District</td>
        <td>SMALLINT</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Location</td>
        <td>VARCHAR</td>
        <td>27</td>
    </tr>
</table>



Double-click __here__ for the solution.

<!-- Solution:

%sql select COLNAME, TYPENAME, LENGTH from SYSCAT.COLUMNS where TABNAME = 'SCHOOLS'

or

%sql select distinct(NAME), COLTYPE, LENGTH from SYSIBM.SYSCOLUMNS where TBNAME = 'SCHOOLS'

-->

### Questions
1. Is the column name for the "SCHOOL ID" attribute in upper or mixed case?
1. What is the name of "Community Area Name" column in your table? Does it have spaces?
1. Are there any columns in whose names the spaces and paranthesis (round brackets) have been replaced by the underscore character "_"?

## Problems

### Problem 1

##### How many Elementary Schools are in the dataset?


```python
%sql select count(*) from SCHOOLS where "Elementary, Middle, or High School" = 'ES'
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>1</th>
    </tr>
    <tr>
        <td>462</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
Which column specifies the school type e.g. 'ES', 'MS', 'HS'?
-->

Double-click __here__ for another hint

<!--
Does the column name have mixed case, spaces or other special characters?
If so, ensure you use double quotes around the "Name of the Column"
-->

Double-click __here__ for the solution.

<!-- Solution:

%sql select count(*) from SCHOOLS where "Elementary, Middle, or High School" = 'ES'

Correct answer: 462

-->

### Problem 2

##### What is the highest Safety Score?


```python
%sql select MAX(safety_score) as MAX_SAFETY_SCORE from SCHOOLS
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>max_safety_score</th>
    </tr>
    <tr>
        <td>99</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
Use the MAX() function
-->

Double-click __here__ for the solution.

<!-- Hint:

%sql select MAX(Safety_Score) AS MAX_SAFETY_SCORE from SCHOOLS

Correct answer: 99
-->


### Problem 3

##### Which schools have highest Safety Score?


```python
%sql select name_of_school from SCHOOLS WHERE safety_score = (select MAX(safety_score) from SCHOOLS)
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>name_of_school</th>
    </tr>
    <tr>
        <td>Abraham Lincoln Elementary School</td>
    </tr>
    <tr>
        <td>Alexander Graham Bell Elementary School</td>
    </tr>
    <tr>
        <td>Annie Keller Elementary Gifted Magnet School</td>
    </tr>
    <tr>
        <td>Augustus H Burley Elementary School</td>
    </tr>
    <tr>
        <td>Edgar Allan Poe Elementary Classical School</td>
    </tr>
    <tr>
        <td>Edgebrook Elementary School</td>
    </tr>
    <tr>
        <td>Ellen Mitchell Elementary School</td>
    </tr>
    <tr>
        <td>James E McDade Elementary Classical School</td>
    </tr>
    <tr>
        <td>James G Blaine Elementary School</td>
    </tr>
    <tr>
        <td>LaSalle Elementary Language Academy</td>
    </tr>
    <tr>
        <td>Mary E Courtenay Elementary Language Arts Center</td>
    </tr>
    <tr>
        <td>Northside College Preparatory High School</td>
    </tr>
    <tr>
        <td>Northside Learning Center High School</td>
    </tr>
    <tr>
        <td>Norwood Park Elementary School</td>
    </tr>
    <tr>
        <td>Oriole Park Elementary School</td>
    </tr>
    <tr>
        <td>Sauganash Elementary School</td>
    </tr>
    <tr>
        <td>Stephen Decatur Classical Elementary School</td>
    </tr>
    <tr>
        <td>Talman Elementary School</td>
    </tr>
    <tr>
        <td>Wildwood Elementary School</td>
    </tr>
</table>



Double-click __here__ for the solution.

<!-- Solution:
In the previous problem we found out that the highest Safety Score is 99, so we can use that as an input in the where clause:

%sql select Name_of_School, Safety_Score from SCHOOLS where Safety_Score = 99

or, a better way:

%sql select Name_of_School, Safety_Score from SCHOOLS where \
  Safety_Score= (select MAX(Safety_Score) from SCHOOLS)


Correct answer: several schools with with Safety Score of 99.
-->


### Problem 4

##### What are the top 10 schools with the highest "Average Student Attendance"?



```python
%sql select name_of_school, average_student_attendance from SCHOOLS order by average_student_attendance desc nulls last limit 10
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>name_of_school</th>
        <th>average_student_attendance</th>
    </tr>
    <tr>
        <td>John Charles Haines Elementary School</td>
        <td>98.40%</td>
    </tr>
    <tr>
        <td>James Ward Elementary School</td>
        <td>97.80%</td>
    </tr>
    <tr>
        <td>Edgar Allan Poe Elementary Classical School</td>
        <td>97.60%</td>
    </tr>
    <tr>
        <td>Orozco Fine Arts &amp; Sciences Elementary School</td>
        <td>97.60%</td>
    </tr>
    <tr>
        <td>Rachel Carson Elementary School</td>
        <td>97.60%</td>
    </tr>
    <tr>
        <td>Annie Keller Elementary Gifted Magnet School</td>
        <td>97.50%</td>
    </tr>
    <tr>
        <td>Andrew Jackson Elementary Language Academy</td>
        <td>97.40%</td>
    </tr>
    <tr>
        <td>Lenart Elementary Regional Gifted Center</td>
        <td>97.40%</td>
    </tr>
    <tr>
        <td>Disney II Magnet School</td>
        <td>97.30%</td>
    </tr>
    <tr>
        <td>John H Vanderpoel Elementary Magnet School</td>
        <td>97.20%</td>
    </tr>
</table>



Double-click __here__ for the solution.

<!-- Solution:

%sql select Name_of_School, Average_Student_Attendance from SCHOOLS \
    order by Average_Student_Attendance desc nulls last limit 10 

-->

### Problem 5

##### Retrieve the list of 5 Schools with the lowest Average Student Attendance sorted in ascending order based on attendance


```python
%sql select name_of_school, average_student_attendance from SCHOOLS order by average_student_attendance fetch first 5 rows only
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>name_of_school</th>
        <th>average_student_attendance</th>
    </tr>
    <tr>
        <td>Richard T Crane Technical Preparatory High School</td>
        <td>57.90%</td>
    </tr>
    <tr>
        <td>Barbara Vick Early Childhood &amp; Family Center</td>
        <td>60.90%</td>
    </tr>
    <tr>
        <td>Dyett High School</td>
        <td>62.50%</td>
    </tr>
    <tr>
        <td>Wendell Phillips Academy High School</td>
        <td>63.00%</td>
    </tr>
    <tr>
        <td>Orr Academy High School</td>
        <td>66.30%</td>
    </tr>
</table>



Double-click __here__ for the solution.

<!-- Solution:

%sql SELECT Name_of_School, Average_Student_Attendance  \
     from SCHOOLS \
     order by Average_Student_Attendance \
     fetch first 5 rows only

-->


### Problem 6

##### Now remove the '%' sign from the above result set for Average Student Attendance column


```python
%sql select name_of_school, REPLACE(average_student_attendance, '%', '') as average_student_attendance from SCHOOLS order by average_student_attendance fetch first 5 rows only
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>name_of_school</th>
        <th>average_student_attendance</th>
    </tr>
    <tr>
        <td>Richard T Crane Technical Preparatory High School</td>
        <td>57.90</td>
    </tr>
    <tr>
        <td>Barbara Vick Early Childhood &amp; Family Center</td>
        <td>60.90</td>
    </tr>
    <tr>
        <td>Dyett High School</td>
        <td>62.50</td>
    </tr>
    <tr>
        <td>Wendell Phillips Academy High School</td>
        <td>63.00</td>
    </tr>
    <tr>
        <td>Orr Academy High School</td>
        <td>66.30</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
Use the REPLACE() function to replace '%' with ''
See documentation for this function at:
https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.sql.ref.doc/doc/r0000843.html
-->

Double-click __here__ for the solution.

<!-- Hint:

%sql SELECT Name_of_School, REPLACE(Average_Student_Attendance, '%', '') \
     from SCHOOLS \
     order by Average_Student_Attendance \
     fetch first 5 rows only

-->


### Problem 7

##### Which Schools have Average Student Attendance lower than 70%?


```python
%sql select name_of_school, average_student_attendance from SCHOOLS where CAST(REPLACE(average_student_attendance, '%', '') as DOUBLE) <  70
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>name_of_school</th>
        <th>average_student_attendance</th>
    </tr>
    <tr>
        <td>Barbara Vick Early Childhood &amp; Family Center</td>
        <td>60.90%</td>
    </tr>
    <tr>
        <td>Chicago Vocational Career Academy High School</td>
        <td>68.80%</td>
    </tr>
    <tr>
        <td>Dyett High School</td>
        <td>62.50%</td>
    </tr>
    <tr>
        <td>Manley Career Academy High School</td>
        <td>66.80%</td>
    </tr>
    <tr>
        <td>Orr Academy High School</td>
        <td>66.30%</td>
    </tr>
    <tr>
        <td>Richard T Crane Technical Preparatory High School</td>
        <td>57.90%</td>
    </tr>
    <tr>
        <td>Roberto Clemente Community Academy High School</td>
        <td>69.60%</td>
    </tr>
    <tr>
        <td>Wendell Phillips Academy High School</td>
        <td>63.00%</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
The datatype of the "Average_Student_Attendance" column is varchar.
So you cannot use it as is in the where clause for a numeric comparison.
First use the CAST() function to cast it as a DECIMAL or DOUBLE
e.g. CAST("Column_Name" as DOUBLE)
or simply: DECIMAL("Column_Name")
-->

Double-click __here__ for another hint

<!--
Don't forget the '%' age sign needs to be removed before casting
-->

Double-click __here__ for the solution.

<!-- Solution:

%sql SELECT Name_of_School, Average_Student_Attendance  \
     from SCHOOLS \
     where CAST ( REPLACE(Average_Student_Attendance, '%', '') AS DOUBLE ) < 70 \
     order by Average_Student_Attendance
     
or,

%sql SELECT Name_of_School, Average_Student_Attendance  \
     from SCHOOLS \
     where DECIMAL ( REPLACE(Average_Student_Attendance, '%', '') ) < 70 \
     order by Average_Student_Attendance

-->


### Problem 8

##### Get the total College Enrollment for each Community Area


```python
%sql select community_area_name, SUM(College_Enrollment) as total_enrollments from SCHOOLS group by community_area_name
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>community_area_name</th>
        <th>total_enrollments</th>
    </tr>
    <tr>
        <td>ALBANY PARK</td>
        <td>6864</td>
    </tr>
    <tr>
        <td>ARCHER HEIGHTS</td>
        <td>4823</td>
    </tr>
    <tr>
        <td>ARMOUR SQUARE</td>
        <td>1458</td>
    </tr>
    <tr>
        <td>ASHBURN</td>
        <td>6483</td>
    </tr>
    <tr>
        <td>AUBURN GRESHAM</td>
        <td>4175</td>
    </tr>
    <tr>
        <td>AUSTIN</td>
        <td>10933</td>
    </tr>
    <tr>
        <td>AVALON PARK</td>
        <td>1522</td>
    </tr>
    <tr>
        <td>AVONDALE</td>
        <td>3640</td>
    </tr>
    <tr>
        <td>BELMONT CRAGIN</td>
        <td>14386</td>
    </tr>
    <tr>
        <td>BEVERLY</td>
        <td>1636</td>
    </tr>
    <tr>
        <td>BRIDGEPORT</td>
        <td>3167</td>
    </tr>
    <tr>
        <td>BRIGHTON PARK</td>
        <td>9647</td>
    </tr>
    <tr>
        <td>BURNSIDE</td>
        <td>549</td>
    </tr>
    <tr>
        <td>CALUMET HEIGHTS</td>
        <td>1568</td>
    </tr>
    <tr>
        <td>CHATHAM</td>
        <td>5042</td>
    </tr>
    <tr>
        <td>CHICAGO LAWN</td>
        <td>7086</td>
    </tr>
    <tr>
        <td>CLEARING</td>
        <td>2085</td>
    </tr>
    <tr>
        <td>DOUGLAS</td>
        <td>4670</td>
    </tr>
    <tr>
        <td>DUNNING</td>
        <td>4568</td>
    </tr>
    <tr>
        <td>EAST GARFIELD PARK</td>
        <td>5337</td>
    </tr>
    <tr>
        <td>EAST SIDE</td>
        <td>5305</td>
    </tr>
    <tr>
        <td>EDGEWATER</td>
        <td>4600</td>
    </tr>
    <tr>
        <td>EDISON PARK</td>
        <td>910</td>
    </tr>
    <tr>
        <td>ENGLEWOOD</td>
        <td>6832</td>
    </tr>
    <tr>
        <td>FOREST GLEN</td>
        <td>1431</td>
    </tr>
    <tr>
        <td>FULLER PARK</td>
        <td>531</td>
    </tr>
    <tr>
        <td>GAGE PARK</td>
        <td>9915</td>
    </tr>
    <tr>
        <td>GARFIELD RIDGE</td>
        <td>4552</td>
    </tr>
    <tr>
        <td>GRAND BOULEVARD</td>
        <td>2809</td>
    </tr>
    <tr>
        <td>GREATER GRAND CROSSING</td>
        <td>4051</td>
    </tr>
    <tr>
        <td>HEGEWISCH</td>
        <td>963</td>
    </tr>
    <tr>
        <td>HERMOSA</td>
        <td>3975</td>
    </tr>
    <tr>
        <td>HUMBOLDT PARK</td>
        <td>8620</td>
    </tr>
    <tr>
        <td>HYDE PARK</td>
        <td>1930</td>
    </tr>
    <tr>
        <td>IRVING PARK</td>
        <td>7764</td>
    </tr>
    <tr>
        <td>JEFFERSON PARK</td>
        <td>1755</td>
    </tr>
    <tr>
        <td>KENWOOD</td>
        <td>4287</td>
    </tr>
    <tr>
        <td>LAKE VIEW</td>
        <td>7055</td>
    </tr>
    <tr>
        <td>LINCOLN PARK</td>
        <td>5615</td>
    </tr>
    <tr>
        <td>LINCOLN SQUARE</td>
        <td>4132</td>
    </tr>
    <tr>
        <td>LOGAN SQUARE</td>
        <td>7351</td>
    </tr>
    <tr>
        <td>LOOP</td>
        <td>871</td>
    </tr>
    <tr>
        <td>LOWER WEST SIDE</td>
        <td>7257</td>
    </tr>
    <tr>
        <td>MCKINLEY PARK</td>
        <td>1552</td>
    </tr>
    <tr>
        <td>MONTCLARE</td>
        <td>1317</td>
    </tr>
    <tr>
        <td>MORGAN PARK</td>
        <td>3271</td>
    </tr>
    <tr>
        <td>MOUNT GREENWOOD</td>
        <td>2091</td>
    </tr>
    <tr>
        <td>NEAR NORTH SIDE</td>
        <td>3362</td>
    </tr>
    <tr>
        <td>NEAR SOUTH SIDE</td>
        <td>1378</td>
    </tr>
    <tr>
        <td>NEAR WEST SIDE</td>
        <td>7975</td>
    </tr>
    <tr>
        <td>NEW CITY</td>
        <td>7922</td>
    </tr>
    <tr>
        <td>NORTH CENTER</td>
        <td>7541</td>
    </tr>
    <tr>
        <td>NORTH LAWNDALE</td>
        <td>5146</td>
    </tr>
    <tr>
        <td>NORTH PARK</td>
        <td>4210</td>
    </tr>
    <tr>
        <td>NORWOOD PARK</td>
        <td>6469</td>
    </tr>
    <tr>
        <td>OAKLAND</td>
        <td>140</td>
    </tr>
    <tr>
        <td>OHARE</td>
        <td>786</td>
    </tr>
    <tr>
        <td>PORTAGE PARK</td>
        <td>6954</td>
    </tr>
    <tr>
        <td>PULLMAN</td>
        <td>1620</td>
    </tr>
    <tr>
        <td>RIVERDALE</td>
        <td>1547</td>
    </tr>
    <tr>
        <td>ROGERS PARK</td>
        <td>4068</td>
    </tr>
    <tr>
        <td>ROSELAND</td>
        <td>7020</td>
    </tr>
    <tr>
        <td>SOUTH CHICAGO</td>
        <td>4043</td>
    </tr>
    <tr>
        <td>SOUTH DEERING</td>
        <td>1859</td>
    </tr>
    <tr>
        <td>SOUTH LAWNDALE</td>
        <td>14793</td>
    </tr>
    <tr>
        <td>SOUTH SHORE</td>
        <td>4543</td>
    </tr>
    <tr>
        <td>UPTOWN</td>
        <td>4388</td>
    </tr>
    <tr>
        <td>WASHINGTON HEIGHTS</td>
        <td>4006</td>
    </tr>
    <tr>
        <td>WASHINGTON PARK</td>
        <td>2648</td>
    </tr>
    <tr>
        <td>WEST ELSDON</td>
        <td>3700</td>
    </tr>
    <tr>
        <td>WEST ENGLEWOOD</td>
        <td>5946</td>
    </tr>
    <tr>
        <td>WEST GARFIELD PARK</td>
        <td>2622</td>
    </tr>
    <tr>
        <td>WEST LAWN</td>
        <td>4207</td>
    </tr>
    <tr>
        <td>WEST PULLMAN</td>
        <td>3240</td>
    </tr>
    <tr>
        <td>WEST RIDGE</td>
        <td>8197</td>
    </tr>
    <tr>
        <td>WEST TOWN</td>
        <td>9429</td>
    </tr>
    <tr>
        <td>WOODLAWN</td>
        <td>4206</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
Verify the exact name of the Enrollment column in the database
Use the SUM() function to add up the Enrollments for each Community Area
-->

Double-click __here__ for another hint

<!--
Don't forget to group by the Community Area
-->

Double-click __here__ for the solution.

<!-- Solution:

%sql select Community_Area_Name, sum(College_Enrollment) AS TOTAL_ENROLLMENT \
   from SCHOOLS \
   group by Community_Area_Name 

-->


### Problem 9

##### Get the 5 Community Areas with the least total College Enrollment  sorted in ascending order 


```python
%sql select community_area_name, SUM(College_Enrollment) as total_enrollments from SCHOOLS group by community_area_name order by total_enrollments asc \
fetch first 5 rows only
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>community_area_name</th>
        <th>total_enrollments</th>
    </tr>
    <tr>
        <td>OAKLAND</td>
        <td>140</td>
    </tr>
    <tr>
        <td>FULLER PARK</td>
        <td>531</td>
    </tr>
    <tr>
        <td>BURNSIDE</td>
        <td>549</td>
    </tr>
    <tr>
        <td>OHARE</td>
        <td>786</td>
    </tr>
    <tr>
        <td>LOOP</td>
        <td>871</td>
    </tr>
</table>



Double-click __here__ for a hint

<!--
Order the previous query and limit the number of rows you fetch
-->

Double-click __here__ for the solution.

<!-- Solution:

%sql select Community_Area_Name, sum(College_Enrollment) AS TOTAL_ENROLLMENT \
   from SCHOOLS \
   group by Community_Area_Name \
   order by TOTAL_ENROLLMENT asc \
   fetch first 5 rows only

-->

### Problem 10

##### Get the hardship index for the community area which has College Enrollment of 4638


```python
%%sql
select hardship_index 
   from chicago_socioeconomic_data CD, schools CPS 
   where CD.ca = CPS.community_area_number 
      and college_enrollment = 4368
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>hardship_index</th>
    </tr>
    <tr>
        <td>6.0</td>
    </tr>
</table>



Double-click __here__ for the solution.

<!-- Solution:
NOTE: For this solution to work the CHICAGO_SOCIOECONOMIC_DATA table 
      as created in the last lab of Week 3 should already exist

%%sql 
select hardship_index 
   from chicago_socioeconomic_data CD, schools CPS 
   where CD.ca = CPS.community_area_number 
      and college_enrollment = 4368

-->

### Problem 11

##### Get the hardship index for the community area which has the highest value for College Enrollment


```python
%sql select ca, community_area_name, hardship_index from chicago_socioeconomic_data \
   where ca in \
   ( select community_area_number from schools order by college_enrollment desc limit 1 )
```

     * ibm_db_sa://nrx71347:***@dashdb-txn-sbox-yp-lon02-07.services.eu-gb.bluemix.net:50000/BLUDB
    Done.





<table>
    <tr>
        <th>ca</th>
        <th>community_area_name</th>
        <th>hardship_index</th>
    </tr>
    <tr>
        <td>5.0</td>
        <td>North Center</td>
        <td>6.0</td>
    </tr>
</table>



Double-click __here__ for the solution.

<!-- Solution:
NOTE: For this solution to work the CHICAGO_SOCIOECONOMIC_DATA table 
      as created in the last lab of Week 3 should already exist

%sql select ca, community_area_name, hardship_index from chicago_socioeconomic_data \
   where ca in \
   ( select community_area_number from schools order by college_enrollment desc limit 1 )

-->

## Summary

##### In this lab you learned how to work with a real word dataset using SQL and Python. You learned how to query columns with spaces or special characters in their names and with mixed case names. You also used built in database functions and practiced how to sort, limit, and order result sets, as well as used sub-queries and worked with multiple tables.

Copyright &copy; 2018 [cognitiveclass.ai](cognitiveclass.ai?utm_source=bducopyrightlink&utm_medium=dswb&utm_campaign=bdu). This notebook and its source code are released under the terms of the [MIT License](https://bigdatauniversity.com/mit-license/).

