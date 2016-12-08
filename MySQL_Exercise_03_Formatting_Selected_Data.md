
Copyright Jana Schaich Borg/Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)

# MySQL Exercise 3: Formatting Selected Data

In this lesson, we are going to learn about three SQL clauses or functionalities that will help you format and edit the output of your queries.  We will also learn how to export the results of your formatted queries to a text file so that you can analyze them in other software packages such as Tableau or Excel.

**Begin by loading the SQL library into Jupyter, connecting to the Dognition database, and setting Dognition as the default database.**

```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```


```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb

```

    0 rows affected.





    []




## 1. Use AS to change the titles of the columns in your output

The AS clause allows you to assign an alias (a temporary name) to a table or a column in a table.  Aliases can be useful for increasing the readability of queries, for abbreviating long names, and for changing column titles in query outputs.  To implement the AS clause, include it in your query code immediately after the column or table you want to rename.  For example, if you wanted to change the name of the time stamp field of the completed_tests table from "created_at" to "time_stamp" in your output, you could take advantage of the AS clause and execute the following query:

```mySQL
SELECT dog_guid, created_at AS time_stamp
FROM complete_tests
```

Note that if you use an alias that includes a space, the alias must be surrounded in quotes:

```mySQL
SELECT dog_guid, created_at AS "time stamp"
FROM complete_tests
```

You could also make an alias for a table:

```mySQL
SELECT dog_guid, created_at AS "time stamp"
FROM complete_tests AS tests
```

Since aliases are strings, again, MySQL accepts both double and single quotation marks, but some database systems only accept single quotation marks. It is good practice to avoid using SQL keywords in your aliases, but if you have to use an SQL keyword in your alias for some reason, the string must be enclosed in backticks instead of quotation marks.

**Question 1: How would you change the title of the "start_time" field in the exam_answers table to "exam start time" in a query output?  Try it below:**


```python
%%sql
SELECT start_time AS "exam start time"
FROM exam_answers
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>exam start time</th>
    </tr>
    <tr>
        <td>2013-02-05 03:58:13</td>
    </tr>
    <tr>
        <td>2013-02-05 03:58:31</td>
    </tr>
    <tr>
        <td>2013-02-05 03:59:03</td>
    </tr>
    <tr>
        <td>2013-02-05 03:59:10</td>
    </tr>
    <tr>
        <td>2013-02-05 03:59:22</td>
    </tr>
    <tr>
        <td>2013-02-05 03:59:36</td>
    </tr>
    <tr>
        <td>2013-02-05 03:59:41</td>
    </tr>
    <tr>
        <td>2013-02-05 04:00:00</td>
    </tr>
    <tr>
        <td>2013-02-05 04:00:16</td>
    </tr>
    <tr>
        <td>2013-02-05 04:00:35</td>
    </tr>
</table>



## 2. Use DISTINCT to remove duplicate rows

Especially in databases like the Dognition database where no primary keys were declared in each table, sometimes entire duplicate rows can be entered in error.  Even with no duplicate rows present, sometimes your queries correctly output multiple instances of the same value in a column, but you are interested in knowing what the different possible values in the column are, not what each value in each row is. In both of these cases, the best way to arrive at the clean results you want is to instruct the query to return only values that are distinct, or different from all the rest.  The SQL keyword that allows you to do this is called DISTINCT.  To use it in a query, place it directly after the word SELECT in your query.

For example, if we wanted a list of all the breeds of dogs in the Dognition database, we could try the following query from a previous exercise:

```mySQL
SELECT breed
FROM dogs;
```

However, the output of this query would not be very helpful, because it would output the entry for every single row in the breed column of the dogs table, regardless of whether it duplicated the breed of a previous entry.  Fortunately, we could arrive at the list we want by executing the following query with the DISTINCT modifier:

```mySQL
SELECT DISTINCT breed
FROM dogs;
```

**Try it yourself (If you do not limit your output, you should get 2006 rows in your output):**


```python
%%sql
SELECT DISTINCT breed
FROM dogs
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>breed</th>
    </tr>
    <tr>
        <td>Labrador Retriever</td>
    </tr>
    <tr>
        <td>Shetland Sheepdog</td>
    </tr>
    <tr>
        <td>Golden Retriever</td>
    </tr>
    <tr>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>Siberian Husky</td>
    </tr>
    <tr>
        <td>Mixed</td>
    </tr>
    <tr>
        <td>Shih Tzu-Poodle Mix</td>
    </tr>
    <tr>
        <td>German Shepherd Dog-Pembroke Welsh Corgi Mix</td>
    </tr>
    <tr>
        <td>Vizsla</td>
    </tr>
    <tr>
        <td>Pug</td>
    </tr>
</table>



