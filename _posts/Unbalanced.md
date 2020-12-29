---
tags: [HTB]
title: Unbalanced
created: '2020-09-03T21:51:09.662Z'
modified: '2020-11-10T17:32:59.362Z'
---

# Unbalanced 

nmap




hypothesis:
- squid proxy 4.6 - info disclosure exploit
> https://www.cybersecurity-help.cz/vdb/SB2019110710
> CVE CVE-2019-18679
- rsync 
> https://book.hacktricks.xyz/pentesting/873-pentesting-rsync

- Can just connect to rsync and tells us what mounted directories are on the server
``` bash
nc -vn 127.0.0.1 873
(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0        <--- You receive this banner with the version from the server
@RSYNCD: 31.0        <--- Then you send the same info
#list                <--- Then you ask the sever to list
raidroot             <--- The server starts enumerating
USBCopy        	
NAS_Public     	
_NAS_Recycle_TOSRAID	<--- Enumeration finished
@RSYNCD: EXIT         <--- Sever closes the connection```

- Output

``````
