
Copyright Jana Schaich Borg/Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)

# MySQL Exercise 1: Welcome to your first notebook!

Database interfaces vary greatly across platforms and companies.  The interface you will be using here, called Jupyter, is a web application designed for data science teams who need to create and share documents that share live code.  We will be taking advantage of Jupyter's ability to integrate instructional text with areas that allow you to write and run SQL queries.  In this course, you will practice writing queries about the Dognition data set in these Jupyter notebooks as I give you step-by-step instructions through written text.  Then once you are comfortable with the query syntax, you will practice writing queries about the Dillard's data set in Teradata Viewpoint's more traditional SQL interface, called SQL scratchpad. Your assessments each week will be based on the exercises you complete using the Dillard's dataset.

Jupyter runs in a language called Python, so some of the commands you will run in these MySQL exercises will require incorporating small amounts of Python code, along with the MySQL query itself.  Python is a very popular programming language with many statistical and visualization libraries, so you will likely encounter it in other business analysis settings.  Since many data analysis projects do not use Python, however, I will point out to you what parts of the commands are specific to Python interfaces. 

## The first thing you should do every time you start working with a database: load the SQL library

Since Jupyter is run through Python, the first thing you need to do to start practicing SQL queries is load the SQL library.  To do this, type the following line of code into the empty cell below:

```python
%load_ext sql
```



```python
%load_ext sql
```


The "%" in this line of code is syntax for Python, not SQL.  The "cell" I am referring to is the empty box area beside the "In [  ]:" you see on the left side of the screen.

Once you've entered the line of code, press the "run" button on the Jupyter toolbar that looks like an arrow.  It's located under “cell” in the drop-down menu bar, and next to the stop button (the solid square).  The run button is outlined in red in this picture:

![alt text](https://duke.box.com/shared/static/u9chww13kim30t5p6ndh404d9ap6auwf.jpg)

**TIP: Whenever instructions say “run” or "execute" a command in future exercises, type the appropriate code into the empty cell and execute it by pressing this same button with the arrow.**

When the library has loaded, you will see a number between the brackets that preceed the line of code you executed: 

For example, it might look like this: 

```
In [2]:
```
    

## The second thing you must do every time you want to start working with a database: connect to the database you need to use.

Now that the SQL library is loaded, we need to connect to a database.  The following command will log you into the MySQL server at mysqlserver as the user 'studentuser' and will select the database named 'dognitiondb' :

```python
mysql://studentuser:studentpw@mysqlserver/dognitiondb
```

However, to execute this command using SQL language instead of Python, you will need to begin the line of code with:

```python
%sql
```

Thus, the complete line of code is:

```python
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
```

Connect to the database by typing this command into the cell below, and running it (note: you can copy and paste the command from above rather than typing it, if you choose):




```python
%sql mysql://studentuser:studentpw@mysqlserver/dognitiondb
```




    'Connected: studentuser@dognitiondb'



<mark>***Every time you run a line of SQL code in Jupyter, you will need to preface the line with "%sql".  Remember to do this, even though I will not explicitly instruct you to do so for the rest of the exercises in this course.***</mark>

Once you are connected, the output cell (which reads "Out" followed by brackets) will read: "Connected:studentuser@dognitiondb".  To make this the default database for our queries, run this "USE" command:

```python
%sql USE dognitiondb
```








```python
%sql USE dognitiondb
```

    0 rows affected.





    []



You are now ready to run queries in the Dognition database!


## The third thing you should do every time you start working with a new database: get to know your data

The data sets you will be working with in business settings will be big.  REALLY big.  If you just start making queries without knowing what you are pulling out, you could hang up your servers or be staring at your computer for hours before you get an output.  Therefore, even if you are given an ER diagram or relational schema like we learned about in the first week of the course, before you start querying I strongly recommend that you (1) confirm how many tables each database has, and (2) identify the fields contained in each table of the database.  To determine how many tables each database has, use the SHOW command:

```mySQL
SHOW tables
```

**Try it yourself (TIP: if you get an error message, it's probably because you forgot to start the query with "%sql"):**


```python
%sql SHOW tables
```

    6 rows affected.





<table>
    <tr>
        <th>Tables_in_dognitiondb</th>
    </tr>
    <tr>
        <td>complete_tests</td>
    </tr>
    <tr>
        <td>dogs</td>
    </tr>
    <tr>
        <td>exam_answers</td>
    </tr>
    <tr>
        <td>reviews</td>
    </tr>
    <tr>
        <td>site_activities</td>
    </tr>
    <tr>
        <td>users</td>
    </tr>
</table>



The output that appears above should show you there are six tables in the Dognition database.  To determine what columns or fields (we will use those terms interchangeably in this course) are in each table, you can use the SHOW command again, but this time (1) you have to clarify that you want to see columns instead of tables, and (2) you have to specify from which table you want to examine the columns. 

The syntax, which sounds very similar to what you would actually say in the spoken English language, looks like this:

```mySQL
SHOW columns FROM (enter table name here)
```
or if you have multiple databases loaded:
```mySQL
SHOW columns FROM (enter table name here) FROM (enter database name here)
```
or
```mySQL
SHOW columns FROM databasename.tablename 
```
<mark> Whenever you have multiple databases loaded, you will need to specify which database a table comes from using one of the syntax options described above.</mark>

As I said in the earlier "Introduction to Query Syntax" video, it makes it easier to read and troubleshoot your queries if you always write SQL keywords in UPPERCASE format and write your table and field names in their native format.  We will only use the most important SQL keywords in this course, but a full list can be found here:

https://dev.mysql.com/doc/refman/5.5/en/keywords.html


**Question 1: How many columns does the "dogs" table have?  Enter the appropriate query below to find out:**



```python
%sql SHOW columns FROM dogs
```

    21 rows affected.





<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>gender</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>birthday</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>breed</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>weight</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_fixed</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dna_tested</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dimension</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>exclude</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>breed_type</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>breed_group</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>total_tests_completed</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>mean_iti_days</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>mean_iti_minutes</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>median_iti_days</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>median_iti_minutes</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>time_diff_between_first_and_last_game_days</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>time_diff_between_first_and_last_game_minutes</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>



You should have determined that the "dogs" table has 21 columns.  

An alternate way to learn the same information would be to use the DESCRIBE function.  The syntax is:

```mySQL
DESCRIBE tablename
```

**Question 2: Try using the DESCRIBE function to learn how many columns are in the "reviews" table:**


```python
%sql DESCRIBE reviews
```

    7 rows affected.





<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>rating</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subcategory_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>



You should have determined that there are 7 columns in the "reviews" table.  

The SHOW and DESCRIBE functions give a lot more information about the table than just how many columns, or fields, there are.  Indeed, the first column of the output shows the title of each field in the table.  The column next to that describes what type of data are stored in that column.  There are 3 main types of data in MySQL: text, number, and datetime.  There are many subtypes of data within these three general categories, as described here:

http://support.hostgator.com/articles/specialized-help/technical/phpmyadmin/mysql-variable-types

The next column in the SHOW/DESCRIBE output indicates whether null values can be stored in the field in the table.  The "Key" column of the output provides the following information about each field of data in the table being described (see https://dev.mysql.com/doc/refman/5.6/en/show-columns.html for more information): 

+ Empty: the column either is not indexed or is indexed only as a secondary column in a multiple-column, nonunique index.
+ PRI: the column is a PRIMARY KEY or is one of the columns in a multiple-column PRIMARY KEY.
+ UNI: the column is the first column of a UNIQUE index. 
+ MUL: the column is the first column of a nonunique index in which multiple occurrences of a given value are permitted within the column.

The "Default" field of the output indicates the default value that is assigned to the field. The "Extra" field contains any additional information that is available about a given field in that table. 

**Questions 3-6: In the cells below, examine the fields in the other 4 tables of the Dognition database:**


```python
%sql DESCRIBE complete_tests
```

    6 rows affected.





<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subcategory_name</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>




```python
%sql DESCRIBE exam_answers
```

    8 rows affected.





<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>script_detail_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subcategory_name</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>step_type</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>start_time</td>
        <td>datetime</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>end_time</td>
        <td>datetime</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>loop_number</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(60)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>




```python
%sql DESCRIBE site_activities
```

    11 rows affected.





<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>activity_type</td>
        <td>varchar(150)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>description</td>
        <td>text</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>membership_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>category_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>script_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>script_detail_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>test_name</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>dog_guid</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
</table>




```python
%sql DESCRIBE users
```

    16 rows affected.





<table>
    <tr>
        <th>Field</th>
        <th>Type</th>
        <th>Null</th>
        <th>Key</th>
        <th>Default</th>
        <th>Extra</th>
    </tr>
    <tr>
        <td>sign_in_count</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>0</td>
        <td></td>
    </tr>
    <tr>
        <td>created_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>updated_at</td>
        <td>datetime</td>
        <td>NO</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>max_dogs</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>0</td>
        <td></td>
    </tr>
    <tr>
        <td>membership_id</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>subscribed</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>0</td>
        <td></td>
    </tr>
    <tr>
        <td>exclude</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>free_start_user</td>
        <td>tinyint(1)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>last_active_at</td>
        <td>datetime</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>membership_type</td>
        <td>int(11)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>user_guid</td>
        <td>text</td>
        <td>YES</td>
        <td>MUL</td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>city</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>state</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>zip</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>country</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
    <tr>
        <td>utc_correction</td>
        <td>varchar(255)</td>
        <td>YES</td>
        <td></td>
        <td>None</td>
        <td></td>
    </tr>
</table>



As you examine the fields in each table, you will notice that none of the Dognition tables have primary keys declared.  However, take note of which fields say "MUL" in the "Key" column of the DESCRIBE output, because these columns can still be used to link tables together.  An important thing to keep in mind, though, is that because these linking columns were not configured as primary keys, it is possible the linking fields contain NULL values or duplicate rows.
    
If you do not have a ER diagram or relational schema of the Dognition database yet, consider making one at this point, because you will need to refer back to table and column names constantly throughout designing your queries  (if you don't remember what primary keys, secondary keys, ER diagrams, or relational schemas are, review the material discussed in the first week of this course).  Some database interfaces do provide notes or visual representations about the information in each table for easy reference, but since the Jupyter interface does not provide this, take your own notes and make your own diagrams, and keep them handy.  As you will see, these diagrams and notes will save you a lot of time when you design your queries to pull data.


## Using SELECT to look at your raw data

Once you have an idea of what is in your tables look like and how they might interact, it's a good idea to look at some of the raw data itself so that you are aware of any anomalies that could pose problems for your analysis or interpretations.  To do that, we will use arguably the most important SQL statement for analysts: the SELECT statement.

SELECT is used anytime you want to retrieve data from a table.  In order to retrieve that data, you always have to provide at least two pieces of information: 
   
    (1) what you want to select, and      
    (2) from where you want to select it.  
    
I recommend that you always format your SQL code to ensure that these two pieces of information are on separate lines, so they are easy to identify quickly by eye.  

The skeleton of a SELECT statement looks like this:

```mySQL
SELECT
FROM
```

To fill in the statement, you indicate the column names you are interested in after "SELECT" and the table name (and database name, if you have multiple databases loaded) you are drawing the information from after "FROM."  So in order to look at the breeds in the dogs table, you would execute the following command:

```mySQL
SELECT breed
FROM dogs;
```

Remember:
+ SQL syntax and keywords are case insensitive.  I recommend that you always enter SQL keywords in upper case and table or column names in either lower case or their native format to make it easy to read and troubleshoot your code, but it is not a requirement to do so.  Table or column names are often case insensitive as well, but defaults may vary across database platforms so it's always a good idea to check.
+ Table or column names with spaces in them need to be surrounded by quotation marks in SQL.  MySQL accepts both double and single quotation marks, but some database systems only accept single quotation marks.  In all database systems, if a table or column name contains an SQL keyword, the name must be enclosed in backticks instead of quotation marks.
> ` 'the marks that surrounds this phrase are single quotation marks' `     
> ` "the marks that surrounds this phrase are double quotation marks" `     
> `` `the marks that surround this phrase are backticks` ``
+ The semi-colon at the end of a query is only required when you have multiple separate queries saved in the same text file or editor.  That said, I recommend that you make it a habit to always include a semi-colon at the end of your queries.  

<mark>An important note for executing queries in Jupyter: in order to tell Python that you want to execute SQL language on multiple lines, you must include two percent signs in front of the SQL prefix instead of one.</mark>  Therefore, to execute the above query, you should enter:

```mySQL
%%sql
SELECT breed
FROM dogs;
```

When Jupyter is busy executing a query, you will see an asterisk in the brackets next to the output field:

```
Out [*]
```

When the query is completed, you will see a number in the brackets next to the output field.

```
Out [5]
```

**Try it yourself:**


```python
%%sql
SELECT breed
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
        <td>Golden Retriever</td>
    </tr>
    <tr>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>Siberian Husky</td>
    </tr>
    <tr>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>Mixed</td>
    </tr>
    <tr>
        <td>Labrador Retriever</td>
    </tr>
    <tr>
        <td>Shih Tzu-Poodle Mix</td>
    </tr>
</table>



When you do so, you will see a line at the top of the output panel that says "35050 rows affected".  This means that there are 35050 rows of data in the dogs table.  Each row of the output lists the name of the breed of the dog represented by that entry.  Notice that some breed names are listed multiple times, because several dogs of that breed have participated in the Dognition tests.

If you scroll all the way down to the bottom of the output, you will see a notification that says "35050 rows, truncated to displaylimit of 1000."  We have set up a display limit of 1000 rows in these notebooks to ensure that our database servers are not overloaded, and to reduce the amount of time you have to wait for the query output. However, in a actual scenario these limits would not necessarily be in place for you.  Therefore, before we go any further, I want to show you how you could restrict the number of rows outputted by a query.

![Alt text](https://duke.box.com/shared/static/dm85qcbpdsza8tc7be8x7tc6x6tr2xjq.jpg)



## Using LIMIT to restrict the number of rows in your output (and prevent system crashes)

The MySQL clause you should use is called LIMIT, and it is always placed at the very end of your query.  The simplest version of a limit statement looks like this:

```mySQL
SELECT breed
FROM dogs LIMIT 5;
```

The "5" in this case indicates that you will only see the first 5 rows of data you select.  

**Question 7: In the next cell, try entering a query that will let you see the first 10 rows of the breed column in the dogs table.**




```python
%%sql
SELECT breed
FROM dogs LIMIT 10;
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
        <td>Golden Retriever</td>
    </tr>
    <tr>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>Siberian Husky</td>
    </tr>
    <tr>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>Mixed</td>
    </tr>
    <tr>
        <td>Labrador Retriever</td>
    </tr>
    <tr>
        <td>Shih Tzu-Poodle Mix</td>
    </tr>
</table>



You can also select rows of data from different parts of the output table, rather than always just starting at the beginning.  To do this, use the OFFSET clause after LIMIT. The number after the OFFSET clause indicates from which row the output will begin querying.  Note that the offset of Row 1 of a table is actually 0.  Therefore, in the following query:

```mySQL
SELECT breed
FROM dogs LIMIT 10 OFFSET 5;
```

10 rows of data will be returned, starting at Row 6.  

An alternative way to write the OFFSET clause in the query is:

```mySQL
SELECT breed
FROM dogs LIMIT 5, 10;
```

In this notation, the offset is the number before the comma, and the number of rows returned is the number after the comma.  

**Try it yourself:**


```python
%%sql
SELECT breed
FROM dogs LIMIT 10 OFFSET 5;
```

    10 rows affected.





<table>
    <tr>
        <th>breed</th>
    </tr>
    <tr>
        <td>Siberian Husky</td>
    </tr>
    <tr>
        <td>Shih Tzu</td>
    </tr>
    <tr>
        <td>Mixed</td>
    </tr>
    <tr>
        <td>Labrador Retriever</td>
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
    <tr>
        <td>Boxer</td>
    </tr>
    <tr>
        <td>German Shepherd Dog-Nova Scotia Duck Tolling Retriever Mix</td>
    </tr>
</table>



The LIMIT command is one of the pieces of syntax that can vary across database platforms.  MySQL uses LIMIT to restrict the output, but other databases including Teradata use a statement called "TOP" instead.  Oracle has yet another syntax:

http://www.tutorialspoint.com/sql/sql-top-clause.htm

Make sure to look up the correct syntax for the database type you are using.


## Using SELECT to query multiple columns

Now that we know how to limit our output, we are ready to make our SELECT statement work a little harder.  The SELECT statement can be used to select multiple columns as well as a single column.  The output of the query will depend on the order of the columns you enter after the SELECT statement in your query.  <mark> When you enter column names, separate each name with a comma, but do NOT include a comma after the last column name. </mark>

**Try the following query with different orders of the column names to observe the differences in output (I will include a LIMIT statement in many of the examples I use in the rest of the course, but feel free to change or remove them to explore different aspects of the data):**

```mySQL
SELECT breed, breed_type, breed_group
FROM dogs LIMIT 5, 10;
```




```python
%%sql
SELECT breed_type, breed, breed_group
FROM dogs LIMIT 5, 10;
```

    10 rows affected.





<table>
    <tr>
        <th>breed_type</th>
        <th>breed</th>
        <th>breed_group</th>
    </tr>
    <tr>
        <td>Pure Breed</td>
        <td>Siberian Husky</td>
        <td>Working</td>
    </tr>
    <tr>
        <td>Pure Breed</td>
        <td>Shih Tzu</td>
        <td>Toy</td>
    </tr>
    <tr>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
        <td>Mixed</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Pure Breed</td>
        <td>Labrador Retriever</td>
        <td>Sporting</td>
    </tr>
    <tr>
        <td>Cross Breed</td>
        <td>Shih Tzu-Poodle Mix</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Cross Breed</td>
        <td>German Shepherd Dog-Pembroke Welsh Corgi Mix</td>
        <td>None</td>
    </tr>
    <tr>
        <td>Pure Breed</td>
        <td>Vizsla</td>
        <td>Sporting</td>
    </tr>
    <tr>
        <td>Pure Breed</td>
        <td>Pug</td>
        <td>Toy</td>
    </tr>
    <tr>
        <td>Pure Breed</td>
        <td>Boxer</td>
        <td>Working</td>
    </tr>
    <tr>
        <td>Cross Breed</td>
        <td>German Shepherd Dog-Nova Scotia Duck Tolling Retriever Mix</td>
        <td>None</td>
    </tr>
</table>



Another trick to know about when using SELECT is that <mark> you can use an asterisk as a "wild card" to return all the data in a table.</mark>  (A wild card is defined as a character that will represent or match any character or sequence of characters in a query.) <mark> Take note, this is very risky to do if you do not limit your output or if you don't know how many data are in your database, so use the wild card with caution.</mark>  However, it is a handy tool to use when you don't have all the column names easily available or when you know you want to query an entire table.  

The syntax is as follows:

```mySQL
SELECT *
FROM dogs LIMIT 5, 10;
```

**Question 8: Try using the wild card to query the reviews table:**




```python
%%sql
SELECT *
FROM dogs LIMIT 5, 10;
```

    10 rows affected.





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
        <td>2011</td>
        <td>Siberian Husky</td>
        <td>60</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 18:14:14</td>
        <td>2013-07-25 19:41:49</td>
        <td>stargazer</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Working</td>
        <td>fd27b948-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce13615c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.1785873538</td>
        <td>257.16578947</td>
        <td>0.0035648148035</td>
        <td>5.1333333171</td>
        <td>3.3931597222</td>
        <td>4886.15</td>
    </tr>
    <tr>
        <td>male</td>
        <td>1982</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 18:16:24</td>
        <td>2014-05-30 15:52:54</td>
        <td>maverick</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd27ba1a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>27</td>
        <td>6.1905898326</td>
        <td>8914.449359</td>
        <td>0.00033564807185</td>
        <td>0.48333322347</td>
        <td>160.95533565</td>
        <td>231775.68333</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2012</td>
        <td>Mixed</td>
        <td>50</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 18:44:02</td>
        <td>2013-07-25 19:41:49</td>
        <td>protodog</td>
        <td>None</td>
        <td>Mixed Breed/ Other/ I Don&#x27;t Know</td>
        <td>None</td>
        <td>fd27bbbe-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135f2c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.0080750487303</td>
        <td>11.628070172</td>
        <td>0.0046412037941</td>
        <td>6.6833334635</td>
        <td>0.15342592588</td>
        <td>220.93333326</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2008</td>
        <td>Labrador Retriever</td>
        <td>70</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 20:59:42</td>
        <td>2013-07-25 19:41:49</td>
        <td>einstein</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
        <td>fd27c1c2-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce136a1c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.68410453216</td>
        <td>985.11052631</td>
        <td>0.0033796295731</td>
        <td>4.8666665853</td>
        <td>12.997986111</td>
        <td>18717.1</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2008</td>
        <td>Shih Tzu-Poodle Mix</td>
        <td>0</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 21:30:14</td>
        <td>2013-07-25 19:41:49</td>
        <td>socialite</td>
        <td>None</td>
        <td>Cross Breed</td>
        <td>None</td>
        <td>fd27c5be-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce136ac6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.22328155458</td>
        <td>321.5254386</td>
        <td>0.0038888889878</td>
        <td>5.6000001424</td>
        <td>4.2423495371</td>
        <td>6108.9833334</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2011</td>
        <td>German Shepherd Dog-Pembroke Welsh Corgi Mix</td>
        <td>40</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 22:29:24</td>
        <td>2013-07-25 19:41:49</td>
        <td>None</td>
        <td>None</td>
        <td>Cross Breed</td>
        <td>None</td>
        <td>fd27c74e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce136c24-7144-11e5-ba71-058fbc01cf0b</td>
        <td>14</td>
        <td>25.69619391</td>
        <td>37002.519231</td>
        <td>0.0058449073678</td>
        <td>8.4166666097</td>
        <td>334.05052083</td>
        <td>481032.75</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2011</td>
        <td>Vizsla</td>
        <td>60</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 23:09:37</td>
        <td>2013-07-25 19:41:49</td>
        <td>socialite</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
        <td>fd27c7d0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce136e36-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.0053776803082</td>
        <td>7.7438596438</td>
        <td>0.0044791667034</td>
        <td>6.4500000529</td>
        <td>0.10217592586</td>
        <td>147.13333323</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2007</td>
        <td>Pug</td>
        <td>20</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 23:10:40</td>
        <td>2013-07-31 21:57:21</td>
        <td>stargazer</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd27c852-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce136ee0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.051511939572</td>
        <td>74.177192984</td>
        <td>0.0039930555499</td>
        <td>5.7499999919</td>
        <td>0.97872685187</td>
        <td>1409.3666667</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2010</td>
        <td>Boxer</td>
        <td>50</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 23:27:59</td>
        <td>2013-07-25 19:41:49</td>
        <td>ace</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Working</td>
        <td>fd27c8d4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce136f94-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.15856725147</td>
        <td>228.33684211</td>
        <td>0.0039351851456</td>
        <td>5.6666666097</td>
        <td>3.0127777779</td>
        <td>4338.4000001</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2010</td>
        <td>German Shepherd Dog-Nova Scotia Duck Tolling Retriever Mix</td>
        <td>30</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-06 00:09:33</td>
        <td>2014-05-30 15:52:56</td>
        <td>None</td>
        <td>None</td>
        <td>Cross Breed</td>
        <td>None</td>
        <td>fd27c956-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce134be0-7144-11e5-ba71-058fbc01cf0b</td>
        <td>11</td>
        <td>2.8247997685</td>
        <td>4067.7116667</td>
        <td>0.0037731481142</td>
        <td>5.4333332845</td>
        <td>28.247997685</td>
        <td>40677.116667</td>
    </tr>
</table>



NOTE: if you do this for the dogs table, your output will be too wide to see at one time in the dialog box.  Use the scroll bars at bottom and to the right of the dialog box to see the entire output table.  
   
     
SELECT statements can also be used to make new derivations of individual columns using "+" for addition, "-" for subtraction, "\*" for multiplication, or "/" for division.  For example, if you wanted the median inter-test intervals in hours instead of minutes or days, you could query:

```mySQL
SELECT median_iti_minutes / 60
FROM dogs LIMIT 5, 10;
```

**Question 9: Go ahead and try it, adding in a column to your output that shows you the original median_iti in minutes.**




```python
%%sql
SELECT median_iti_minutes
FROM dogs LIMIT 5, 10;
```

    10 rows affected.





<table>
    <tr>
        <th>median_iti_minutes</th>
    </tr>
    <tr>
        <td>5.1333333171</td>
    </tr>
    <tr>
        <td>0.48333322347</td>
    </tr>
    <tr>
        <td>6.6833334635</td>
    </tr>
    <tr>
        <td>4.8666665853</td>
    </tr>
    <tr>
        <td>5.6000001424</td>
    </tr>
    <tr>
        <td>8.4166666097</td>
    </tr>
    <tr>
        <td>6.4500000529</td>
    </tr>
    <tr>
        <td>5.7499999919</td>
    </tr>
    <tr>
        <td>5.6666666097</td>
    </tr>
    <tr>
        <td>5.4333332845</td>
    </tr>
</table>



## Now it's time to practice writing your own SELECT statements.  

**Question 10: How would you retrieve the first 15 rows of data from the dog_guid, subcategory_name, and test_name fields of the Reviews table, in that order?**   



```python
%%sql
SELECT dog_guid, subcategory_name, test_name
FROM reviews LIMIT 15;
```

    15 rows affected.





<table>
    <tr>
        <th>dog_guid</th>
        <th>subcategory_name</th>
        <th>test_name</th>
    </tr>
    <tr>
        <td>ce3ac77e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Yawn Warm-up</td>
    </tr>
    <tr>
        <td>ce2aedcc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Eye Contact Warm-up</td>
    </tr>
    <tr>
        <td>ce2aedcc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Eye Contact Game</td>
    </tr>
    <tr>
        <td>ce2aedcc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Communication</td>
        <td>Treat Warm-up</td>
    </tr>
    <tr>
        <td>ce405c52-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Yawn Warm-up</td>
    </tr>
    <tr>
        <td>ce405c52-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Yawn Game</td>
    </tr>
    <tr>
        <td>ce405c52-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Eye Contact Game</td>
    </tr>
    <tr>
        <td>ce405e28-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Communication</td>
        <td>Treat Warm-up</td>
    </tr>
    <tr>
        <td>ce405e28-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Cunning</td>
        <td>Turn Your Back</td>
    </tr>
    <tr>
        <td>ce2609c4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Communication</td>
        <td>Treat Warm-up</td>
    </tr>
    <tr>
        <td>ce2609c4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Communication</td>
        <td>Arm Pointing</td>
    </tr>
    <tr>
        <td>ce2609c4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Communication</td>
        <td>Foot Pointing</td>
    </tr>
    <tr>
        <td>ce260c1c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Shaker Game</td>
        <td>Shaker Warm-Up</td>
    </tr>
    <tr>
        <td>ce3fd48a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Yawn Game</td>
    </tr>
    <tr>
        <td>ce3fd48a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>Empathy</td>
        <td>Eye Contact Warm-up</td>
    </tr>
</table>



**Question 11: How would you retrieve 10 rows of data from the activity_type, created_at, and updated_at fields of the site_activities table, starting at row 50?  What do you notice about the created_at and updated_at fields?**


```python
%%sql
SELECT activity_type, created_at, updated_at
FROM site_activities LIMIT 10 OFFSET 49;
```

    10 rows affected.





<table>
    <tr>
        <th>activity_type</th>
        <th>created_at</th>
        <th>updated_at</th>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 20:55:08</td>
        <td>2013-07-25 20:55:08</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 20:55:07</td>
        <td>2013-07-25 20:55:07</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 20:55:50</td>
        <td>2013-07-25 20:55:50</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 20:56:09</td>
        <td>2013-07-25 20:56:09</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 20:56:21</td>
        <td>2013-07-25 20:56:21</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 21:00:31</td>
        <td>2013-07-25 21:00:31</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 21:02:29</td>
        <td>2013-07-25 21:02:29</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 21:02:31</td>
        <td>2013-07-25 21:02:31</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 21:02:45</td>
        <td>2013-07-25 21:02:45</td>
    </tr>
    <tr>
        <td>point_in_cat</td>
        <td>2013-07-25 21:05:41</td>
        <td>2013-07-25 21:05:41</td>
    </tr>
</table>



**Question 12: How would you retrieve 20 rows of data from all the columns in the users table, starting from row 2000?  What do you notice about the free_start_user field?**


```python
%%sql
SELECT *
FROM users LIMIT 20 OFFSET 2000;
```

    20 rows affected.





<table>
    <tr>
        <th>sign_in_count</th>
        <th>created_at</th>
        <th>updated_at</th>
        <th>max_dogs</th>
        <th>membership_id</th>
        <th>subscribed</th>
        <th>exclude</th>
        <th>free_start_user</th>
        <th>last_active_at</th>
        <th>membership_type</th>
        <th>user_guid</th>
        <th>city</th>
        <th>state</th>
        <th>zip</th>
        <th>country</th>
        <th>utc_correction</th>
    </tr>
    <tr>
        <td>3</td>
        <td>2013-02-21 23:25:03</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>2</td>
        <td>ce24589a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-22 01:21:20</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce245a3e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-22 03:05:42</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>2</td>
        <td>ce245c50-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>5</td>
        <td>2013-02-22 03:29:06</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>2014-06-23 23:31:28</td>
        <td>1</td>
        <td>ce245d7c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-22 15:05:13</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce245eda-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-22 16:30:17</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>2</td>
        <td>ce245f98-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-22 18:16:05</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce24611e-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>3</td>
        <td>2013-02-22 20:26:53</td>
        <td>2015-01-28 20:51:53</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce24629a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-22 23:14:40</td>
        <td>2015-01-28 20:51:54</td>
        <td>2</td>
        <td>2</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>2</td>
        <td>ce2465ec-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-23 13:05:38</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce246772-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>3</td>
        <td>2013-02-23 16:56:55</td>
        <td>2015-01-28 20:51:54</td>
        <td>2</td>
        <td>2</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>2</td>
        <td>ce24688a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>3</td>
        <td>2013-02-23 21:07:41</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>2</td>
        <td>ce246b28-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-23 21:22:09</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce246b82-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2013-02-23 22:48:15</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>2014-08-04 14:16:34</td>
        <td>2</td>
        <td>ce246c40-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2013-02-24 00:14:21</td>
        <td>2015-01-28 20:51:54</td>
        <td>2</td>
        <td>2</td>
        <td>0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>2</td>
        <td>ce246dbc-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-24 07:34:32</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce246e84-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-24 14:48:43</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce246ff6-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-24 15:34:10</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce247050-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2013-02-24 15:36:19</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>1</td>
        <td>0</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>1</td>
        <td>ce2470aa-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
    <tr>
        <td>2</td>
        <td>2013-02-24 16:29:34</td>
        <td>2015-01-28 20:51:54</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>None</td>
        <td>None</td>
        <td>2014-07-05 19:59:14</td>
        <td>1</td>
        <td>ce2470fa-7144-11e5-ba71-058fbc01cf0b</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
        <td>None</td>
    </tr>
</table>



## You have already learned how to see all the data in your database!  Congratulations!  

Feel free to practice any other queries you are interested in below:


```python
%%sql
SELECT *
FROM dogs LIMIT 5;
```

    5 rows affected.





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
        <td>female</td>
        <td>2011</td>
        <td>Labrador Retriever</td>
        <td>50</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 03:57:19</td>
        <td>2013-07-25 19:41:49</td>
        <td>charmer</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
        <td>fd27b272-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce134e42-7144-11e5-ba71-058fbc01cf0b</td>
        <td>21</td>
        <td>28.20107581</td>
        <td>40609.549167</td>
        <td>0.004687499952</td>
        <td>6.7499999308</td>
        <td>564.0215162</td>
        <td>812190.98333</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2007</td>
        <td>Shetland Sheepdog</td>
        <td>20</td>
        <td>1</td>
        <td>0</td>
        <td>2013-02-05 15:35:09</td>
        <td>2013-07-25 19:41:49</td>
        <td>protodog</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Herding</td>
        <td>fd27b5ba-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce1353d8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>20</td>
        <td>0.41330713937</td>
        <td>595.1622807</td>
        <td>0.0058449074865</td>
        <td>8.4166667806</td>
        <td>7.8528356481</td>
        <td>11308.083333</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2012</td>
        <td>Golden Retriever</td>
        <td>70</td>
        <td>0</td>
        <td>1</td>
        <td>2013-02-05 17:22:56</td>
        <td>2013-07-25 19:41:49</td>
        <td>None</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
        <td>fd27b6b4-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135ab8-7144-11e5-ba71-058fbc01cf0b</td>
        <td>2</td>
        <td>0.0091319445066</td>
        <td>13.15000009</td>
        <td>0.0091319445066</td>
        <td>13.15000009</td>
        <td>0.0091319445066</td>
        <td>13.15000009</td>
    </tr>
    <tr>
        <td>male</td>
        <td>2011</td>
        <td>Golden Retriever</td>
        <td>70</td>
        <td>0</td>
        <td>0</td>
        <td>2013-02-05 17:40:57</td>
        <td>2014-05-30 15:52:53</td>
        <td>None</td>
        <td>None</td>
        <td>Pure Breed</td>
        <td>Sporting</td>
        <td>fd27b79a-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce13507c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>11</td>
        <td>56.115179398</td>
        <td>80805.858333</td>
        <td>0.0044328703647</td>
        <td>6.3833333252</td>
        <td>561.15179398</td>
        <td>808058.58333</td>
    </tr>
    <tr>
        <td>female</td>
        <td>2010</td>
        <td>Shih Tzu</td>
        <td>190</td>
        <td>0</td>
        <td>0</td>
        <td>2013-02-05 18:12:28</td>
        <td>2014-05-30 15:52:54</td>
        <td>einstein</td>
        <td>1</td>
        <td>Pure Breed</td>
        <td>Toy</td>
        <td>fd27b86c-7144-11e5-ba71-058fbc01cf0b</td>
        <td>ce135e14-7144-11e5-ba71-058fbc01cf0b</td>
        <td>31</td>
        <td>7.7342542438</td>
        <td>11137.326111</td>
        <td>0.00039930554425</td>
        <td>0.57499998372</td>
        <td>232.02762731</td>
        <td>334119.78333</td>
    </tr>
</table>


