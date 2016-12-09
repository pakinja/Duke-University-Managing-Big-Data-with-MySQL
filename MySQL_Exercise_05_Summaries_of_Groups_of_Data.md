
Copyright Jana Schaich Borg/Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)

# MySQL Exercise 5: Summaries of Groups of Data

So far you've learned how to select, reformat, manipulate, order, and summarize data from a single table in database.  In this lesson, you are going to learn how to summarize multiple subsets of your data in the same query.  The method for doing this is to include a "GROUP BY" clause in your SQL queries.  
  
    

## The GROUP BY clause

The GROUP BY clause comes after the WHERE clause, but before ORDER BY or LIMIT:

<img src="https://duke.box.com/shared/static/lc0yjfz1339q3mc04ne13m2q5tkzy3x7.jpg" width=400 alt="SELECT FROM WHERE ORDER BY" />

The GROUP BY clause is easy to incorporate into your queries.  In fact, it might be a little too easy to incorporate into MySQL queries, because it can be used incorrectly in MySQL queries even when no error message is displayed.  As a consequence, I suggest you adopt a healthy dose of caution every time you use the GROUP BY clause.  By the end of this lesson, you will understand why.  When used correctly, though, GROUP BY is one of the most useful and efficient parts of an SQL query, and once you are comfortable using it, you will use it very frequently.

**To get started, load the SQL library and the Dognition database, and set the dognition database as the default:**



```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

    0 rows affected.





    []



Let's return to a question from MySQL Exercise 4.  How would you query the average rating for each of the 40 tests in the Reviews table? As we discussed, one very inefficient method to do that would be to write 40 separate queries with each one having a different test name in the WHERE conditional clause.  Then you could copy or transcribe the results from all 40 queries into one place.  But that wouldn't be very pleasant.  Here's how you could do the same thing using one query that has a GROUP BY clause:

```mySQL
SELECT test_name, AVG(rating) AS AVG_Rating
FROM reviews
GROUP BY test_name
```

This query will output the average rating for each test.  More technically, this query will instruct MySQL to average all the rows that have the same value in the test_name column.

Notice that I included test_name in the SELECT statement.  As a strong rule of thumb, if you are grouping by a column, you should also include that column in the SELECT statement.  If you don't do this, you won't know to which group each row of your output corresponds.  
     
**To see what I mean, try the query above without test_name included in the SELECT statement:**


```python
%%sql
SELECT test_name, AVG(rating) AS AVG_Rating
FROM reviews
GROUP BY test_name
```

    ERROR: Cell magic `%%sql` not found.


You can form groups using derived values as well as original columns.  To illustrate this, let's address another question: how many tests were completed during each month of the year?

To answer this question, we need to take advantage of another datetime function described in the website below:

http://www.w3resource.com/mysql/date-and-time-functions/date-and-time-functions.php

MONTH() will return a number representing the month of a date entry. To get the total number of tests completed each month, you could put the MONTH function into the GROUP BY clause, in this case through an alias:

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY Month;
```

You can also group by multiple columns or derived fields.  If we wanted to determine the total number of each type of test completed each month, you could include both "test_name" and the derived "Month" field in the GROUP BY clause, separated by a comma.

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name, Month;
```

MySQL allows you to use aliases in a GROUP BY clause, but some database systems do not.  If you are using a database system that does NOT accept aliases in GROUP BY clauses, you can still group by derived fields, but you have to duplicate the calculation for the derived field in the GROUP BY clause in addition to including the derived field in the SELECT clause:

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name, MONTH(created_at);
```  
  
Try the query once with test_name first in the GROUP BY list, and once with Month first in the GROUP BY list below.  Inspect the outputs:


```python
%%sql
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name, MONTH(created_at)
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>test_name</th>
        <th>Month</th>
        <th>Num_Completed_Tests</th>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>1</td>
        <td>25</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>2</td>
        <td>28</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>3</td>
        <td>22</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>4</td>
        <td>12</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>5</td>
        <td>13</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>6</td>
        <td>18</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>7</td>
        <td>36</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>8</td>
        <td>17</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>9</td>
        <td>28</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>10</td>
        <td>27</td>
    </tr>
</table>




```python
%%sql
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY MONTH(created_at),  test_name
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>test_name</th>
        <th>Month</th>
        <th>Num_Completed_Tests</th>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>1</td>
        <td>25</td>
    </tr>
    <tr>
        <td>3 vs 1 Game</td>
        <td>1</td>
        <td>35</td>
    </tr>
    <tr>
        <td>5 vs 1 Game</td>
        <td>1</td>
        <td>59</td>
    </tr>
    <tr>
        <td>Arm Pointing</td>
        <td>1</td>
        <td>622</td>
    </tr>
    <tr>
        <td>Cover Your Eyes</td>
        <td>1</td>
        <td>452</td>
    </tr>
    <tr>
        <td>Delayed Cup Game</td>
        <td>1</td>
        <td>356</td>
    </tr>
    <tr>
        <td>Different Perspective</td>
        <td>1</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Expression Game</td>
        <td>1</td>
        <td>6</td>
    </tr>
    <tr>
        <td>Eye Contact Game</td>
        <td>1</td>
        <td>624</td>
    </tr>
    <tr>
        <td>Eye Contact Warm-up</td>
        <td>1</td>
        <td>707</td>
    </tr>
</table>



Notice that in the first case, the first block of rows share the same test_name, but are broken up into separate months (for those of you who took the "Data Visualization and Communication with Tableau" course of this specialization, this is similar to what would happen if you put test_name first and created_at second on the rows or columns shelf in Tableau).  

In the second case, the first block of rows share the same month, but are broken up into separate tests  (this is similar to what would happen if you put created_at first and test_name second on the rows or columns shelf in Tableau).  If you were to visualize these outputs, they would look like the charts below.

<img src="https://duke.box.com/shared/static/y6l3ldxg289brid5mmn886qweglt65qv.jpg" width=800 alt="ORDER" />

Different database servers might default to ordering the outputs in a certain way, but you shouldn't rely on that being the case.  To ensure the output is ordered in a way you intend, add an ORDER BY clause to your grouped query using the syntax you already know and have practiced:

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY test_name, Month
ORDER BY test_name ASC, Month ASC;
```

**Question 1: Output a table that calculates the number of distinct female and male dogs in each breed group of the Dogs table, sorted by the total number of dogs in descending order (the sex/breed_group pair with the greatest number of dogs should have 8466 unique Dog_Guids):**


```python
%%sql
SELECT gender,breed_group, COUNT(DISTINCT dog_guid) AS Num_dogs
FROM dogs
GROUP BY gender, breed_group
ORDER BY Num_dogs DESC;
```

    18 rows affected.





<table>
    <tr>
        <th>gender</th>
        <th>breed_group</th>
        <th>Num_dogs</th>
    </tr>
    <tr>
        <td>male</td>
        <td>None</td>
        <td>8466</td>
    </tr>
    <tr>
        <td>female</td>
        <td>None</td>
        <td>8367</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Sporting</td>
        <td>2584</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Sporting</td>
        <td>2262</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Herding</td>
        <td>1736</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Herding</td>
        <td>1704</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Toy</td>
        <td>1473</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Toy</td>
        <td>1145</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Non-Sporting</td>
        <td>1098</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Working</td>
        <td>1075</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Non-Sporting</td>
        <td>919</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Terrier</td>
        <td>919</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Working</td>
        <td>895</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Terrier</td>
        <td>794</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Hound</td>
        <td>725</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Hound</td>
        <td>614</td>
    </tr>
    <tr>
        <td>male</td>
        <td></td>
        <td>147</td>
    </tr>
    <tr>
        <td>female</td>
        <td></td>
        <td>127</td>
    </tr>
</table>



Some database servers, including MySQL, allow you to use numbers in place of field names in the GROUP BY or ORDER BY fields to reduce the overall length of the queries.  I tend to avoid this abbreviated method of writing queries because I find it challenging to troubleshoot when you are writing complicated queries with many fields, but it does allow you to write queries faster.  To use this method, assign each field in your SELECT statement a number acording to the order the field appears in the SELECT statement.  In the following statement:

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
```