If you scroll through the output, you will see that no two entries are the same.  Of note, if you use the DISTINCT clause on a column that has NULL values, MySQL will include one NULL value in the DISTINCT output from that column.

<mark> When the DISTINCT clause is used with multiple columns in a SELECT statement, the combination of all the columns together is used to determine the uniqueness of a row in a result set.</mark>  
     
For example, if you wanted to know all the possible combinations of states and cities in the users table, you could query:

```mySQL
SELECT DISTINCT state, city
FROM users;
```
**Try it (if you don't limit your output you'll see 3999 rows in the query result, of which the first 1000 are displayed):**



```python
%%sql
SELECT DISTINCT state, city
FROM users
LIMIT 20;
```

    20 rows affected.





<table>
    <tr>
        <th>state</th>
        <th>city</th>
    </tr>
    <tr>
        <td>ND</td>
        <td>Grand Forks</td>
    </tr>
    <tr>
        <td>MA</td>
        <td>Barre</td>
    </tr>
    <tr>
        <td>CT</td>
        <td>Darien</td>
    </tr>
    <tr>
        <td>IL</td>
        <td>Winnetka</td>
    </tr>
    <tr>
        <td>NC</td>
        <td>Raleigh</td>
    </tr>
    <tr>
        <td>WA</td>
        <td>Auburn</td>
    </tr>
    <tr>
        <td>CO</td>
        <td>Fort Collins</td>
    </tr>
    <tr>
        <td>WA</td>
        <td>Seattle</td>
    </tr>
    <tr>
        <td>WA</td>
        <td>Bainbridge Island</td>
    </tr>
    <tr>
        <td>WA</td>
        <td>Bremerton</td>
    </tr>
    <tr>
        <td>CA</td>
        <td>Aptos</td>
    </tr>
    <tr>
        <td>OH</td>
        <td>North Ridgeville</td>
    </tr>
    <tr>
        <td>VA</td>
        <td>Charlottesville</td>
    </tr>
    <tr>
        <td>NC</td>
        <td>Hillsborough</td>
    </tr>
    <tr>
        <td>NY</td>
        <td>New York</td>
    </tr>
    <tr>
        <td>CA</td>
        <td>Los Angeles</td>
    </tr>
    <tr>
        <td>WY</td>
        <td>Worland</td>
    </tr>
    <tr>
        <td>IL</td>
        <td>Grayslake</td>
    </tr>
    <tr>
        <td>VA</td>
        <td>Falls Church</td>
    </tr>
    <tr>
        <td>WA</td>
        <td>Everett</td>
    </tr>
</table>



If you examine the query output carefully, you will see that there are many rows with California (CA) in the state column and four rows that have Gainesville in the city column (Georgia, Arkansas, Florida, and Virginia all have cities named Gainesville in our user table), but no two rows have the same state and city combination. 

When you use the DISTINCT clause with the LIMIT clause in a statement, MySQL stops searching when it finds the number of *unique* rows specified in the LIMIT clause, not when it goes through the number of rows in the LIMIT clause. 

For example, if the first 6 entries of the breed column in the dogs table were:

Labrador Retriever  
Shetland Sheepdog  
Golden Retriever  
Golden Retriever  
Shih Tzu  
Siberian Husky  

The output of the following query:

```mySQL
SELECT DISTINCT breed
FROM dogs LIMIT 5;
```
would be the first 5 different breeds:

Labrador Retriever  
Shetland Sheepdog  
Golden Retriever  
Shih Tzu  
Siberian Husky  

*not* the distinct breeds in the first 5 rows:

Labrador Retriever  
Shetland Sheepdog  
Golden Retriever  
Shih Tzu  

**Question 2: How would you list all the possible combinations of test names and subcategory names in complete_tests table? (If you do not limit your output, you should retrieve 45 possible combinations)**



```python
%%sql
SELECT DISTINCT test_name, subcategory_name
FROM complete_tests;
```

    45 rows affected.





