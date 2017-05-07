# Intro To Relational Databases
## Data and Tables
1. Structuring Application Data:
	1. In memory - simple variables, data structures: lists, dictionaries objects
	1. Durable storage
		1. flat files on disk - text XML, JSON
1. Database gives us persistence, like a file, as well as data structures for storing and searching our data. Usually much faster and easier than we could search a flat file.
1. Databases also make it possible for multiple programs or users to access, and modify data at the same time without stepping on each other's toes, or accidentally undoing each other's changes.
1. Relational database features:
	1. Persistent storage (like all dbs)
	1. Safe concurrent access by multiple users (like all dbs)
	1. Flexible query language with aggregation and join operations
	1. Constraints - rules for protecting consistency of your data
1. Aggregation - a database operation that summarises multiple rows into a single row. E.g. Count, Sum, Min, Max, Avg
1. In a relational database, when there are multiple answers to a question (like "What do brown bears eat?") they will usually appear as multiple rows in a table.
1. Derive new tables by linking up existing tables using `join`.
1. A column that uniquely identifies the rows in a table can be called a primary key.

## Elements of SQL
1. Data types in SQL
1. In SQL, Always put 'single quotes' around text strings and date/time values. E.g.  '2012-11-23'.
1. SQL to create table:
```SQL
create table animals (  
       name text,
       species text,
       birthdate date);

create table diet (
       species text,
       food text);  

create table taxonomy (
       name text,
       species text,
       genus text,
       family text,
       t_order text); 

create table ordernames (
       t_order text,
       name text);
```
1. Example queries:
```Python
QUERY = "select max(name) from animals;"

QUERY = "select * from animals limit 10;"

QUERY = "select * from animals where species = 'orangutan' order by birthdate;"

QUERY = "select name from animals where species = 'orangutan' order by birthdate desc;"

QUERY = "select name, birthdate from animals order by name limit 10 offset 20;"

QUERY = "select species, min(birthdate) from animals group by species;"
# for each species of animal, find the smallest value of the birthdate column, that is, the oldest animal's birthdate
QUERY = '''
select name, count(*) as num from animals
group by name
order by num desc
limit 5;
# as here is to give the column a name, in this case 'num'
```
1. `GROUP BY` - which columns to use as groupings when aggregating
1. Why do the query in database? Why not get all the data and do it in python instead?
	1. Speed and space!
1. `Insert into table(col1, col2, ...) values(val1, val2, ...);`
1. Joining tables:
```SQL
Select T.thing, S.stuff
from T join S
on T.target = S.match

or

Select T.thing, S.stuff
from T,S
where T.target = S.match

The latter is more common in practice.
```
1. `where` is a restriction on the source tables.
1. `having` is a restriction on the result... after aggregation.
E.g.
```SQL
Select species, count(*) as num from animals group by species where num = 1; # Won't work because there's no num column in the source table

Select species, count(*) as num from animals group by species having num = 1; # This works 
```
## Python DB-API
1. Each database system has it's own library.
1. sql3lite library for SQLite
1. pyscopg2 library for PostgreSQL
E.g.
```Python
import sqlite3

con = sqlite3.connect("Cookies")

cursor = conn.cursor() # cursor runs query and fetches results
# it's called a cursor because when the database gives you results,
you use a cursor to scan through results

cursor.execute(
	"select host_key from cookies limit 10")

results = cursor.fetchall()

print results

conn.close()
```
1. Inserts in DB-API
```SQL
pg = psycopg2.connect("dbname = somedb")
c = pg.cursor()
c.execute("insert into names values('Jenny Smith')")
pg.commit() # ensures atomicity
# you commit because of transaction issues. Say two tables will be updated during a transaction, changes should take effect both at the same time or never take effect if something goes wrong.
```
1. Atomicity - a transaction happens as a whole or not at all.
1. Curing Bobby Tables:
```SQL
c.execute("INSERT INTO posts (content) VALUES (%s)", (content,))hece the comma
```
1. Javascript injection:
	1. [Bleach documentation](http://bleach.readthedocs.io/en/latest/) - stop unsafe HTML from attacking your site.
1. `update table set column = value where restriction ;` - to update table
1. `update posts set content = 'cheese' where content like '%spam%';`
1. `Delete` works mostly like select

## Deeper into SQL
1. In a normalized database, the relationships among the tables match the relationships that are really there among the data.
1. William Kent's [A Simple Guide to Five Normal Forms in Relational Database Theory](http://www.bkent.net/Doc/simple5.htm)
1. [Normalized Design](https://www.youtube.com/watch?v=l6SDnhM7B_k):
	1. Every row has the same number of columns.
	In practice, the database system won't let us literally have different numbers of columns in different rows. But if we have columns that are sometimes empty (null) and sometimes not, or if we stuff multiple values into a single field, we're bending this rule.
	The example to keep in mind here is the diet table from the zoo database. Instead of trying to stuff multiple foods for a species into a single row about that species, we separate them out. This makes it much easier to do aggregations and comparisons.

	1. There is a unique key and everything in a row says something about the key.
	The key may be one column or more than one. It may even be the whole row, as in the diet table. But we don't have duplicate rows in a table.
	More importantly, if we are storing non-unique facts — such as people's names — we distinguish them using a unique identifier such as a serial number. This makes sure that we don't combine two people's grades or parking tickets just because they have the same name.
	1. Facts that don't relate to the key belong in different tables.
	The example here was the items table, which had items, their locations, and the location's street addresses in it. The address isn't a fact about the item; it's a fact about the location. Moving it to a separate table saves space and reduces ambiguity, and we can always reconstitute the original table using a join.

	1. Tables shouldn't imply relationships that don't exist.
	The example here was the job_skills table, where a single row listed one of a person's technology skills (like 'Linux') and one of their language skills (like 'French'). This made it look like their Linux knowledge was specific to French, or vice versa ... when that isn't the case in the real world. Normalizing this involved splitting the tech skills and job skills into separate tables.
1. Create Table and Types:
```SQL
	create table tablename(
		column1 type,
		column2 type,
		...);
```
1. To `drop` a database is to delete a <database class=""></database>
1. in psql, connect to database with \c name.
1. Serial is shorthand for integer type plus a special default value. Database creates sequence, which is an internal data structure, sets that as the default value of the serial column.
1. Declaring primary key:
```SQL
	create table tablename(
		id serial primary key,
		name text,
		birthdate date
		);

	# multicolumn primary key
	create table tablename(
		postal_code text,
		country text,
		name text,
		primary key (postal_code, country)
		);		
```
1. Declaring relationships
```SQL
	create table sales (
		sku text references products (sku),
		sale_date date,
		count integer
	);
```
1. SKU - stock keeping unit, an id number or ode used for products.
1. A foreign key is a column or set of columns in one table, that uniquely identifies rows in another table.
1. Inner join returns only rows which matched the conditions.
1. Left or Right Join returns all rows of the left or right table.
```SQL
select programs.name, count(bugs.filename) as num
       from programs left join bugs
         on programs.filename = bugs.filename
       group by programs.name
       order by num;
```
1. Subqueries - you can query a result table, because it's a table.
1. E.g.
```SQL
	select avg(bigscore) from
	(select max(score)
		as bigscore
		from mooseball
		group by team)
	as maxes; # required to name the result table

	# or 
	select name, weight 
	from players, 
		(select avg(weight) as av
		from players) as subq
	where weight < av;
```
1. A view is a select query stored in the database in a way that lets you use it like a table. E.g.
```SQL
	create view viewname as select...
```
1. Your ability to update or delete rows in a view depends on the database system.

