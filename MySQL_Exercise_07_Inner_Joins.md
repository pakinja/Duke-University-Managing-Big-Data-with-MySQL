
Copyright Jana Schaich Borg/Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)

# MySQL Exercise 7: Joining Tables with Inner Joins

Before completing these exercises, I strongly recommend that you watch the video called "What are Joins?" that describe what joins are, and how different types of joins work.

As one of the last building blocks we need to address our Dognition analysis questions, in this lesson we will learn how to combine tables using inner joins.

   
## 1. Inner Joins between 2 tables

To begin, load the sql library, connect to the Dognition database, and set the Dognition database as the default.


```python
%load_ext sql
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
%sql USE dognitiondb
```

    0 rows affected.





    []



Recall that tables in relational databases are linked through primary keys and sometimes other fields that are common to multiple tables (as is the case with our Dognition data set).  Our goal when we execute a JOIN or make a joined table is to use those common columns to let the database figure out which rows in one table match up to which rows in another table.  Once that mapping is established using at least one common field or column, the database can pull any columns you want out of the mapped, or joined, tables and output the matched data to one common table.

An inner join is a join that outputs only rows that have an exact match in both tables being joined:

<img src="https://duke.box.com/shared/static/xazeqtyq6bjo12ojvgxup4bx0e9qcn5d.jpg" width=400 alt="INNER_JOIN" />

To illustrate how this works, let's find out whether dog owners that are particularly surprised by their dog's performance on Dognition tests tend to own similar breeds (or breed types, or breed groups) of dogs.  There are many ways to address this question, but let's start by focusing on the dog owners who provided at least 10 ratings for one or more of their dogs in the ratings table. Of these owners, which 200 owners reported the highest average amount of surprise at their dog's performance, and what was the breed, breed_type, and breed_group of each of these owner's dog?

The surprise ratings are stored in the reviews table.  The dog breed information is provided in the dogs table.  There are two columns that are common to both tables: user_guid and dog_guid. How do we use the common columns to combine information from the two tables? 

To join the tables, you can use a WHERE clause and add a couple of details to the FROM clause so that the database knows from what table each field in your SELECT clause comes.  

First, start by adding all the columns we want to examine to the SELECT statement:

```mySQL
SELECT dog_guid AS DogID, user_guid AS UserID, AVG(rating) AS AvgRating, 
       COUNT(rating) AS NumRatings, breed, breed_group, breed_type
```
then list all the tables from which the fields we are interested in come, separated by commas (with no comma at the end of the list):

```mySQL
FROM dogs, reviews
```
then add the other restrictions:

```mySQL
GROUP BY user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200
```

**Try running this query and see what happens:**




```python
%%sql
SELECT dog_guid AS DogID, user_guid AS UserID, AVG(rating) AS AvgRating, COUNT(rating) AS NumRatings,
breed, breed_group, breed_type
FROM dogs, reviews
GROUP BY user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200;
```

    (_mysql_exceptions.OperationalError) (1052, "Column 'dog_guid' in field list is ambiguous") [SQL: 'SELECT dog_guid AS DogID, user_guid AS UserID, AVG(rating) AS AvgRating, COUNT(rating) AS NumRatings,\nbreed, breed_group, breed_type\nFROM dogs, reviews\nGROUP BY user_guid\nHAVING NumRatings >= 10\nORDER BY AvgRating DESC\nLIMIT 200;']


You should receive an error message stating that the identity of dog_guid and user_guid in the field list is ambiguous. The reason is that the column title exists in both tables, and MySQL doesn't know which one we want. We have to specify the table name before stating the field name, and separate the two names by a period <mark>(NOTE: read this entire section before deciding whether you want to execute this query)<mark\>:

```mySQL
SELECT dogs.dog_guid AS DogID, dogs.user_guid AS UserID, AVG(reviews.rating) AS AvgRating,     
       COUNT(reviews.rating) AS NumRatings, dogs.breed, dogs.breed_group, dogs.breed_type
FROM dogs, reviews
GROUP BY dogs.user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200
```


