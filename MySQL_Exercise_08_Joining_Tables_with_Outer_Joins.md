
Copyright Jana Schaich Borg/Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)

# MySQL Exercise 8: Joining Tables with Outer Joins

We focused on inner joins in the last set of exercises.  Most of the time inner joins will give you the results you are looking for.  Occasionally, though, you might want to include all the data from a table in your calculations or your output, even if those data do not all match up with the data from the other tables you are joining with.  
    
For example, if you have a table with customer demographic information and a table with information about which customers were sent a free sample, your might want to analyze the characteristics of customers who did and did not receive the sample.  The best way to do that in a program like Tableau would be to have all your customer information in one table with an extra column indicating whether the customer received the free sample or not.  Alternatively, you might want to generate a list of customers who did *not* receive the free sample, so that you can arrange for the customers to receive a free sample in the future.

In these types of situations, you will use outer joins to connect tables.  Outer joins include left joins, right joins, or full outer joins (recall that full outer joins are NOT supported in MySQL).  Refer to the videos "What are Joins?" and "Joins With Many to Many Relationships and Duplicates" for more information about joins. 

Here's a picture to remind you of the general concepts behind outer joins:

<img src="https://duke.box.com/shared/static/4s89rmm8a75tep4puyqt1tdlhic0stzx.jpg" width=400 alt="SELECT FROM WHERE" />

To begin practicing outer joins, load the sql library, connect to the Dognition database, and make the Dognition database your default database: 



```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

    0 rows affected.





    []



## Left and Right Joins

Left and right joins use a different sytax than we used in the lesson about inner joins.  The method I showed you to execute inner joins tells the database how to relate tables in a WHERE clause like this:

```mySQL
WHERE d.dog_guid=r.dog_guid
```

I find this syntax -- called the "equijoin" syntax -- to be very intuitive, so I thought it would be a good idea to start with it.  However, we can re-write the inner joins in the same syntax used by outer joins.  To use this more traditional syntax, you have to tell the database how to connect the tables using an ON clause that comes right after the FROM clause.  Make sure to specify the word "JOIN" explicitly.  This traditional version of the syntax frees up the WHERE clause for other things you might want to include in your query.  Here's what one of our queries from the inner join lesson would look like using the traditional syntax:

```mySQL
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d JOIN reviews r
  ON d.dog_guid=r.dog_guid AND d.user_guid=r.user_guid
GROUP BY d.user_guid
HAVING NumRatings > 9
ORDER BY AvgRating DESC
LIMIT 200
```

You could also write "INNER JOIN" instead of "JOIN" but the default in MySQL is that JOIN will mean inner join, so including the word "INNER" is optional.

If you need a WHERE clause in the query above, it would go after the ON clause and before the GROUP BY clause.

Here's an example of a different query we used in the last lesson that employed the equijoin syntax:

```mySQL
SELECT d.user_guid AS UserID, d.dog_guid AS DogID, 
       d.breed, d.breed_type, d.breed_group
FROM dogs d, complete_tests c
WHERE d.dog_guid=c.dog_guid AND test_name='Yawn Warm-up';
```

**Question 1: How would you re-write this query using the traditional join syntax?**


```python
%%sql
SELECT d.user_guid AS UserID, d.dog_guid AS DogID, 
       d.breed, d.breed_type, d.breed_group
FROM dogs d JOIN complete_tests c
WHERE d.dog_guid=c.dog_guid AND test_name='Yawn Warm-up'
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>UserID</th>
        <th>DogID</th>
        <th>breed</th>
        <th>breed_type</th>
        <th>breed_group</th>
    </tr>
    <tr>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b272-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Labrador Retriever</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
    </tr>
    <tr>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b5ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shetland Sheepdog</td>
        <td>Pure Breed</td>
        <td>Herding</td>
    </tr>
    <tr>
        <td>ce135ab8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b6b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Golden Retriever</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
    </tr>
    <tr>
        <td>ce13507c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Golden Retriever</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
        <td>Pure Breed</td>
        <td>Toy</td>
    </tr>
    <tr>
        <td>ce13615c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b948-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Siberian Husky</td>
        <td>Pure Breed</td>
        <td>Working</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27ba1a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
        <td>Pure Breed</td>
        <td>Toy</td>
    </tr>
    <tr>
        <td>ce135f2c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27bbbe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Mixed</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce136a1c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27c1c2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Labrador Retriever</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
    </tr>
    <tr>
        <td>ce136ac6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27c5be-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu-Poodle Mix</td>
        <td>Cross Breed</td>
        <td>None</td>
    </tr>