<table>
    <tr>
        <th>test_name</th>
        <th>subcategory_name</th>
    </tr>
    <tr>
        <td>Yawn Warm-up</td>
        <td>Empathy</td>
    </tr>
    <tr>
        <td>Yawn Game</td>
        <td>Empathy</td>
    </tr>
    <tr>
        <td>Eye Contact Warm-up</td>
        <td>Empathy</td>
    </tr>
    <tr>
        <td>Eye Contact Game</td>
        <td>Empathy</td>
    </tr>
    <tr>
        <td>Treat Warm-up</td>
        <td>Communication</td>
    </tr>
    <tr>
        <td>Arm Pointing</td>
        <td>Communication</td>
    </tr>
    <tr>
        <td>Foot Pointing</td>
        <td>Communication</td>
    </tr>
    <tr>
        <td>Watching</td>
        <td>Cunning</td>
    </tr>
    <tr>
        <td>Turn Your Back</td>
        <td>Cunning</td>
    </tr>
    <tr>
        <td>Cover Your Eyes</td>
        <td>Cunning</td>
    </tr>
    <tr>
        <td>Watching - Part 2</td>
        <td>Cunning</td>
    </tr>
    <tr>
        <td>One Cup Warm-up</td>
        <td>Memory</td>
    </tr>
    <tr>
        <td>Two Cup Warm-up</td>
        <td>Memory</td>
    </tr>
    <tr>
        <td>Memory versus Pointing</td>
        <td>Memory</td>
    </tr>
    <tr>
        <td>Memory versus Smell</td>
        <td>Memory</td>
    </tr>
    <tr>
        <td>Delayed Cup Game</td>
        <td>Memory</td>
    </tr>
    <tr>
        <td>Inferential Reasoning Warm-up</td>
        <td>Reasoning</td>
    </tr>
    <tr>
        <td>Inferential Reasoning Game</td>
        <td>Reasoning</td>
    </tr>
    <tr>
        <td>Physical Reasoning Warm-up</td>
        <td>Reasoning</td>
    </tr>
    <tr>
        <td>Physical Reasoning Game</td>
        <td>Reasoning</td>
    </tr>
    <tr>
        <td>Navigation Warm-up</td>
        <td>Spatial Navigation</td>
    </tr>
    <tr>
        <td>Navigation Learning</td>
        <td>Spatial Navigation</td>
    </tr>
    <tr>
        <td>Navigation Game</td>
        <td>Spatial Navigation</td>
    </tr>
    <tr>
        <td>Impossible Task Warm-up</td>
        <td>Impossible Task</td>
    </tr>
    <tr>
        <td>Impossible Task Game</td>
        <td>Impossible Task</td>
    </tr>
    <tr>
        <td>Numerosity Warm-Up</td>
        <td>Numerosity</td>
    </tr>
    <tr>
        <td>5 vs 1 Game</td>
        <td>Numerosity</td>
    </tr>
    <tr>
        <td>3 vs 1 Game</td>
        <td>Numerosity</td>
    </tr>
    <tr>
        <td>Warm-Up</td>
        <td>Social Bias</td>
    </tr>
    <tr>
        <td>1 vs 1 Game</td>
        <td>Social Bias</td>
    </tr>
    <tr>
        <td>5 vs 1 Game</td>
        <td>Social Bias</td>
    </tr>
    <tr>
        <td>Warm-up</td>
        <td>Smell Game</td>
    </tr>
    <tr>
        <td>Smell Game</td>
        <td>Smell Game</td>
    </tr>
    <tr>
        <td>Warm-Up</td>
        <td>Shell Game</td>
    </tr>
    <tr>
        <td>Switch</td>
        <td>Shell Game</td>
    </tr>
    <tr>
        <td>Slide</td>
        <td>Shell Game</td>
    </tr>
    <tr>
        <td>Warm-Up</td>
        <td>Self Control Game</td>
    </tr>
    <tr>
        <td>Self Control Game</td>
        <td>Self Control Game</td>
    </tr>
    <tr>
        <td>Warm-Up</td>
        <td>Expression Game</td>
    </tr>
    <tr>
        <td>Expression Game</td>
        <td>Expression Game</td>
    </tr>
    <tr>
        <td>Different Perspective</td>
        <td>Perspective Game</td>
    </tr>
    <tr>
        <td>Shared Perspective</td>
        <td>Perspective Game</td>
    </tr>
    <tr>
        <td>Stair Game</td>
        <td>Laterality</td>
    </tr>
    <tr>
        <td>Shaker Warm-Up</td>
        <td>Shaker Game</td>
    </tr>
    <tr>
        <td>Shaker Game</td>
        <td>Shaker Game</td>
    </tr>
</table>



## 3. Use ORDER BY to sort the output of your query

As you might have noticed already when examining the output of the queries you have executed thus far, databases do not have built-in sorting mechanisms that automatically sort the output of your query.  However, SQL permits the use of the powerful ORDER BY clause to allow you to sort the output according to your own specifications.  Let's look at how you would implement a simple ORDER BY clause.  

Recall our query outline:

<img src="https://duke.box.com/shared/static/l9v2khefe7er98pj1k6oyhmku4tz5wpf.jpg" width=400 alt="SELECT FROM WHERE ORDER BY" />

Your ORDER BY clause will come after everything else in the main part of your query, but before a LIMIT clause.  

If you wanted the breeds of dogs in the dog table sorted in alphabetical order, you could query:

```mySQL
SELECT DISTINCT breed
FROM dogs 
ORDER BY breed
```