You can also take advantage of aliases so that you don't have to write out the name of the tables each time.  Here I will introduce another syntax for aliases that omits the AS completely.  In this syntax, the alias is whatever word (or phrase, if you use quotation marks) follows immediately after the field or table name, separated by a space.  So we could write:

```mySQL
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, 
       COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d, reviews r
GROUP BY d.user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200
```

I am tempted to tell you to run this query so that you will see what happens, but instead, I will explain what will happen and let you decide if you want to see what the output looks...and feels...like.  
      
There is nothing built into the database table definitions that can instruct the server how to combine the tables on its own (remember, this is how relational databases save space and remain flexible).  Further, the query as written does not tell the database how the two tables are related.  As a consequence, rather than match up the two tables according to the values in the user_id and/or dog_id column, the database will do the only thing it knows how to do which is output every single combination of the records in the dogs table with the records in the reviews table.  In other words, every single row of the dogs table will get paired with every single row of the reviews table.  This is known as a Cartesian product.  Not only will it be a heavy burden on the database to output a table that has the full length of one table multiplied times the full length of another (and frustrating to you, because the query would take a very long time to run), the output would be close to useless.

To prevent this from happening, tell the database how to relate the tables in the WHERE clause:

```mySQL
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, 
       COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d, reviews r
WHERE d.dog_guid=r.dog_guid
GROUP BY d.user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200
```

To be very careful and exclude any incorrect dog_guid or user_guid entries, you can include both shared columns in the WHERE clause:

```mySQL
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, 
       COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d, reviews r
WHERE d.dog_guid=r.dog_guid AND d.user_guid=r.user_guid
GROUP BY d.user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200
```

**Try running this query now:**


```python
%%sql
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, 
       COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d, reviews r
WHERE d.dog_guid=r.dog_guid AND d.user_guid=r.user_guid
GROUP BY d.user_guid
HAVING NumRatings >= 10
ORDER BY AvgRating DESC
LIMIT 200;
```

    38 rows affected.