</table>



Now that we've seen the join syntax, we can begin practicing outer joins. Our Dognition data set will make outer joins more challenging than usual, due to the lack of declared primary keys in the original database, the many-to-many relationships, and the presence of duplicate rows and NULL values in columns we will be using to combine tables.  Mastering outer joins in this challenging context, though, will ensure that you understand the fundamental concepts behind joins, which will be a terrific benefit when you start writing queries in other company databases.

The first query I described above was originally written to address the question of whether dog owners who are particularly surprised by their dog's performance on Dognition tests tend to own similar breeds of dogs. When we designed this query in the last lesson, we wanted to focus on the dog owners who reported the highest average amount of surprise at their dog's performance, and provided at least 10 ratings for one or more of their dogs in the ratings.  We also wanted to examine the breed, breed_type, and breed_group of each of these owner's dogs.  

In examining the query, we learned that:

+ All of the user_guids in the reviews table are in the dogs table
+ Only 389 of the over 5000 dog_guids in the reviews table are in the dogs table

The inner join we executed resulted in 389 rows of output, because it only included the data from rows that have equivalent values in both tables being joined.  But what if we wanted the full list of dogs in the reviews table and an indication of whether or not they were in the dogs table, rather than only a list of review information from dogs in the dogs table?  To achieve this list, we could execute an outer join.  
     
Let's start by using a left outer join to get the list we want. When we use the traditional join syntax to write inner joins, the order you enter the tables in your query doesn't matter.  In outer joins, however, the order matters a lot.  A left outer join will include all of the rows of the table to the left of the LEFT JOIN clause. A right outer join will include all of the rows of the table to the right of the RIGHT JOIN clause.  So in order to retrieve a full list of dogs who completed at least 10 tests in the reviews table, and include as much breed information as possible, we could query:

```mysql
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM reviews r LEFT JOIN dogs d
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE r.dog_guid IS NOT NULL
GROUP BY r.dog_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC;
```

**Question 2: How could you retrieve this same information using a RIGHT JOIN?**



```python
%%sql
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d RIGHT JOIN reviews r
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE r.dog_guid IS NOT NULL
GROUP BY r.dog_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>rDogID</th>
        <th>dDogID</th>
        <th>rUserID</th>
        <th>dUserID</th>
        <th>AvgRating</th>
        <th>NumRatings</th>
        <th>breed</th>
        <th>breed_group</th>
        <th>breed_type</th>
    </tr>
    <tr>
        <td>fdbf39f8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fdbf39f8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce987914-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce987914-7144-11e5-ba71-058fbc01cf0b</td>
        <td>8.0000</td>
        <td>12</td>
        <td>Canaan Dog</td>
        <td>Herding</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>ce47553e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce6ca9ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.8750</td>
        <td>16</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce6f07e6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce7091e2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.5000</td>
        <td>10</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce45ae5a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce67562c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.3529</td>
        <td>17</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce2a68ac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce2a45c0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.1333</td>
        <td>15</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce72be0e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce73f7a6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.0000</td>
        <td>12</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce93d206-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce8be19a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.7273</td>
        <td>11</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce97cd7a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce948926-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.7273</td>
        <td>11</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce7038dc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce71a230-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.6667</td>
        <td>18</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce7dc3da-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce7cbba2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.3750</td>
        <td>16</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
</table>



Notice in the output of both the left and the right version of the outer join, all the rows that had a dog_guid in the reviews table but did NOT have a matching dog_guid in the dogs table have the word "None" entered in output columns related to the dogs table.  "None", in this case, is Jupyter's way of saying the value is NULL.  This becomes clear when you query a list of only the dog_guids that were NOT in the dogs table:

```mysql
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM reviews r LEFT JOIN dogs d
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE d.dog_guid IS NULL
GROUP BY r.dog_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC;
```

**Go ahead and try it yourself (you should get 894 rows in your query):**


```python
%%sql
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM reviews r LEFT JOIN dogs d
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE d.dog_guid IS NULL
GROUP BY r.dog_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>rDogID</th>
        <th>dDogID</th>
        <th>rUserID</th>
        <th>dUserID</th>
        <th>AvgRating</th>
        <th>NumRatings</th>
        <th>breed</th>
        <th>breed_group</th>
        <th>breed_type</th>
    </tr>
    <tr>
        <td>ce47553e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce6ca9ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.8750</td>
        <td>16</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce6f07e6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce7091e2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.5000</td>
        <td>10</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce45ae5a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce67562c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.3529</td>
        <td>17</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce2a68ac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce2a45c0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.1333</td>
        <td>15</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce72be0e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce73f7a6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>7.0000</td>
        <td>12</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce93d206-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce8be19a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.7273</td>
        <td>11</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce97cd7a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce948926-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.7273</td>
        <td>11</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce7038dc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce71a230-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.6667</td>
        <td>18</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce7dc3da-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce7cbba2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.3750</td>
        <td>16</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>ce866fda-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>ce80c12a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>6.3000</td>
        <td>10</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