**Try it yourself:**


```python
%%sql
SELECT DISTINCT breed
FROM dogs 
ORDER BY breed
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>breed</th>
    </tr>
    <tr>
        <td>-American Eskimo Dog Mix</td>
    </tr>
    <tr>
        <td>-American Pit Bull Terrier Mix</td>
    </tr>
    <tr>
        <td>-Anatolian Shepherd Dog Mix</td>
    </tr>
    <tr>
        <td>-Australian Cattle Dog Mix</td>
    </tr>
    <tr>
        <td>-Australian Shepherd Mix</td>
    </tr>
    <tr>
        <td>-Beagle Mix</td>
    </tr>
    <tr>
        <td>-Bichon Frise Mix</td>
    </tr>
    <tr>
        <td>-Bluetick Coonhound Mix</td>
    </tr>
    <tr>
        <td>-Border Collie Mix</td>
    </tr>
    <tr>
        <td>-Boxer Mix</td>
    </tr>
</table>



(You might notice that some of the breeds start with a hyphen; we'll come back to that later.)

The default is to sort the output in ascending order.  However, you can tell SQL to sort the output in descending order as well:

```mySQL
SELECT DISTINCT breed
FROM dogs 
ORDER BY breed DESC
```

Combining ORDER BY with LIMIT gives you an easy way to select the "top 10" and "last 10" in a list or column.  For example, you could select the User IDs and Dog IDs of the 5 customer-dog pairs who spent the least median amount of time between their Dognition tests:

```mySQL
SELECT DISTINCT user_guid, median_ITI_minutes
FROM dogs 
ORDER BY median_ITI_minutes
LIMIT 5
```
or the greatest median amount of time between their Dognition tests:

```mySQL
SELECT DISTINCT user_guid, median_ITI_minutes
FROM dogs 
ORDER BY median_ITI_minutes DESC
LIMIT 5
```

You can also sort your output based on a derived field.  If you wanted your inter-test interval to be expressed in seconds instead of minutes, you could incorporate a derived column and an alias into your last query to get the 5 customer-dog pairs who spent the greatest median amount of time between their Dognition tests in seconds:

```mySQL
SELECT DISTINCT user_guid, (median_ITI_minutes * 60) AS median_ITI_sec
FROM dogs 
ORDER BY median_ITI_sec DESC
LIMIT 5
```

Note that the parentheses are important in that query; without them, the database would try to make an alias for 60 instead of median_ITI_minutes * 60.

SQL queries also allow you to sort by multiple fields in a specified order, similar to how Excel allows to include multiple levels in a sort (see image below):

<img src="https://duke.box.com/shared/static/lbubaw9rkqoyv5xd61y57o3lpqkvrj10.jpg" width=600 alt="SELECT FROM WHERE" />

To achieve this in SQL, you include all the fields (or aliases) by which you want to sort the results after the ORDER BY clause, separated by commas, in the order you want them to be used for sorting.  You can then specify after each field whether you want the sort using that field to be ascending or descending.

If you wanted to select all the distinct User IDs of customers in the United States (abbreviated "US") and sort them according to the states they live in in alphabetical order first, and membership type second, you could query:

```mySQL
SELECT DISTINCT user_guid, state, membership_type
FROM users
WHERE country="US"
ORDER BY state ASC, membership_type ASC
```

**Go ahead and try it yourself (if you do not limit the output, you should get 9356 rows in your output):**


```python
%%sql
SELECT DISTINCT user_guid, state, membership_type
FROM users
WHERE country="US"
ORDER BY state ASC, membership_type ASC
LIMIT 100;
```

    100 rows affected.





<table>
    <tr>
        <th>user_guid</th>
        <th>state</th>
        <th>membership_type</th>
    </tr>
    <tr>
        <td>ce138312-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AE</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7587ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AE</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce76f528-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AE</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce221dbe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AE</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce70836e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AE</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce969298-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AE</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce26e01a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce351572-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce3c3b36-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce4170ec-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce666e38-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7007a4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce718782-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce72c64c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce72ef5a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce735210-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce741718-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce74f656-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce756d52-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce77149a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7c50ae-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7cc106-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce945e92-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce95ab26-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce9767ae-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce98bdca-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce267512-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce29dde2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce45974e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce9ab9b8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce88a2f0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce2533f0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce2d932e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce34b564-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce4018c8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce46ba34-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce46bafc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce6d9a82-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce6e8bfe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce72b0a8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce733e74-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7462f4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce74ae1c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce74dca2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7b62a2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7c0e32-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7c5e6e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7cf14e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7e4d82-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7e5b06-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7eb920-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7ed1da-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce819190-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce82a382-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce8327bc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce83c6f4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce844dea-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce84c2e8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce878f28-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce8b2c1e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce957a0c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce96b9c6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce971916-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce9aece4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce242aa0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce289324-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce6ed06e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce6f86bc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce723c0e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce727124-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce7cb79c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce97cff0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce7031c0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce730c6a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce840a06-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce7c06bc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AP</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce245e6c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AP</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce2b2ba2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AP</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce6e84d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AP</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce255b64-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce26e074-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce3f6d6a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce4063a0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce6dbc88-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce6ddd44-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce6ed74e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce70b2e4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce70f2e0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce718250-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7745d2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce778510-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce784f40-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce7a5d80-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce81b1ac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce828082-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce8346a2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce854704-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce8b1c74-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce90b1de-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
    <tr>
        <td>ce96c0e2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>1</td>
    </tr>
</table>



You might notice that some of the rows have null values in the state field.  You could revise your query to only select rows that do not have null values in either the state or membership_type column:

```mySQL
SELECT DISTINCT user_guid, state, membership_type
FROM users
WHERE country="US" AND state IS NOT NULL and membership_type IS NOT NULL
ORDER BY state ASC, membership_type ASC
```

**Question 3: Below, try executing a query that would sort the same output as described above by membership_type first in descending order, and state second in ascending order:**


```python
%%sql
SELECT DISTINCT user_guid, state, membership_type
FROM users
WHERE country="US" AND state IS NOT NULL and membership_type IS NOT NULL
ORDER BY membership_type DESC, state ASC 
LIMIT 100;
```

    100 rows affected.





<table>
    <tr>
        <th>user_guid</th>
        <th>state</th>
        <th>membership_type</th>
    </tr>
    <tr>
        <td>ce7f9a2a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce80ec72-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce88fa84-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce815e0a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CO</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce802602-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CT</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce7ff100-7144-11e5-ba71-058fbc01cf0b</td>
        <td>FL</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce803a0c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>FL</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce804394-7144-11e5-ba71-058fbc01cf0b</td>
        <td>FL</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce8756ac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>FL</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce80c184-7144-11e5-ba71-058fbc01cf0b</td>
        <td>IN</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce80af14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>KS</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce7fb4c4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>KY</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce801d06-7144-11e5-ba71-058fbc01cf0b</td>
        <td>MA</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce7fcf54-7144-11e5-ba71-058fbc01cf0b</td>
        <td>MI</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce80adf2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>MI</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce95a360-7144-11e5-ba71-058fbc01cf0b</td>
        <td>MO</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce80ad98-7144-11e5-ba71-058fbc01cf0b</td>
        <td>MS</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce7fca7c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NY</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce800cee-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NY</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce8094ac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NY</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce84a3f8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NY</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce802972-7144-11e5-ba71-058fbc01cf0b</td>
        <td>OH</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce865540-7144-11e5-ba71-058fbc01cf0b</td>
        <td>OH</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce805956-7144-11e5-ba71-058fbc01cf0b</td>
        <td>SC</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce808bce-7144-11e5-ba71-058fbc01cf0b</td>
        <td>SC</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce7fb82a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>VA</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce81536a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>VA</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce8040ec-7144-11e5-ba71-058fbc01cf0b</td>
        <td>WA</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce80b630-7144-11e5-ba71-058fbc01cf0b</td>
        <td>WI</td>
        <td>5</td>
    </tr>
    <tr>
        <td>ce88b6be-7144-11e5-ba71-058fbc01cf0b</td>
        <td>IL</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce29002a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce29046c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce2e2708-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce2e4756-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce2f7a4a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce34682a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce35ba0e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce38a660-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce3ac21a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce3bff68-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce3d21cc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce3df5d4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce767486-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce7f674e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce81b8b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce8306e2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce850848-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce931488-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce94489e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce94f9a6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce950f9a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>N/A</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce326d2c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NC</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce8b1cf6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NC</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce25accc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NY</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce3dcaaa-7144-11e5-ba71-058fbc01cf0b</td>
        <td>NY</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce2a6e7e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>TN</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce2bf226-7144-11e5-ba71-058fbc01cf0b</td>
        <td>TX</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce2e6b46-7144-11e5-ba71-058fbc01cf0b</td>
        <td>WA</td>
        <td>4</td>
    </tr>
    <tr>
        <td>ce969298-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AE</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce88a2f0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AK</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce7031c0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce730c6a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce840a06-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AL</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce78f12a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AR</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce40ffea-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce4701b0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce664890-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6c4d44-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6cce4a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6ee252-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce72019e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce98bd0c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>AZ</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce2791c2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce28e36a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce3f9768-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce419022-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce662f54-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce663a26-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce66efac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce670e56-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6c72ce-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6e22c2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6e272c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6e69a8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6e6c32-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce6f9d8c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce70a79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce70c07c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce7102da-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce711400-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce712666-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce7131a6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce7264a4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce734054-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce735760-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce7396d0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce73d1d6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce7426f4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce74311c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
    <tr>
        <td>ce74abf6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>CA</td>
        <td>3</td>
    </tr>
</table>



## 4. Export your query results to a text file 

Next week, we will learn how to complete some basic forms of data analysis in SQL.  However, if you know how to use other analysis or visualization software like Excel or Tableau, you can implement these analyses with the SQL skills you have gained already, as long as you can export the results of your SQL queries in a format other software packages can read.  Almost every database interface has a different method for exporting query results, so you will need to look up how to do it every time you try a new interface (another place where having a desire to learn new things will come in handy!). 

There are two ways to export your query results using our Jupyter interface.  

1.  You can select and copy the output you see in an output window, and paste it into another program.  Although this strategy is very simple, it only works if your output is very limited in size (since you can only paste 1000 rows at a time).

2.  You can tell MySQL to put the results of a query into a variable (for our purposes consider a variable to be a temporary holding place), and then use Python code to format the data in the variable as a CSV file (comma separated value file, a .CSV file) that can be downloaded.  When you use this strategy, all of the results of a query will be saved into the variable, not just the first 1000 rows as displayed in Jupyter, even if we have set up Jupyter to only display 1000 rows of the output.  

Let's see how we could export query results using the second method.

To tell MySQL to put the results of a query into a variable, use the following syntax:

>```python
variable_name_of_your_choice = %sql [your full query goes here];
```

In this case, you must execute your SQL query all on one line.  So if you wanted to export the list of dog breeds in the dogs table, you could begin by executing:

>```python
breed_list = %sql SELECT DISTINCT breed FROM dogs ORDER BY breed;
```

**Go ahead and try it:**



```python
breed_list = %sql SELECT DISTINCT breed FROM dogs ORDER BY breed;
```

    2006 rows affected.


Once your variable is created, using the above command tell Jupyter to format the variable as a csv file using the following syntax:

>```python
the_output_name_you_want.csv('the_output_name_you_want.csv')
```

Since this line is being run in Python, do NOT include the %sql prefix when trying to execute the line.  We could therefore export the breed list by executing:

>```python
breed_list.csv('breed_list.csv')
```

When you do this, all of the results of the query will be saved in the text file but the results will not be displayed in your notebook.  This is a convenient way to retrieve large amounts of data from a query without taxing your browser or the server.  
     
**Try it yourself:**


```python
breed_list.csv('breed_list.csv')
```




<a href="./files/breed_list.csv">CSV results</a>



You should see a link in the output line that says "CSV results." You can click on this link to see the text file in a tab in your browser or to download the file to your computer (exactly how this works will differ depending on your browser and settings, but your options will be the same as if you were trying to open or download a file from any other website.) 

You can also open the file directly from the home page of your Jupyter account.  Behind the scenes, your csv file was written to your directory on the Jupyter server, so you should now see this file listed in your Jupyter account landing page along with the list of your notebooks.  Just like a notebook, you can copy it, rename it, or delete it from your directory by clicking on the check box next to the file and clicking the "duplicate," "rename," or trash can buttons at the top of the page.

<img src="https://duke.box.com/shared/static/0k33vrxct1k03iz5u0cunfzf81vyn3ns.jpg" width=400 alt="JUPYTER SCREEN SHOT" />


## 5.  A Bird's Eye View of Other Functions You Might Want to Explore

When you open your breed list results file, you will notice the following:

1) All of the rows of the output are included, even though you can only see 1000 of those rows when you run the query through the Jupyter interface.

2) There are some strange values in the breed list.  Some of the entries in the breed column seem to have a dash included before the name.  This is an example of what real business data sets look like...they are messy!  We will use this as an opportunity to highlight why it is so important to be curious and explore MySQL functions on your own. 

If you needed an accurate list of all the dog breeds in the dogs table, you would have to find some way to "clean up" the breed list you just made.  Let's examine some of the functions that could help you achieve this cleaning using SQL syntax rather than another program or language outside of the database.

I included these links to MySQL functions in an earlier notebook:  
http://dev.mysql.com/doc/refman/5.7/en/func-op-summary-ref.html  
http://www.w3resource.com/mysql/mysql-functions-and-operators.php

The following description of a function called REPLACE is included in that resource:

"REPLACE(str,from_str,to_str)  
Returns the string str with all occurrences of the string from_str replaced by the string to_str. REPLACE() performs a case-sensitive match when searching for from_str."

One thing we could try is using this function to replace any dashes included in the breed names with no character:

```mySQL
SELECT DISTINCT breed,
REPLACE(breed,'-','') AS breed_fixed
FROM dogs
ORDER BY breed_fixed
```

In this query, we put the field/column name in the replace function where the syntax instructions listed "str" in order to tell the REPLACE function to act on the entire column.  The "-" was the "from_str", which is the string we wanted to replace.  The "" was the to_str, which is the character with which we want to replace the "from_str".  

**Try looking at the output:**



```python
%%sql
SELECT DISTINCT breed,
REPLACE(breed,'-','') AS breed_fixed
FROM dogs
ORDER BY breed_fixed
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>breed</th>
        <th>breed_fixed</th>
    </tr>
    <tr>
        <td>Affenpinscher</td>
        <td>Affenpinscher</td>
    </tr>
    <tr>
        <td>Affenpinscher-Afghan Hound Mix</td>
        <td>AffenpinscherAfghan Hound Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Airedale Terrier Mix</td>
        <td>AffenpinscherAiredale Terrier Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Alaskan Malamute Mix</td>
        <td>AffenpinscherAlaskan Malamute Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-American English Coonhound Mix</td>
        <td>AffenpinscherAmerican English Coonhound Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Brussels Griffon Mix</td>
        <td>AffenpinscherBrussels Griffon Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Poodle Mix</td>
        <td>AffenpinscherPoodle Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Rat Terrier Mix</td>
        <td>AffenpinscherRat Terrier Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Spanish Water Dog Mix</td>
        <td>AffenpinscherSpanish Water Dog Mix</td>
    </tr>
    <tr>
        <td>Afghan Hound</td>
        <td>Afghan Hound</td>
    </tr>