<table>
    <tr>
        <th>DogID</th>
        <th>UserID</th>
        <th>AvgRating</th>
        <th>NumRatings</th>
        <th>breed</th>
        <th>breed_group</th>
        <th>breed_type</th>
    </tr>
    <tr>
        <td>fdbf39f8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce987914-7144-11e5-ba71-058fbc01cf0b</td>
        <td>8.0000</td>
        <td>12</td>
        <td>Canaan Dog</td>
        <td>Herding</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc09a82-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce99bb12-7144-11e5-ba71-058fbc01cf0b</td>
        <td>5.0000</td>
        <td>10</td>
        <td>Golden Doodle</td>
        <td>None</td>
        <td>Popular Hybrid</td>
    </tr>
    <tr>
        <td>fdbef330-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce984d2c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>4.5385</td>
        <td>13</td>
        <td>Havanese</td>
        <td>Toy</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc0518a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce99661c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3.7333</td>
        <td>15</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fd684cf6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce473856-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3.7222</td>
        <td>18</td>
        <td>Labrador Retriever</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbf66e4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98b9b0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3.5000</td>
        <td>14</td>
        <td>Golden Retriever</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc09b0e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce99bb76-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3.2308</td>
        <td>13</td>
        <td>Golden Doodle</td>
        <td>None</td>
        <td>Popular Hybrid</td>
    </tr>
    <tr>
        <td>fdbeedc2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9847dc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3.1538</td>
        <td>13</td>
        <td>Eurasier</td>
        <td>None</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc180be-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9a7110-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3.0625</td>
        <td>16</td>
        <td>Chihuahua-Dachshund Mix</td>
        <td>None</td>
        <td>Cross Breed</td>
    </tr>
    <tr>
        <td>fd6aabd6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce6cd804-7144-11e5-ba71-058fbc01cf0b</td>
        <td>3.0000</td>
        <td>13</td>
        <td>American Pit Bull Terrier</td>
        <td>None</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbfc0b2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98f3bc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.6923</td>
        <td>13</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdbf96dc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98ce0a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.5714</td>
        <td>14</td>
        <td>American Pit Bull Terrier-American Staffordshire Terrier Mix</td>
        <td>None</td>
        <td>Cross Breed</td>
    </tr>
    <tr>
        <td>fdbedef4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce982e46-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.4615</td>
        <td>13</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdbec8ce-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce980e3e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.4545</td>
        <td>11</td>
        <td>Basset Hound</td>
        <td>Hound</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbeff06-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9856f0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.4000</td>
        <td>10</td>
        <td>Bearded Collie</td>
        <td>Herding</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbed7ce-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce981dc0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.3333</td>
        <td>18</td>
        <td>Havanese</td>
        <td>Toy</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc1c31c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9ac674-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.3077</td>
        <td>13</td>
        <td>Labrador Retriever</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc1a224-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9ab1f2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.2857</td>
        <td>14</td>
        <td>Other</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdc0c49e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce99d674-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.2778</td>
        <td>18</td>
        <td>Other</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdc19b30-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9aab30-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.2222</td>
        <td>18</td>
        <td>Brittany</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbedf94-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce982ed2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.0000</td>
        <td>11</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdc0c502-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce99d732-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2.0000</td>
        <td>15</td>
        <td>Golden Retriever</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fd7104ae-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce7212a6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.9000</td>
        <td>10</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdbfddfe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce99049c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.8000</td>
        <td>40</td>
        <td>Irish Water Spaniel</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc198d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9a9500-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.8000</td>
        <td>10</td>
        <td>German Shepherd Dog</td>
        <td>Herding</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbe923c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce97f926-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.7647</td>
        <td>17</td>
        <td>Beagle</td>
        <td>Hound</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fd6a7490-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce6cf3fc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.7500</td>
        <td>20</td>
        <td>Golden Retriever</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbef5e2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce984f70-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.7000</td>
        <td>10</td>
        <td>Havanese</td>
        <td>Toy</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbf9f10-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98d6de-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.6667</td>
        <td>18</td>
        <td>Australian Cattle Dog-Border Collie Mix</td>
        <td>None</td>
        <td>Cross Breed</td>
    </tr>
    <tr>
        <td>fdbea952-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9805ce-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.6667</td>
        <td>15</td>
        <td>Golden Doodle</td>
        <td>None</td>
        <td>Popular Hybrid</td>
    </tr>
    <tr>
        <td>fdbf686a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98bca8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.6000</td>
        <td>10</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdbf6144-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98b776-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.5455</td>
        <td>11</td>
        <td>American Pit Bull Terrier-Labrador Retriever Mix</td>
        <td>None</td>
        <td>Cross Breed</td>
    </tr>
    <tr>
        <td>fdc1edec-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9ae4b0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.5333</td>
        <td>15</td>
        <td>Poodle</td>
        <td>Non-Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbe7cac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce97e10c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.3333</td>
        <td>12</td>
        <td>Labrador Retriever</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbe8a9e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce97f1f6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>1.2500</td>
        <td>16</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdbf0456-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce985d9e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>0.7500</td>
        <td>16</td>
        <td>English Springer Spaniel</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc23ba8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9b306e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>0.4211</td>
        <td>19</td>
        <td>Cockapoo</td>
        <td>None</td>
        <td>Popular Hybrid</td>
    </tr>
    <tr>
        <td>fdbec040-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98098e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>0.4000</td>
        <td>10</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
</table>



The query should execute quickly.  This would NOT have been the case if you did not include the WHERE clause to combine the two tables. If you accidentally request a Cartesian product from datasets with billions of rows, you could be waiting for your query output for days (and will probably get in trouble with your database administrator).  <mark>So always remember to tell the database how to join your tables!</mark>