</table>



<img src="https://duke.box.com/shared/static/p2eucjdttai08eeo7davbpfgqi3zrew0.jpg" width=600 alt="SELECT FROM WHERE" />

**Question 3: How would you use a left join to retrieve a list of all the unique dogs in the dogs table, and retrieve a count of how many tests each one completed? Include the dog_guids and user_guids from the dogs and complete_tests tables in your output. (If you do not limit your query, your output should contain 35050 rows.  HINT: use the dog_guid from the dogs table to group your results.)**


```python
%%sql
SELECT d.user_guid AS dUserID, c.user_guid AS cUserID, d.dog_guid AS dDogID, c.dog_guid AS dDogID, COUNT(test_name)
FROM dogs d LEFT JOIN complete_tests c
ON d.dog_guid = c.dog_guid
GROUP BY d.dog_guid
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>dUserID</th>
        <th>cUserID</th>
        <th>dDogID</th>
        <th>dDogID_1</th>
        <th>COUNT(test_name)</th>
    </tr>
    <tr>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b272-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b272-7144-11e5-ba71-058fbc01cf0b</td>
        <td>21</td>
    </tr>
    <tr>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b5ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b5ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
    </tr>
    <tr>
        <td>ce135ab8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b6b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b6b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce13507c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>11</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>31</td>
    </tr>
    <tr>
        <td>ce13615c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b948-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b948-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27ba1a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27ba1a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>27</td>
    </tr>
    <tr>
        <td>ce1362ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27baec-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>ce135f2c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27bbbe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27bbbe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
    </tr>
    <tr>
        <td>ce13697c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27be84-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>0</td>
    </tr>
</table>



Sometimes you can get so focused on writing your join statement that you don't pay close attention to the fields and tables you put in your other clauses, especially when you are joining a lot of tables.  Often your query will still run successfully, even if you haven't entered the criteria or grouping clause you intended.  The next question will illustrate how easy it is for this to happen.

**Question 4: Repeat the query you ran in Question 3, but intentionally use the dog_guids from the completed_tests table to group your results instead of the dog_guids from the dogs table. (Your output should contain 17987 rows)**


```python
%%sql
SELECT d.user_guid AS dUserID, c.user_guid AS cUserID, d.dog_guid AS dDogID, c.dog_guid AS dDogID, COUNT(test_name)
FROM dogs d LEFT JOIN complete_tests c
ON d.dog_guid = c.dog_guid
GROUP BY c.dog_guid
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>dUserID</th>
        <th>cUserID</th>
        <th>dDogID</th>
        <th>dDogID_1</th>
        <th>COUNT(test_name)</th>
    </tr>
    <tr>
        <td>ce1362ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27baec-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>0</td>
    </tr>
    <tr>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b272-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b272-7144-11e5-ba71-058fbc01cf0b</td>
        <td>21</td>
    </tr>
    <tr>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b5ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b5ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
    </tr>
    <tr>
        <td>ce135ab8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b6b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b6b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2</td>
    </tr>
    <tr>
        <td>ce13507c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>11</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>31</td>
    </tr>
    <tr>
        <td>ce13615c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27b948-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b948-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27ba1a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27ba1a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>27</td>
    </tr>
    <tr>
        <td>ce135f2c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27bbbe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27bbbe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
    </tr>
    <tr>
        <td>ce136a1c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>fd27c1c2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27c1c2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
    </tr>