</table>



That was helpful, but you'll still notice some issues with the output.

First, the leading dashes are indeed removed in the breed_fixed column, but now the dashes used to separate breeds in entries like 'French Bulldog-Boston Terrier Mix' are missing as well. So REPLACE isn't the right choice to selectively remove leading dashes.

Perhaps we could try using the TRIM function:

http://www.w3resource.com/mysql/string-functions/mysql-trim-function.php

   
```sql
SELECT DISTINCT breed, TRIM(LEADING '-' FROM breed) AS breed_fixed
FROM dogs
ORDER BY breed_fixed
```
  
**Try the query written above yourself, and inspect the output carefully:**


```python
%%sql
SELECT DISTINCT breed, TRIM(LEADING '-' FROM breed) AS breed_fixed
FROM dogs
ORDER BY breed_fixed
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>breed</th>
        <th>breed_fixed</th>
    </tr>
    <tr>
        <td>Affenpinscher</td>
        <td>Affenpinscher</td>
    </tr>
    <tr>
        <td>Affenpinscher-Afghan Hound Mix</td>
        <td>Affenpinscher-Afghan Hound Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Airedale Terrier Mix</td>
        <td>Affenpinscher-Airedale Terrier Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Alaskan Malamute Mix</td>
        <td>Affenpinscher-Alaskan Malamute Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-American English Coonhound Mix</td>
        <td>Affenpinscher-American English Coonhound Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Brussels Griffon Mix</td>
        <td>Affenpinscher-Brussels Griffon Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Poodle Mix</td>
        <td>Affenpinscher-Poodle Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Rat Terrier Mix</td>
        <td>Affenpinscher-Rat Terrier Mix</td>
    </tr>
    <tr>
        <td>Affenpinscher-Spanish Water Dog Mix</td>
        <td>Affenpinscher-Spanish Water Dog Mix</td>
    </tr>
    <tr>
        <td>Afghan Hound</td>
        <td>Afghan Hound</td>
    </tr>
</table>



That certainly gets us a lot closer to the list we might want, but there are still some entries in the breed_fixed column that are conceptual duplicates of each other, due to poor consistency in how the breed names were entered.  For example, one entry is "Beagle Mix" while another is "Beagle- Mix".  These entries are clearly meant to refer to the same breed, but they will be counted as separate breeds as long as their breed names are different.

Cleaning up all of the entries in the breed column would take quite a bit of work, so we won't go through more details about how to do it in this lesson.  Instead, use this exercise as a reminder for why it's so important to always look at the details of your data, and as motivation to explore the MySQL functions we won't have time to discuss in the course.  If you push yourself to learn new SQL functions and embrace the habit of getting to know your data by exploring its raw values and outputs, you will find that SQL provides very efficient tools to clean real-world messy data sets, and you will arrive at the correct conclusions about what your data indicate your company should do.

## Now it's time to practice using AS, DISTINCT, and ORDER BY in your own queries.


**Question 4: How would you get a list of all the subcategories of Dognition tests, in alphabetical order, with no test listed more than once (if you do not limit your output, you should retrieve 16 rows)?**


```python
%%sql
SELECT DISTINCT subcategory_name
FROM complete_tests
ORDER BY subcategory_name;
```

    16 rows affected.





<table>
    <tr>
        <th>subcategory_name</th>
    </tr>
    <tr>
        <td>Communication</td>
    </tr>
    <tr>
        <td>Cunning</td>
    </tr>
    <tr>
        <td>Empathy</td>
    </tr>
    <tr>
        <td>Expression Game</td>
    </tr>
    <tr>
        <td>Impossible Task</td>
    </tr>
    <tr>
        <td>Laterality</td>
    </tr>
    <tr>
        <td>Memory</td>
    </tr>
    <tr>
        <td>Numerosity</td>
    </tr>
    <tr>
        <td>Perspective Game</td>
    </tr>
    <tr>
        <td>Reasoning</td>
    </tr>
    <tr>
        <td>Self Control Game</td>
    </tr>
    <tr>
        <td>Shaker Game</td>
    </tr>
    <tr>
        <td>Shell Game</td>
    </tr>
    <tr>
        <td>Smell Game</td>
    </tr>
    <tr>
        <td>Social Bias</td>
    </tr>
    <tr>
        <td>Spatial Navigation</td>
    </tr>
</table>



**Question 5: How would you create a text file with a list of all the non-United States countries of Dognition customers with no country listed more than once?**


```python
non_us_countries = %sql SELECT DISTINCT country FROM users WHERE country!= "US";
non_us_countries.csv("non_us_countries.csv")
```

    68 rows affected.





<a href="./files/non_us_countries.csv">CSV results</a>



**Question 6: How would you find the User ID, Dog ID, and test name of the first 10 tests to ever be completed in the Dognition database?**


```python
%%sql
SELECT user_guid, dog_guid, test_name
FROM complete_tests
ORDER BY created_at
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>user_guid</th>
        <th>dog_guid</th>
        <th>test_name</th>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Yawn Warm-up</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Yawn Game</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Eye Contact Warm-up</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Eye Contact Game</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Treat Warm-up</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Arm Pointing</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Foot Pointing</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Watching</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Turn Your Back</td>
    </tr>
    <tr>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Cover Your Eyes</td>
    </tr>