test_name would be #1, Month would be #2, and Num_Completed_Tests would be #3.  You could then rewrite the query above to read:

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
GROUP BY 1, 2
ORDER BY 1 ASC, 2 ASC;
```

**Question 2: Revise the query your wrote in Question 1 so that it uses only numbers in the GROUP BY and ORDER BY fields.**


```python
%%sql
SELECT gender,breed_group, COUNT(DISTINCT dog_guid) AS Num_dogs
FROM dogs
GROUP BY 1, 2
ORDER BY 3 DESC;
```

    18 rows affected.





<table>
    <tr>
        <th>gender</th>
        <th>breed_group</th>
        <th>Num_dogs</th>
    </tr>
    <tr>
        <td>male</td>
        <td>None</td>
        <td>8466</td>
    </tr>
    <tr>
        <td>female</td>
        <td>None</td>
        <td>8367</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Sporting</td>
        <td>2584</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Sporting</td>
        <td>2262</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Herding</td>
        <td>1736</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Herding</td>
        <td>1704</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Toy</td>
        <td>1473</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Toy</td>
        <td>1145</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Non-Sporting</td>
        <td>1098</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Working</td>
        <td>1075</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Terrier</td>
        <td>919</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Non-Sporting</td>
        <td>919</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Working</td>
        <td>895</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Terrier</td>
        <td>794</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Hound</td>
        <td>725</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Hound</td>
        <td>614</td>
    </tr>
    <tr>
        <td>male</td>
        <td></td>
        <td>147</td>
    </tr>
    <tr>
        <td>female</td>
        <td></td>
        <td>127</td>
    </tr>
</table>



## The HAVING clause

Just like you can query subsets of rows using the WHERE clause, you can query subsets of aggregated groups using the HAVING clause.  However, wheras the expression that follows a WHERE clause has to be applicable to each row of data in a column, the expression that follows a HAVING clause has to be applicable or computable using a group of data.  

If you wanted to examine the number of tests completed only during the winter holiday months of November and December, you would need to use a WHERE clause, because the month a test was completed in is recorded in each row.  Your query might look like this:

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
WHERE MONTH(created_at)=11 OR MONTH(created_at)=12
GROUP BY 1, 2
ORDER BY 3 DESC;
```
If you then wanted to output only the test-month pairs that had at least 20 records in them, you would add a HAVING clause, because the stipulation of at least 20 records only makes sense and is only computable at the aggregated group level:

```mySQL
SELECT test_name, MONTH(created_at) AS Month, COUNT(created_at) AS Num_Completed_Tests
FROM complete_tests
WHERE MONTH(created_at)=11 OR MONTH(created_at)=12
GROUP BY 1, 2
HAVING COUNT(created_at)>=20
ORDER BY 3 DESC;
```

**Question 3: Revise the query your wrote in Question 2 so that it (1) excludes the NULL and empty string entries in the breed_group field, and (2) excludes any groups that don't have at least 1,000 distinct Dog_Guids in them.  Your result should contain 8 rows.  (HINT: sometimes empty strings are registered as non-NULL values.  You might want to include the following line somewhere in your query to exclude these values as well):**

```mySQL
breed_group!=""
```



```python
%%sql
SELECT gender, breed_group, COUNT(DISTINCT dog_guid) AS Num_Dogs
FROM dogs
WHERE breed_group IS NOT NULL AND breed_group!="None" and breed_group!=""
GROUP BY 1,2
HAVING Num_Dogs >=1000
ORDER BY 3 DESC;
```

    8 rows affected.