</table>



This time your query ran successfully, but you retrieved many fewer DogIDs because the GROUP BY clause grouped your results according to the dog_guids in the completed_tests table rather than the dog_guid table.  As a result, even though you implemented your join correctly, all of the dog_guids that were in the dogs table but not in the completed_tests table got rolled up into one row of your output where completed_tests.dogs_guid = NULL.  This is a good opportunity to remind ourselves about the differences between SELECT/GROUP BY and COUNT DISTINCT.  

**Question 5: Write a query using COUNT DISTINCT to determine how many distinct dog_guids there are in the completed_tests table.**


```python
%%sql
SELECT COUNT(DISTINCT dog_guid)
FROM complete_tests;
```

    1 rows affected.





<table>
    <tr>
        <th>COUNT(DISTINCT dog_guid)</th>
    </tr>
    <tr>
        <td>17986</td>
    </tr>
</table>



The result of your COUNT DISTINCT clause should be 17,986 which is one row less than the number of rows you retrieved from your query in Question 4.  That's because COUNT DISTINCT does NOT count NULL values, while SELECT/GROUP BY clauses roll up NULL values into one group.  <mark> If you want to infer the number of distinct entries from the results of a query using joins and GROUP BY clauses, remember to include an "IS NOT NULL" clause to ensure you are not counting NULL values.</mark>
   
These exercises are a good illustration of why it is very helpful to save your queries when you are doing an analysis.  Saving your queries allows you and your team members to double-check your work later.  As you can see, the concepts behind SQL aren't themselves too tricky, but it is easy to make mistakes, especially when your queries get long and more complicated.  

One more situation where joins can cause some confusion is when you have duplicate rows in a table you are joining.  If you ignore what we've discussed about set theory and the way databases compute their joins, the behavior databases exhibit when you have duplicate rows in a joined table will seem utterly baffling.  With this knowledge, though, the behavior will make perfect sense.  Let's walk through what happens.   

**Question 6: We want to extract all of the breed information of every dog a user_guid in the users table owns.  If a user_guid in the users table does not own a dog, we want that information as well.  Write a query that would return this information.  Include the dog_guid from the dogs table, and user_guid from both the users and dogs tables in your output. (HINT: you should get 952557 rows in your output!)**  



