# Burpsuite Academy (SQL injection UNION attack, retrieving multiple values in a single column)

## Challenge: 

![challenge description](img/sqli_union_dump/chall%20desc.jpg)

## Solution:
This challenge requires us to use manual SQLi to first find the number of columns present, find which column has the string data type, find the username and password columns from the users table and to dump the database.

Firstly, I went to the challenge homepage.

![challenge homepage](img/sqli_union_dump/homepage.jpg)

Secondly, I clicked onto the `categories` tab which showed me the following page.

![clicked on cats](img/sqli_union_dump/normal%20query.jpg)

Looking at the GET parameter, it seems like it is interacting with a back-end database.

Hence, I tried to inject a single quote and obtained the `Internal Server Error` message.

![internal server error](img/sqli_union_dump/single%20quote%20error.jpg)

Moving on, I tried to determine the number of columns present in the original `SELECT` query, using the command `' UNION SELECT NULL -- -`, which gave the following output.

![output of single null](img/sqli_union_dump/union%20select%20one%20null.jpg)

After incrementing the number of columns and trying again, I obtained the following output.

![output of 2 nulls](img/sqli_union_dump/union%20select%202%20nulls.jpg)

This shows that there are 2 columns in the original `SELECT` statement.

Next, I had to determine which columns contain the string data type. Using the command `' UNION SELECT 'a',NULL -- -`, I received the `Internal Server Error` message. Hence, I decided to try for the second column. Luckily, the second column is of string data type.

![second col](img/sqli_union_dump/second%20col%20is%20str%20data%20type.jpg)

Moving on, I tried to find the tables and columns that store sensitive information such as usernames and passwords. I tried the command `' UNION SELECT NULL,CONCAT(table_name,':', column_name) FROM information_schema.columns -- -` and managed to ouput all the tables and columns present.

![output all tables and cols](img/sqli_union_dump/showing%20tables%20and%20cols.jpg)

Using `CTRL-F` and searching for `users`, I found that there are 2 columns, `username` and `password` for the `users` table.

![username](img/sqli_union_dump/users%20table%20with%20col%20username.jpg)

![pw](img/sqli_union_dump/users%20table%20with%20col%20pw.jpg)

Next, I used the command `' UNION SELECT NULL,CONCAT(username,':',password) FROM users -- -` to dump the users table.

![obtained admin pw](img/sqli_union_dump/dump%20db.jpg)

Lastly, I was able to login as the `administrator` account, solving this challenge. :')

![solved](img/sqli_union_dump/able%20to%20login%20as%20administrator.jpg)