<table>
    <tr>
        <th>gender</th>
        <th>breed_group</th>
        <th>Num_Dogs</th>
    </tr>
    <tr>
        <td>male</td>
        <td>Sporting</td>
        <td>2584</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Sporting</td>
        <td>2262</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Herding</td>
        <td>1736</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Herding</td>
        <td>1704</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Toy</td>
        <td>1473</td>
    </tr>
    <tr>
        <td>female</td>
        <td>Toy</td>
        <td>1145</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Non-Sporting</td>
        <td>1098</td>
    </tr>
    <tr>
        <td>male</td>
        <td>Working</td>
        <td>1075</td>
    </tr>
</table>



We will review several issues that can be tricky about using GROUP BY in your queries in the next lesson, but those issues will make more sense once you are sure you are comfortable with the basic functionality of the GROUP BY and HAVING clauses.  
   
      

## Practice incorporating GROUP BY and HAVING into your own queries.

**Question 4: Write a query that outputs the average number of tests completed and average mean inter-test-interval for every breed type, sorted by the average number of completed tests in descending order (popular hybrid should be the first row in your output).**


```python
%%sql
SELECT breed_type, AVG(total_tests_completed), AVG(mean_iti_minutes)
FROM dogs
GROUP BY breed_type
ORDER BY AVG(total_tests_completed);
```

    4 rows affected.





<table>
    <tr>
        <th>breed_type</th>
        <th>AVG(total_tests_completed)</th>
        <th>AVG(mean_iti_minutes)</th>
    </tr>
    <tr>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
        <td>9.54250850170034</td>
        <td>3023.0711302156274</td>
    </tr>
    <tr>
        <td>Pure Breed</td>
        <td>9.871602824737856</td>
        <td>3193.350493795222</td>
    </tr>
    <tr>
        <td>Cross Breed</td>
        <td>9.945900537634408</td>
        <td>2872.351156110182</td>
    </tr>
    <tr>
        <td>Popular Hybrid</td>
        <td>10.257530120481928</td>
        <td>2834.3205728931534</td>
    </tr>
</table>



**Question 5: Write a query that outputs the average amount of time (in hours) it took customers to complete each type of test where any individual reaction times over 6000 minutes are excluded and only average reaction times that are greater than 0 minutes are included (your output should end up with 67 rows).**



```python
%%sql
SELECT test_name, AVG(TIMESTAMPDIFF(HOUR,start_time,end_time)) AS Duration
FROM exam_answers
WHERE timestampdiff(MINUTE,start_time,end_time) < 6000
GROUP BY test_name
HAVING AVG (timestampdiff(MINUTE,start_time,end_time)) > 0 ORDER BY Duration DESC;
```

    67 rows affected.