```python
%%sql
SELECT u.user_guid AS uUserID, d.user_guid AS dUserID, d.dog_guid AS dDogID, d.breed
FROM users u LEFT JOIN dogs d
ON u.user_guid=d.user_guid
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>uUserID</th>
        <th>dUserID</th>
        <th>dDogID</th>
        <th>breed</th>
    </tr>
    <tr>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b272-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Labrador Retriever</td>
    </tr>
    <tr>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd417cac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Mixed</td>
    </tr>
    <tr>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b5ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shetland Sheepdog</td>
    </tr>
    <tr>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd3fb0f2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shetland Sheepdog</td>
    </tr>
    <tr>
        <td>ce135ab8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135ab8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b6b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Golden Retriever</td>
    </tr>
    <tr>
        <td>ce13507c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce13507c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Golden Retriever</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27ba1a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27e9a4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27ed46-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
    </tr>
</table>



There are only 35050 distinct dog_guids in the dogs table.  Why is the database outputting almost a million rows?  That can't be right.  Let's figure out what is going on.  

**Question 7: Adapt the query you wrote above so that it counts the number of rows the join will output per user_id.  Sort the results by this count in descending order.  Remember that if you include dog_guid or breed fields in this query, they will be randomly populated by only one of the values associated with a user_guid (see MySQL Exercise 6; there should be 33,193 rows in your output).**


```python
%%sql
SELECT u.user_guid AS uUserID, d.user_guid AS dUserID, d.dog_guid AS dDogID, d.breed, count(*) AS numrows
FROM users u LEFT JOIN dogs d
ON u.user_guid=d.user_guid
GROUP BY u.user_guid
ORDER BY numrows DESC
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>uUserID</th>
        <th>dUserID</th>
        <th>dDogID</th>
        <th>breed</th>
        <th>numrows</th>
    </tr>
    <tr>
        <td>ce7b75bc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce7b75bc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd7bfb52-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
        <td>913138</td>
    </tr>
    <tr>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd423714-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
        <td>442</td>
    </tr>
    <tr>
        <td>ce2258a6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce2258a6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd40bd62-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
        <td>320</td>
    </tr>
    <tr>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
        <td>130</td>
    </tr>
    <tr>
        <td>ce29675e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce29675e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd46b014-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Labrador Retriever- Mix</td>
        <td>110</td>
    </tr>
    <tr>
        <td>ce6676d0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce6676d0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd68ed6e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Golden Retriever</td>
        <td>64</td>
    </tr>
    <tr>
        <td>ce7adeea-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce7adeea-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd7b3faa-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Labrador Retriever</td>
        <td>49</td>
    </tr>
    <tr>
        <td>ce47264a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce47264a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd401614-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shih Tzu</td>
        <td>36</td>
    </tr>
    <tr>
        <td>ce8c2d08-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce8c2d08-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fdb54786-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Basenji</td>
        <td>36</td>
    </tr>
    <tr>
        <td>ce66713a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce66713a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>fd68e80a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Puggle</td>
        <td>30</td>
    </tr>
</table>



This query told us that user 'ce7b75bc-7144-11e5-ba71-058fbc01cf0b' would be associated with 913,138 rows in the output of the outer join we designed!  Once again, why?  We are going to work with the second user_guid in the output you just generated, 'ce225842-7144-11e5-ba71-058fbc01cf0b', because it would be associated with 442 output rows, and 442 rows are much easier to work with than 913,138.   

**Question 8: How many rows in the *users* table are associated with user_guid 'ce225842-7144-11e5-ba71-058fbc01cf0b'?**


```python
%%sql
SELECT COUNT(user_guid)
FROM users
WHERE user_guid='ce225842-7144-11e5-ba71-058fbc01cf0b';
```

    1 rows affected.





<table>
    <tr>
        <th>COUNT(user_guid)</th>
    </tr>
    <tr>
        <td>17</td>
    </tr>
</table>



There are 17 entries associated with that user_guid in the users table.  If you examine all the columns in the entries, you will see that the rows are exact duplicates of each other.  That's unfortunate, but also something that can happen in real life data sets, especially those from new companies or governmental agencies.

Ok, now...

**Question 9: Examine all the rows in the *dogs* table that are associated with user_guid 'ce225842-7144-11e5-ba71-058fbc01cf0b'?**


```python
%%sql
SELECT *
FROM dogs
WHERE user_guid='ce225842-7144-11e5-ba71-058fbc01cf0b';
```

    26 rows affected.