</table>



**Question 7: How would create a text file with a list of all the customers with yearly memberships who live in the state of North Carolina (USA) and joined Dognition after March 1, 2014, sorted so that the most recent member is at the top of the list?**


```python
NC_yearly_after_March_1_2014 = %sql SELECT DISTINCT user_guid, state, created_at FROM users WHERE membership_type=2 AND state = "NC" AND country = "US" AND created_at >'2014_03_01' ORDER BY created_at DESC
```

    68 rows affected.



```python
NC_yearly_after_March_1_2014.csv('NC_yearly_after_March_1_2014.csv')
```




<a href="./files/NC_yearly_after_March_1_2014.csv">CSV results</a>



**Question 8: See if you can find an SQL function from the list provided at:**

http://www.w3resource.com/mysql/mysql-functions-and-operators.php

**that would allow you to output all of the distinct breed names in UPPER case.  Create a query that would output a list of these names in upper case, sorted in alphabetical order.**


```python
%%sql
SELECT DISTINCT UPPER(breed)
FROM dogs
ORDER BY breed
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>UPPER(breed)</th>
    </tr>
    <tr>
        <td>-AMERICAN ESKIMO DOG MIX</td>
    </tr>
    <tr>
        <td>-AMERICAN PIT BULL TERRIER MIX</td>
    </tr>
    <tr>
        <td>-ANATOLIAN SHEPHERD DOG MIX</td>
    </tr>
    <tr>
        <td>-AUSTRALIAN CATTLE DOG MIX</td>
    </tr>
    <tr>
        <td>-AUSTRALIAN SHEPHERD MIX</td>
    </tr>
    <tr>
        <td>-BEAGLE MIX</td>
    </tr>
    <tr>
        <td>-BICHON FRISE MIX</td>
    </tr>
    <tr>
        <td>-BLUETICK COONHOUND MIX</td>
    </tr>
    <tr>
        <td>-BORDER COLLIE MIX</td>
    </tr>
    <tr>
        <td>-BOXER MIX</td>
    </tr>
</table>



**Practice any other queries you want to try below!**


```python

```
