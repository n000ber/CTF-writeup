# TryHackMe (sqlilab: Intro 2)

## Challenge: 
![chall desc](img/sqlilab/update%20chall%20desc.jpg)

## Solution: 

This challenge requires us to use `UPDATE` as the injection point to dump the `secrets` table.

Firstly, I headed over to the challenge site and was greeted with the following login page.

![login page](img/sqlilab/update%20homepage.jpg)

After logging in with the given credentials, I was redirected to this page.

![after logged in](img/sqlilab/logged%20in.jpg)

Clicking on the `Edit Profile` tab, I was redirected to the following page.

![Update profile](img/sqlilab/updating%20profile.jpg)

Upon updating my nickname, email and password, I was able to view them on the `Profile` page.

![able to view the query](img/sqlilab/able%20to%20view%20the%20query.jpg)

Now, I had to determine the name of the various columns present. This could be inferred from the `id` of the various form tags in the HTML code. 

![id could be col name](img/sqlilab/id%20could%20be%20col%20name.jpg)

I tried to determine the type of DB that was used by using the command `',nickName=sqlite_version() -- -` in the nickname field.

![sqlite version checking](img/sqlilab/sqlite%20version%20checking.jpg)

![sqlite version shown](img/sqlilab/sqlite%20version%20shown.jpg)

Knowing that it is a `sqlite` database, I then tried to dump the tables present, using the command `',nickName=(SELECT group_concat(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%')-- -`

![Dumped tables](img/sqlilab/tables%20shown.jpg)

From the output, I was able to know that the `secrets` table consists of `id`, `author` and `secret` columns.

Finally, I constructed the injection payload `',nickName=(SELECT group_concat(id || "," || author || "," || secret) FROM secrets) -- -` to obtain the flag.

![found flag](img/sqlilab/found%20flag.jpg)