<table>
    <tr>
        <th>gender</th>
        <th>birthday</th>
        <th>breed</th>
        <th>weight</th>
        <th>dog_fixed</th>
        <th>dna_tested</th>
        <th>created_at</th>
        <th>updated_at</th>
        <th>dimension</th>
        <th>exclude</th>
        <th>breed_type</th>
        <th>breed_group</th>
        <th>dog_guid</th>
        <th>user_guid</th>
        <th>total_tests_completed</th>
        <th>mean_iti_days</th>
        <th>mean_iti_minutes</th>
        <th>median_iti_days</th>
        <th>median_iti_minutes</th>
        <th>time_diff_between_first_and_last_game_days</th>
        <th>time_diff_between_first_and_last_game_minutes</th>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-03-21 19:30:06</td>
        <td>2015-05-13 18:01:39</td>
        <td>maverick</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd423714-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>31</td>
        <td>26.794221451</td>
        <td>38583.678889</td>
        <td>0.0010474537277</td>
        <td>1.5083333679</td>
        <td>803.82664352</td>
        <td>1157510.3667</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-03-30 19:41:08</td>
        <td>2015-01-06 16:26:41</td>
        <td>stargazer</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd42913c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>27</td>
        <td>16.814351852</td>
        <td>24212.666667</td>
        <td>0.0045775463231</td>
        <td>6.5916667053</td>
        <td>437.17314815</td>
        <td>629529.33333</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-04-08 15:44:08</td>
        <td>2015-09-21 18:05:51</td>
        <td>einstein</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd42ad20-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>22.850757188</td>
        <td>32905.090351</td>
        <td>0.00042824063054</td>
        <td>0.61666650798</td>
        <td>434.16438657</td>
        <td>625196.71667</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-05-03 19:56:17</td>
        <td>2013-07-25 19:42:39</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd443064-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>4</td>
        <td>26.755327932</td>
        <td>38527.672222</td>
        <td>12.042581019</td>
        <td>17341.316667</td>
        <td>80.265983796</td>
        <td>115583.01667</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-05-16 16:03:54</td>
        <td>2014-10-20 18:10:48</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd44ec84-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>6</td>
        <td>16.63131713</td>
        <td>23949.096667</td>
        <td>0.015543981453</td>
        <td>22.383333293</td>
        <td>83.156585648</td>
        <td>119745.48333</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-05-17 16:03:57</td>
        <td>2014-11-05 16:18:29</td>
        <td>stargazer</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd44f68e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.021543006823</td>
        <td>31.021929825</td>
        <td>0.0032175926011</td>
        <td>4.6333333455</td>
        <td>0.40931712964</td>
        <td>589.41666668</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-05-23 12:29:28</td>
        <td>2014-10-06 19:29:11</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd453a40-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9</td>
        <td>69.875772569</td>
        <td>100621.1125</td>
        <td>0.0031770833291</td>
        <td>4.5749999939</td>
        <td>559.00618056</td>
        <td>804968.9</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-07-18 19:16:30</td>
        <td>2014-05-28 14:11:10</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd484488-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>0</td>
        <td>1</td>
        <td>1</td>
        <td>2013-07-30 19:39:37</td>
        <td>2013-07-30 19:39:37</td>
        <td>None</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd492a7e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2</td>
        <td>0.0018981480973</td>
        <td>2.7333332601</td>
        <td>0.0018981480973</td>
        <td>2.7333332601</td>
        <td>0.0018981480973</td>
        <td>2.7333332601</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-08-05 19:50:55</td>
        <td>2014-07-20 15:06:18</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd4a5656-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>10</td>
        <td>6.3504243827</td>
        <td>9144.6111111</td>
        <td>0.00526620378</td>
        <td>7.5833334432</td>
        <td>57.153819444</td>
        <td>82301.5</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>1</td>
        <td>2013-07-18 17:44:47</td>
        <td>2013-10-31 18:32:39</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd48423a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2013-09-24 18:09:10</td>
        <td>2014-05-28 14:09:36</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd544fda-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2012</td>
        <td>Spinone Italiano</td>
        <td>50</td>
        <td>0</td>
        <td>0</td>
        <td>2013-09-29 14:56:10</td>
        <td>2014-06-13 15:13:14</td>
        <td>None</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
        <td>fd5479ec-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2013-10-08 21:24:19</td>
        <td>2014-05-28 14:09:05</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd593e50-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2012</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2013-12-03 18:24:30</td>
        <td>2014-05-28 14:08:56</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd64a36c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2009</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2013-12-19 19:21:14</td>
        <td>2014-05-28 14:09:49</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd65695a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2010</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2014-04-29 14:12:54</td>
        <td>2014-06-13 15:13:15</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd6815f6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>NaN</td>
        <td>#VALUE!</td>
        <td>0</td>
        <td>0</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2014</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2014-05-22 21:05:33</td>
        <td>2014-07-10 13:57:17</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd687dca-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>5</td>
        <td>6.9520717593</td>
        <td>10010.983333</td>
        <td>6.4991608796</td>
        <td>9358.7916667</td>
        <td>27.808287037</td>
        <td>40043.933333</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>None</td>
        <td>2014-06-17 20:15:38</td>
        <td>2014-06-30 22:28:42</td>
        <td>stargazer</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd68d55e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.0022283138427</td>
        <td>3.2087719335</td>
        <td>0.00096064817923</td>
        <td>1.3833333781</td>
        <td>0.042337963011</td>
        <td>60.966666736</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2014</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>None</td>
        <td>None</td>
        <td>2014-07-02 14:04:56</td>
        <td>2014-07-02 14:04:56</td>
        <td>None</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd691474-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3</td>
        <td>154.64907407</td>
        <td>222694.66667</td>
        <td>154.64907407</td>
        <td>222694.66667</td>
        <td>309.29814815</td>
        <td>445389.33333</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2014</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2014-05-07 18:38:59</td>
        <td>2014-05-28 14:06:17</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd682cc6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2009</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2014-05-07 20:11:13</td>
        <td>2014-05-28 14:04:53</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd683252-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2013</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2014-05-07 20:32:33</td>
        <td>2014-05-28 14:03:55</td>
        <td>None</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd68334c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2011</td>
        <td>Akita-Airedale Terrier Mix</td>
        <td>20</td>
        <td>0</td>
        <td>None</td>
        <td>2014-10-06 02:46:31</td>
        <td>2014-10-06 02:46:31</td>
        <td>None</td>
        <td>None</td>
        <td>Cross Breed</td>
        <td>None</td>
        <td>fd6c63b8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2014</td>
        <td>Australian Terrier</td>
        <td>150</td>
        <td>0</td>
        <td>None</td>
        <td>2015-04-13 14:40:48</td>
        <td>2015-04-13 14:40:48</td>
        <td>None</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Terrier</td>
        <td>fd7bccf4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2014</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>None</td>
        <td>2015-04-27 15:29:36</td>
        <td>2015-04-27 15:29:36</td>
        <td>None</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd7c0746-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce225842-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