<table>
    <tr>
        <th>test_name</th>
        <th>Duration</th>
    </tr>
    <tr>
        <td>Social-Quiz</td>
        <td>2.3382</td>
    </tr>
    <tr>
        <td>Social</td>
        <td>0.7465</td>
    </tr>
    <tr>
        <td>Diet</td>
        <td>0.6735</td>
    </tr>
    <tr>
        <td>Yawn Warm-up</td>
        <td>0.5921</td>
    </tr>
    <tr>
        <td>Activity</td>
        <td>0.5096</td>
    </tr>
    <tr>
        <td>Sociability</td>
        <td>0.3241</td>
    </tr>
    <tr>
        <td>Watching</td>
        <td>0.2822</td>
    </tr>
    <tr>
        <td>Set 3</td>
        <td>0.2641</td>
    </tr>
    <tr>
        <td>Surprise And Delight</td>
        <td>0.2592</td>
    </tr>
    <tr>
        <td>Confinement</td>
        <td>0.1757</td>
    </tr>
    <tr>
        <td>Navigation Warm-up</td>
        <td>0.1637</td>
    </tr>
    <tr>
        <td>Yawn Game</td>
        <td>0.1556</td>
    </tr>
    <tr>
        <td>Navigation Game</td>
        <td>0.1273</td>
    </tr>
    <tr>
        <td>Set 2</td>
        <td>0.1259</td>
    </tr>
    <tr>
        <td>Physical</td>
        <td>0.1232</td>
    </tr>
    <tr>
        <td>Emotions</td>
        <td>0.1173</td>
    </tr>
    <tr>
        <td>Impossible Task Warm-up</td>
        <td>0.1040</td>
    </tr>
    <tr>
        <td>Gender</td>
        <td>0.0882</td>
    </tr>
    <tr>
        <td>Watching - Part 2</td>
        <td>0.0834</td>
    </tr>
    <tr>
        <td>Stair Game</td>
        <td>0.0793</td>
    </tr>
    <tr>
        <td>Set 1</td>
        <td>0.0790</td>
    </tr>
    <tr>
        <td>Environment</td>
        <td>0.0779</td>
    </tr>
    <tr>
        <td>Turn Your Back</td>
        <td>0.0759</td>
    </tr>
    <tr>
        <td>Eye Contact Game</td>
        <td>0.0738</td>
    </tr>
    <tr>
        <td>Shaker Warm-Up</td>
        <td>0.0644</td>
    </tr>
    <tr>
        <td>Treat Warm-up</td>
        <td>0.0601</td>
    </tr>
    <tr>
        <td>Owner</td>
        <td>0.0567</td>
    </tr>
    <tr>
        <td>Cover Your Eyes</td>
        <td>0.0555</td>
    </tr>
    <tr>
        <td>Training</td>
        <td>0.0550</td>
    </tr>
    <tr>
        <td>Switch</td>
        <td>0.0531</td>
    </tr>
    <tr>
        <td>Toys</td>
        <td>0.0521</td>
    </tr>
    <tr>
        <td>Eye Contact Warm-up</td>
        <td>0.0513</td>
    </tr>
    <tr>
        <td>Different Perspective</td>
        <td>0.0493</td>
    </tr>
    <tr>
        <td>Warm-Up</td>
        <td>0.0486</td>
    </tr>
    <tr>
        <td>One Cup Warm-up</td>
        <td>0.0472</td>
    </tr>
    <tr>
        <td>Shy/Boldness</td>
        <td>0.0436</td>
    </tr>
    <tr>
        <td>Purina-Only</td>
        <td>0.0430</td>
    </tr>
    <tr>
        <td>Attachment</td>
        <td>0.0381</td>
    </tr>
    <tr>
        <td>Physical Reasoning Warm-up</td>
        <td>0.0351</td>
    </tr>
    <tr>
        <td>Perception</td>
        <td>0.0339</td>
    </tr>
    <tr>
        <td>Inferential Reasoning Warm-up</td>
        <td>0.0336</td>
    </tr>
    <tr>
        <td>Purina</td>
        <td>0.0327</td>
    </tr>
    <tr>
        <td>Puzzles</td>
        <td>0.0310</td>
    </tr>
    <tr>
        <td>Recall</td>
        <td>0.0296</td>
    </tr>
    <tr>
        <td>Foot Pointing</td>
        <td>0.0281</td>
    </tr>
    <tr>
        <td>Excitability</td>
        <td>0.0263</td>
    </tr>
    <tr>
        <td>Partnership</td>
        <td>0.0244</td>
    </tr>
    <tr>
        <td>Impossible Task Game</td>
        <td>0.0238</td>
    </tr>
    <tr>
        <td>Arm Pointing</td>
        <td>0.0238</td>
    </tr>
    <tr>
        <td>Memory versus Smell</td>
        <td>0.0235</td>
    </tr>
    <tr>
        <td>Navigation Learning</td>
        <td>0.0226</td>
    </tr>
    <tr>
        <td>Obedience</td>
        <td>0.0169</td>
    </tr>
    <tr>
        <td>Delayed Cup Game</td>
        <td>0.0158</td>
    </tr>
    <tr>
        <td>Inferential Reasoning Game</td>
        <td>0.0157</td>
    </tr>
    <tr>
        <td>Numerosity Warm-Up</td>
        <td>0.0116</td>
    </tr>
    <tr>
        <td>Two Cup Warm-up</td>
        <td>0.0086</td>
    </tr>
    <tr>
        <td>Physical Reasoning Game</td>
        <td>0.0065</td>
    </tr>
    <tr>
        <td>Memory versus Pointing</td>
        <td>0.0057</td>
    </tr>
    <tr>
        <td>Shared Perspective</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>Expression Game</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>5 vs 1 Game</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>Self Control Game</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>Slide</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>Smell Game</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>3 vs 1 Game</td>
        <td>0.0000</td>
    </tr>
    <tr>
        <td>Shaker Game</td>
        <td>0.0000</td>
    </tr>