Let's examine our joined table a bit further.  The joined table outputted by the query above should have 38 rows, despite the fact that we set our LIMIT at 200.  The reason for this is that it turns out that a relatively small number of customers provided 10 or more reviews.  If you remove the HAVING and LIMIT BY clause from the query, you should end up with 389 rows.  **Go ahead and try it:**



```python
%%sql
SELECT d.dog_guid AS DogID, d.user_guid AS UserID, AVG(r.rating) AS AvgRating, 
       COUNT(r.rating) AS NumRatings, d.breed, d.breed_group, d.breed_type
FROM dogs d, reviews r
WHERE d.dog_guid=r.dog_guid AND d.user_guid=r.user_guid
GROUP BY d.user_guid
ORDER BY AvgRating DESC
LIMIT 10;
```

    10 rows affected.





<table>
    <tr>
        <th>DogID</th>
        <th>UserID</th>
        <th>AvgRating</th>
        <th>NumRatings</th>
        <th>breed</th>
        <th>breed_group</th>
        <th>breed_type</th>
    </tr>
    <tr>
        <td>fdc059fa-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce996dc4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Irish Water Spaniel</td>
        <td>Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc19554-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9a8a24-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Miniature Pinscher</td>
        <td>Toy</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc147ac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9a545a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Tibetan Terrier</td>
        <td>Non-Sporting</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc121dc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce9a310a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdbedd14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce975994-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdc1c880-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce96c768-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Maltese</td>
        <td>Toy</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbf178e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce986546-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Airedale Terrier</td>
        <td>Terrier</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdbf94de-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce98cc34-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>2</td>
        <td>Pembroke Welsh Corgi</td>
        <td>Herding</td>
        <td>Pure Breed</td>
    </tr>
    <tr>
        <td>fdc019c2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce99489e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
    <tr>
        <td>fdc057ac-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce996b9e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>9.0000</td>
        <td>1</td>
        <td>Mixed</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
    </tr>
</table>



It's clear from looking at this output that (A) not many customers provided ratings, and (B) when they did, they usually were not very surprised by their dog's performance.  Therefore, these ratings are probably not going to provide a lot of instructive insight into how to improve Dognition's completion rate.  However, the ratings table still provides a great opportunity to illustrate the results of different types of joins.  

To help prepare us for this:

**Questions 1-4:  How many unique dog_guids and user_guids are there in the reviews and dogs table independently?**  


```python
%%sql
SELECT COUNT(DISTINCT dog_guid) AS DOGS_R
FROM reviews;
```

    1 rows affected.





<table>
    <tr>
        <th>DOGS_R</th>
    </tr>
    <tr>
        <td>5991</td>
    </tr>
</table>




```python
%%sql
SELECT COUNT(DISTINCT user_guid) AS USERS_R
FROM reviews;
```

    1 rows affected.





<table>
    <tr>
        <th>USERS_R</th>
    </tr>
    <tr>
        <td>5586</td>
    </tr>
</table>




```python
%%sql
SELECT COUNT(DISTINCT dog_guid) AS DOGS_D
FROM dogs;
```

    1 rows affected.





<table>
    <tr>
        <th>DOGS_D</th>
    </tr>
    <tr>
        <td>35050</td>
    </tr>
</table>




```python
%%sql
SELECT COUNT(DISTINCT user_guid) AS DOGS_D
FROM dogs;
```

    1 rows affected.





<table>
    <tr>
        <th>DOGS_D</th>
    </tr>
    <tr>
        <td>30967</td>
    </tr>
</table>



These counts indicate some important things:

+ Many customers in both the reviews and the dogs table have multiple dogs
+ There are many more unique dog_guids and user_guids in the dogs table than the reviews table
+ There are many more unique dog_guids and user_guids in the reviews table than in the output of our inner join

Let's test one more thing.  
   
**Try the inner join query once with just the dog_guid or once with just the user_guid clause in the WHERE statement:**