</table>



You should see there are 26 rows associated with that UserID in the dogs table.  When you examine the dogs table, you see that there are a lot of entries that have "Shih Tzu" in the breed column and "190" in the weight column.  This was Dognition's internal convention for indicating test accounts.  So these dog_guids and user_guids do not represent real data.  Nonetheless, they provide a great example of what happens when you join on fields that have duplicate entries.

Recall the general strategy relational databases use to join tables:

<img src="https://duke.box.com/shared/static/km5c7scvo7u6aexzsy1i73wm28aizme1.jpg" width=400 alt="SELECT FROM WHERE" />


When databases join tables, they output the result of every pair of entries that meet certain criteria in the linking column of one table with the linking column of another table.  Our join statement imposed the criteria that the output should only include pairs whose user_guids matched in the two linking columns.  However, since there were multiple rows that had the same user_guid in the users table, *each one of these rows got paired up with each row in the dogs table that had the same user_guid.*  The result was 442 rows, because 17 (instances of the user_guid in the users table) x 26 (instances of the user_guid in the dogs table) = 442.

Having seen this, perhaps you can now appreciate why some database experts emphasize terminology that differentiates between set *theory* and real database *implementation*.  Database operations, like those that join tables, are based on set theory that assumes there are no duplicate rows in your tables.  In real life, duplicate rows get entered all the time, and it can cause you to misinterpret your queries if you do not understand the consequences.  If you've been impacted enough times by the differences between real and theoretical databases, it makes sense that it would be important to you to use language that clearly distinguishes between theory and real life.

The important things I want you to remember from this example of joins with duplicates are that duplicate rows and table relationships that have table-to-table mappings of greater than 1 have multiplicative effects on your query results, due to the way relational databases combine tables.  If you write queries that aggregate over a lot of joined tables, it can be very difficult to catch issues that output results you don't intend, because the aggregated results will hide clues from you.   To prevent this from happening, I recommend you adopt the following practices:

