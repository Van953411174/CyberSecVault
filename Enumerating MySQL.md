# Enumerating MySQL
#enumeration of #mysql can provide reams of important information. As a back-end database, MySQL can and often does contain sensitive user or business information, e.g. user names and credentials or client lists and current/past orders. 

Worst case a breach of the database can result in mandatory regulatory compliance reporting and irreparably impact the companies reputation.

## Required 
MySQL needs to be installed on the host system in order to connect to the remote MySQL server. `sudo apt install default-mysql-client`

## Tools
As with other enumerations, there are tools better suited for some jobs compared to other. MetaSploit has modules designed for MySQL but there are tools such as #nmap `mysql-enum` script that can also enumerate very well. Furthermore it can always be accomplished manually.
- MetaSploit
	- #metasploit has `mysql_enum`[^1] and `mysql_sql`modules 
		- `mysql_enum` conducts simple enumeration of a MySQL database server provided proper credentials to connect remotely.
			- Is a heavy-ish enumerator that can return extensive database and host information such as:
				- version
				- architecture
				- log file location
				- users with GRANT, CREAT USER, RELOAD, SHUTDOWN, SUPER, FILE, PROCESS, and msql database privs
		- `mysql_sql` enumerates by using simple SQL statements against the MySQL instance to retrieve information
			- data returned depends upon the what was set for the `SQL` option but by default returns the version of the database
- `nmap`
	- `mysql-enum`[^2] #NSE script performs valid-user enumeration against MySQL server
	- Server version 5.x are susceptible to enumeration due to different messages during login when using old authentication mechanisms from versions 4.x and earlier
	- Also utilized a bug discovered and published by Kingscope
- `mysql`
	- client tool that can be used to manually connect and enumerate a database
	- installed as part of [[Enumerating MySQL#Required|Required]]

[^1]: full module path `auxiliary/admin/mysql/mysql_enum`, and `""/mysql_sql` 
[^2]:https://nmap.org/nsedoc/scripts/mysql-enum.html

## Process
Enumeration can be done with or without credentials. Without credentials involves essentially attacking in an attempt to glean information from it. With credentials is a more standard enumeration involving manipulating the database, often a move done after credentials have been located or low priv has been achieved. 
### With Credentials
First check your credentials and connection to the server with a MySQL client:
`mysql -h IP -u USERNAME -p`
Break down:
- `-h` hostname/IP
- `-u` username 
- `-p` password
	- if you use the shortform `-p` you cannot have a space between it and the password
	- optionally, if you provide the `-p` or `--password` option without a provided password, you will be prompted for one 

A successful login will result in a MySQL prompt allow you to interact with the database. Since we'll be using automated tools, exit the session.

#### MetaSploit
After verifying credentials you can turn to automated enum tools. 
##### mysql_enum
Using `mysql_enum` module in Metasploit only requires
- `RHOSTS` 
- `RPORT`

However if the system requires credentials you should set `PASSWORD` and `USERNAME` accordingly.

##### mysql_sql
Using `mysql_sql` requires:
- `RHOSTS`
- `RPORT`
- `SQL` : the SQL statement to execute (default is `select version()`)

### Without Credentials
Enumerating w/o credentials can involve all sorts of types of attacks. From side channel, injection, indirect object reference, there are multiple routes from which to glean information about the underlying database. Some of this will be linked to [[Exploiting MySQL]] as it involves more offensive oriented methods. 



## Continuing Topic
- [[Exploiting MySQL]]