</table>



**Question 6: Write a query that outputs the total number of unique User_Guids in each combination of State and ZIP code (postal code) in the United States, sorted first by state name in ascending alphabetical order, and second by total number of unique User_Guids in descending order (your first state should be AE and there should be 5043 rows in total in your output).**


```python
%%sql
SELECT state, zip, COUNT(DISTINCT user_guid) AS NUM_Users
FROM users
WHERE Country = "US"
GROUP BY State, zip
ORDER BY State ASC, NUM_Users DESC
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>state</th>
        <th>zip</th>
        <th>NUM_Users</th>
    </tr>
    <tr>
        <td>AE</td>
        <td>9128</td>
        <td>2</td>
    </tr>
    <tr>
        <td>AE</td>
        <td>9469</td>
        <td>1</td>
    </tr>
    <tr>
        <td>AE</td>
        <td>9845</td>
        <td>1</td>
    </tr>
    <tr>
        <td>AE</td>
        <td>9053</td>
        <td>1</td>
    </tr>
    <tr>
        <td>AE</td>
        <td>9107</td>
        <td>1</td>
    </tr>
    <tr>
        <td>AK</td>
        <td>99507</td>
        <td>3</td>
    </tr>
    <tr>
        <td>AK</td>
        <td>99709</td>
        <td>3</td>
    </tr>
    <tr>
        <td>AK</td>
        <td>99501</td>
        <td>2</td>
    </tr>
    <tr>
        <td>AK</td>
        <td>99577</td>
        <td>2</td>
    </tr>
    <tr>
        <td>AK</td>
        <td>99502</td>
        <td>1</td>
    </tr>
</table>



**Question 7: Write a query that outputs the total number of unique User_Guids in each combination of State and ZIP code in the United States *that have at least 5 users*, sorted first by state name in ascending alphabetical order, and second by total number of unique User_Guids in descending order (your first state/ZIP code combination should be AZ/86303).**


```python
%%sql
SELECT state, zip, COUNT(DISTINCT user_guid) AS NUM_Users
FROM users
WHERE Country = "US"
GROUP BY state, zip
HAVING NUM_Users >=5
ORDER BY state ASC, NUM_Users DESC
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>state</th>
        <th>zip</th>
        <th>NUM_Users</th>
    </tr>
    <tr>
        <td>AZ</td>
        <td>86303</td>
        <td>14</td>
    </tr>
    <tr>
        <td>AZ</td>
        <td>85718</td>
        <td>6</td>
    </tr>
    <tr>
        <td>AZ</td>
        <td>85253</td>
        <td>5</td>
    </tr>
    <tr>
        <td>AZ</td>
        <td>85254</td>
        <td>5</td>
    </tr>
    <tr>
        <td>AZ</td>
        <td>85260</td>
        <td>5</td>
    </tr>
    <tr>
        <td>AZ</td>
        <td>85711</td>
        <td>5</td>
    </tr>
    <tr>
        <td>AZ</td>
        <td>85749</td>
        <td>5</td>
    </tr>
    <tr>
        <td>CA</td>
        <td>92107</td>
        <td>16</td>
    </tr>
    <tr>
        <td>CA</td>
        <td>90046</td>
        <td>13</td>
    </tr>
    <tr>
        <td>CA</td>
        <td>92130</td>
        <td>12</td>
    </tr>
</table>



<mark>**Be sure to watch the next video before beginning Exercise 6, the next set of MySQL exercises</mark>,** **and feel free to practice any other queries you wish below!**


```python

```