```python
%%sql
SELECT COUNT(DISTINCT dog_guid) AS DOGS_R, COUNT(DISTINCT dog_guid) AS DOGS_D
FROM reviews.DOGS_R, dogs.DOGS_D;
```

    (_mysql_exceptions.OperationalError) (1142, "SELECT command denied to user 'studentuser'@'172.17.0.107' for table 'DOGS_R'") [SQL: 'SELECT COUNT(DISTINCT dog_guid) AS DOGS_R, COUNT(DISTINCT dog_guid) AS DOGS_D\nFROM reviews.DOGS_R, dogs.DOGS_D;']



```python

```

When you run the query by joining on the dog_guid only, you still get 389 rows in your output.  When you run the query by joining on the user_guid only, you get 5586 rows in your output.  This means that:

+ All of the user_guids in the reviews table are in the dogs table
+ Only 389 of the over 5000 dog_guids in the reviews table are in the dogs table

Perhaps most importantly for our current purposes, these COUNT queries show you that <mark>*inner joins only output the data from rows that have equivalent values in both tables being joined*.</mark>  If you wanted to include all the dog_guids or user_guids in one or both of the tables, you would have to use an outer join, which we will practice in the next lesson.

Try an inner join on your own.  
   

**Question 5: How would you extract the user_guid, dog_guid, breed, breed_type, and breed_group for all animals who completed the "Yawn Warm-up" game (you should get 20,845 rows if you join on dog_guid only)?**


```python

```

## 2. Joining More than 2 Tables

In theory, you can join as many tables together as you want or need.  To join multiple tables you take the same approach as we took when we were joining two tables together: list all the fields you want to extract in the SELECT statement, specify which table they came from in the SELECT statement, list all the tables from which you will need to extract the fields in the FROM statement, and then tell the database how to connect the tables in the WHERE statement.

To extract the user_guid, user's state of residence, user's zip code, dog_guid, breed, breed_type, and breed_group for all animals who completed the "Yawn Warm-up" game, you might be tempted to query:

```mySQL
SELECT c.user_guid AS UserID, u.state, u.zip, d.dog_guid AS DogID, d.breed, d.breed_type, d.breed_group
FROM dogs d, complete_tests c, users u
WHERE d.dog_guid=c.dog_guid 
   AND c.user_guid=u.user_guid
   AND c.test_name="Yawn Warm-up";
```
This query focuses the relationships primarily on the complete_tests table.  However, it turns out that our Dognition dataset has only NULL values in the user_guid column of the complete_tests table.  If you were to execute the query above, you would not get an error message, but your output would have 0 rows.  However, the power of relational databases will come in handy here.  You can use the dogs table to link the complete_tests and users table (pay attention to the difference between the WHERE statement in this query vs. the WHERE statement in the query above):

```mySQL
SELECT d.user_guid AS UserID, u.state, u.zip, d.dog_guid AS DogID, d.breed, d.breed_type, d.breed_group
FROM dogs d, complete_tests c, users u
WHERE d.dog_guid=c.dog_guid 
   AND d.user_guid=u.user_guid
   AND c.test_name="Yawn Warm-up";
```

Of note, joins are very resource intensive, so try not to join unnecessarily.  In general, the more joins you have to execute, the slower your query performance will be.

**Question 6: How would you extract the user_guid, membership_type, and dog_guid of all the golden retrievers who completed at least 1 Dognition test (you should get 711 rows)?**


```python

```

## Practice inner joining your own tables!

**Question 7: How many unique Golden Retrievers who live in North Carolina are there in the Dognition database (you should get 30)?**




```python

```

**Question 8: How many unique customers within each membership type provided reviews (there should be 3208 in the membership type with the greatest number of customers, and 18 in the membership type with the fewest number of customers)?**


```python

```

**Question 9: For which 3 dog breeds do we have the greatest amount of site_activity data, (as defined by non-NULL values in script_detail_id)(your answers should be "Mixed", "Labrador Retriever", and "Labrador Retriever-Golden Retriever Mix"?**


```python

```

**Practice any other inner joins you would like to try here!**


```python

```