+ Avoid making assumptions about your data or your analyses.  For example, rather than assume that all the values in a column are unique just because some documentation says they should be, check for yourself!
+ Always look at example outputs of your queries before you strongly interpret aggregate calculations. Take extra care to do this when your queries require joins.  
+ When your queries require multiple layers of functions or joins, examine the output of each layer or join first before you combine them all together. 
+ Adopt a healthy skepticsm of all your data and results.  If you see something you don't expect, make sure you explore it before interpreting it strongly or incorporating it into other analyses.

One more type of join to mention that I discussed in the joins videos is a full outer join. Full outer joins include all of the rows in both tables in an ON clause, regardless of whether there is a value that links the row of one table with a row in the other table.  As with left or right joins, whenever a value in a row does not have a matching value in the joined table, NULLs will be entered for all values in the joined table.

Outer joins are used very rarely.  The most practical application is if you want to export all of your raw data to another program for visualization or analysis. The syntax for outer joins is the same as for inner joins, but you replace the word "inner" with " full outer":

```mysql
SELECT r.dog_guid AS rDogID, d.dog_guid AS dDogID, r.user_guid AS rUserID, d.user_guid AS dUserID, AVG(r.rating) AS AvgRating, COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM reviews r FULL OUTER JOIN dogs d
  ON r.dog_guid=d.dog_guid AND r.user_guid=d.user_guid
WHERE r.dog_guid IS NOT NULL
GROUP BY r.dog_guid
ORDER BY AvgRating DESC;
```

<mark>**HOWEVER!  MySQL does not support full outer joins.**</mark>
   
If you wanted to imitate a full outer join in mySQL, you could follow one of the methods described at this website:

http://www.xaprb.com/blog/2006/05/26/how-to-write-full-outer-join-in-mysql/





## Practice outer joining your own tables!¶

**Question 10: How would you write a query that used a *left* join to return the number of distinct user_guids that were in the users table, but not the dogs table (your query should return a value of 2226)?**


```python
%%sql
SELECT COUNT(DISTINCT u.user_guid)
FROM users u LEFT JOIN dogs d
ON u.user_guid = d.user_guid
WHERE d.user_guid IS NULL;
```

    1 rows affected.





<table>
    <tr>
        <th>COUNT(DISTINCT u.user_guid)</th>
    </tr>
    <tr>
        <td>2226</td>
    </tr>
</table>



**Question 11: How would you write a query that used a *right* join to return the number of distinct user_guids that were in the users table, but not the dogs table (your query should return a value of 2226)?**


```python
%%sql
SELECT COUNT(DISTINCT u.user_guid)
FROM dogs d RIGHT JOIN users u
ON d.user_guid=u.user_guid
WHERE d.user_guid IS NULL;
```

    1 rows affected.





<table>
    <tr>
        <th>COUNT(DISTINCT u.user_guid)</th>
    </tr>
    <tr>
        <td>2226</td>
    </tr>
</table>



**Question 12: Use a left join to create a list of all the unique dog_guids that are contained in the site_activities table, but not the dogs table, and how many times each one is entered.  Note that there are a lot of NULL values in the dog_guid of the site_activities table, so you will want to exclude them from your list.  (Hint: if you exclude null values, the results you get will have two rows with words in their site_activities dog_guid fields instead of real guids, due to mistaken entries)**


```python
%%sql
SELECT s.dog_guid AS SA_dogs_not_present_in_dogs_table, COUNT(*) AS NumEntries
FROM site_activities s LEFT JOIN dogs d
ON s.dog_guid=d.dog_guid
WHERE d.dog_guid IS NULL AND s.dog_guid IS NOT NULL
GROUP BY SA_dogs_not_present_in_dogs_table;
```

    2 rows affected.





<table>
    <tr>
        <th>SA_dogs_not_present_in_dogs_table</th>
        <th>NumEntries</th>
    </tr>
    <tr>
        <td>Membership</td>
        <td>5587</td>
    </tr>
    <tr>
        <td>PortalContent</td>
        <td>12</td>
    </tr>
</table>



**Practice any other outer joins you are interested in here!**


```python

```
