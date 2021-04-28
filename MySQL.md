# MySQL
## What is it?
#mysql is a relational database management system ( #RDBMS ) based on Structured Query Language ( #SQL )

MySQL, as a RDMS is made up of the server and utility programs that help in the administration of MySQL databases

The server handles all database instructions like creating editing and accessing data. It takes, and manages these requests and communicates using the MySQL protocol. This whole process can be broken down into these stages:  

1.  MySQL creates a database for storing and manipulating data, defining the relationship of each table.
2.  Clients make requests by making specific statements in SQL.
3.  The server will respond to the client with whatever information has been requested

Runs on Linux or Windows, commonly used as a back-end database for websites. Forms an essential component of the #LAMP stack (Linux, Apache, MySQL, and PHP)

### Structure
#### Schema == Database, mostly
In my MySQL, physically, a *schema* is synonymous with a *database*. You can even substitute the two in MySQL SQL syntax, e.g. `CREATE SCHEMA` is equivalent to `CREATE DATABASE`. This does not hold for other database products. For example #oracle database a *schema* represents only a part of a database: the tables and other objects owned by a single user. 
#### Hashes
MySQL can uses hash tables but also can of course use hashes to store credentials. 



### Technical Links
- https://dev.mysql.com/doc/dev/mysql-server/latest/PAGE_SQL_EXECUTION.htl
- https://www.w3schools.com/php/php_mysql_intro.asp


## Continuing Topics
- [[Enumerating MySQL]]
- [[Exploiting MySQL]]