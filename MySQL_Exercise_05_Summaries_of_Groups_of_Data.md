
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

    40 rows affected.





<table>
    <tr>
        <th>test_name</th>
        <th>AVG_Rating</th>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>3.9206</td>
    </tr>
    <tr>
        <td>3 vs 1 Game</td>
        <td>4.2857</td>
    </tr>
    <tr>
        <td>5 vs 1 Game</td>
        <td>3.9272</td>
    </tr>
    <tr>
        <td>Arm Pointing</td>
        <td>4.2153</td>
    </tr>
    <tr>
        <td>Cover Your Eyes</td>
        <td>2.6741</td>
    </tr>
    <tr>
        <td>Delayed Cup Game</td>
        <td>3.3514</td>
    </tr>
    <tr>
        <td>Different Perspective</td>
        <td>2.7647</td>
    </tr>
    <tr>
        <td>Expression Game</td>
        <td>4.0000</td>
    </tr>
    <tr>
        <td>Eye Contact Game</td>
        <td>2.9372</td>
    </tr>
    <tr>
        <td>Eye Contact Warm-up</td>
        <td>0.9632</td>
    </tr>
    <tr>
        <td>Foot Pointing</td>
        <td>4.0093</td>
    </tr>
    <tr>
        <td>Impossible Task Game</td>
        <td>3.0965</td>
    </tr>
    <tr>
        <td>Impossible Task Warm-up</td>
        <td>0.2174</td>
    </tr>
    <tr>
        <td>Inferential Reasoning Game</td>
        <td>4.5223</td>
    </tr>
    <tr>
        <td>Inferential Reasoning Warm-up</td>
        <td>4.3066</td>
    </tr>
    <tr>
        <td>Memory versus Pointing</td>
        <td>3.5584</td>
    </tr>
    <tr>
        <td>Memory versus Smell</td>
        <td>4.2623</td>
    </tr>
    <tr>
        <td>Navigation Game</td>
        <td>2.9841</td>
    </tr>
    <tr>
        <td>Navigation Learning</td>
        <td>2.0303</td>
    </tr>
    <tr>
        <td>Navigation Warm-up</td>
        <td>1.9805</td>
    </tr>
    <tr>
        <td>Numerosity Warm-Up</td>
        <td>2.6173</td>
    </tr>
    <tr>
        <td>One Cup Warm-up</td>
        <td>1.3693</td>
    </tr>
    <tr>
        <td>Physical Reasoning Game</td>
        <td>3.8492</td>
    </tr>
    <tr>
        <td>Physical Reasoning Warm-up</td>
        <td>1.6625</td>
    </tr>
    <tr>
        <td>Self Control Game</td>
        <td>3.8519</td>
    </tr>
    <tr>
        <td>Shaker Game</td>
        <td>4.6667</td>
    </tr>
    <tr>
        <td>Shaker Warm-Up</td>
        <td>2.1818</td>
    </tr>
    <tr>
        <td>Shared Perspective</td>
        <td>3.2778</td>
    </tr>
    <tr>
        <td>Slide</td>
        <td>4.5111</td>
    </tr>
    <tr>
        <td>Smell Game</td>
        <td>4.2857</td>
    </tr>
    <tr>
        <td>Stair Game</td>
        <td>4.2857</td>
    </tr>
    <tr>
        <td>Switch</td>
        <td>5.5676</td>
    </tr>
    <tr>
        <td>Treat Warm-up</td>
        <td>0.7909</td>
    </tr>
    <tr>
        <td>Turn Your Back</td>
        <td>3.1293</td>
    </tr>
    <tr>
        <td>Two Cup Warm-up</td>
        <td>1.6737</td>
    </tr>
    <tr>
        <td>Warm-Up</td>
        <td>1.2020</td>
    </tr>
    <tr>
        <td>Watching</td>
        <td>2.4594</td>
    </tr>
    <tr>
        <td>Watching - Part 2</td>
        <td>2.6570</td>
    </tr>
    <tr>
        <td>Yawn Game</td>
        <td>2.8477</td>
    </tr>
    <tr>
        <td>Yawn Warm-up</td>
        <td>2.0035</td>
    </tr>
</table>



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

```

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

```

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

```

We will review several issues that can be tricky about using GROUP BY in your queries in the next lesson, but those issues will make more sense once you are sure you are comfortable with the basic functionality of the GROUP BY and HAVING clauses.  
   
      

## Practice incorporating GROUP BY and HAVING into your own queries.

**Question 4: Write a query that outputs the average number of tests completed and average mean inter-test-interval for every breed type, sorted by the average number of completed tests in descending order (popular hybrid should be the first row in your output).**


```python

```

**Question 5: Write a query that outputs the average amount of time (in hours) it took customers to complete each type of test where any individual reaction times over 6000 minutes are excluded and only average reaction times that are greater than 0 minutes are included (your output should end up with 67 rows).**



```python

```

**Question 6: Write a query that outputs the total number of unique User_Guids in each combination of State and ZIP code (postal code) in the United States, sorted first by state name in ascending alphabetical order, and second by total number of unique User_Guids in descending order (your first state should be AE and there should be 5043 rows in total in your output).**


```python

```

**Question 7: Write a query that outputs the total number of unique User_Guids in each combination of State and ZIP code in the United States *that have at least 5 users*, sorted first by state name in ascending alphabetical order, and second by total number of unique User_Guids in descending order (your first state/ZIP code combination should be AZ/86303).**


```python

```

<mark>**Be sure to watch the next video before beginning Exercise 6, the next set of MySQL exercises</mark>,** **and feel free to practice any other queries you wish below!**


```python

```
