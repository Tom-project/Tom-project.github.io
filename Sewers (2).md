---
tags: [Nettitude]
title: Sewers
created: '2020-10-21T10:11:31.531Z'
modified: '2020-11-10T17:34:38.371Z'
---

# Sewers

```bash
# Nmap 7.80 scan initiated Fri Oct  9 11:35:53 2020 as: nmap -Pn -sS -sV -A -p- -oN nmapSewer.txt 192.168.0.22
Nmap scan report for 192.168.0.22
Host is up (0.00068s latency).
Not shown: 65520 closed ports
PORT      STATE SERVICE         VERSION
21/tcp    open  ftp             vsftpd 3.0.3
|_ftp-anon: got code 500 "OOPS: cannot change directory:/var/ftp/".
22/tcp    open  ssh             OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 61:f0:32:9d:9d:32:26:1e:54:f0:1c:69:c3:11:8a:79 (RSA)
|   256 db:e5:53:78:b8:40:fb:87:88:00:44:21:04:eb:93:81 (ECDSA)
|_  256 50:cf:a3:55:d3:72:09:4f:0d:fb:48:64:3f:51:55:af (ED25519)
25/tcp    open  smtp            Postfix smtpd
|_smtp-commands: sewers.accel, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
| ssl-cert: Subject: commonName=sewers.accel
| Subject Alternative Name: DNS:sewers.accel
| Not valid before: 2020-07-11T17:07:20
|_Not valid after:  2030-07-09T17:07:20
|_ssl-date: TLS randomness does not represent time
80/tcp    open  http            Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Apache2 Debian Default Page: It works
110/tcp   open  pop3            Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE STLS SASL TOP CAPA RESP-CODES PIPELINING UIDL
| ssl-cert: Subject: commonName=sewers.accel
| Subject Alternative Name: DNS:sewers.accel
| Not valid before: 2020-07-11T17:07:20
|_Not valid after:  2030-07-09T17:07:20
|_ssl-date: TLS randomness does not represent time
111/tcp   open  rpcbind         2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      40585/udp   mountd
|   100005  1,2,3      40868/udp6  mountd
|   100005  1,2,3      57789/tcp   mountd
|   100005  1,2,3      60049/tcp6  mountd
|   100021  1,3,4      35487/tcp6  nlockmgr
|   100021  1,3,4      36011/tcp   nlockmgr
|   100021  1,3,4      37043/udp6  nlockmgr
|   100021  1,3,4      42049/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
143/tcp   open  imap            Dovecot imapd
|_imap-capabilities: more have OK listed ENABLE post-login SASL-IR LOGIN-REFERRALS STARTTLS LOGINDISABLEDA0001 IMAP4rev1 Pre-login ID capabilities IDLE LITERAL+
| ssl-cert: Subject: commonName=sewers.accel
| Subject Alternative Name: DNS:sewers.accel
| Not valid before: 2020-07-11T17:07:20
|_Not valid after:  2030-07-09T17:07:20
|_ssl-date: TLS randomness does not represent time
2049/tcp  open  nfs_acl         3 (RPC #100227)
9090/tcp  open  ssl/zeus-admin?
| fingerprint-strings: 
|   GetRequest, HTTPOptions: 
|     HTTP/1.1 400 Bad request
|     Content-Type: text/html; charset=utf8
|     Transfer-Encoding: chunked
|     X-DNS-Prefetch-Control: off
|     Referrer-Policy: no-referrer
|     <!DOCTYPE html>
|     <html>
|     <head>
|     <title>
|     request
|     </title>
|     <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1.0">
|     <style>
|     body {
|     margin: 0;
|     font-family: "Open Sans", Helvetica, Arial, sans-serif;
|     font-size: 12px;
|     line-height: 1.66666667;
|     color: #333333;
|     background-color: #f5f5f5;
|     border: 0;
|     vertical-align: middle;
|     font-weight: 300;
|     margin: 0 0 10px;
|     @font-face {
|     font-family: 'Open Sans';
|_    font-styl
| ssl-cert: Subject: commonName=sewers/organizationName=68aa516dc3834c5ebee3bc3dfe4d45b6
| Subject Alternative Name: IP Address:127.0.0.1, DNS:localhost
| Not valid before: 2020-07-11T17:58:00
|_Not valid after:  2120-06-17T17:58:00
|_ssl-date: TLS randomness does not represent time
10000/tcp open  http            MiniServ 1.953 (Webmin httpd)
|_http-server-header: MiniServ/1.953
|_http-title: Site doesn't have a title (text/html; Charset=utf-8).
36011/tcp open  nlockmgr        1-4 (RPC #100021)
41333/tcp open  mountd          1-3 (RPC #100005)
48927/tcp open  mountd          1-3 (RPC #100005)
57789/tcp open  mountd          1-3 (RPC #100005)
65000/tcp open  http            Apache httpd 2.4.38 (
```

```bash
PORT      STATE SERVICE
111/udp   open  rpcbind
161/udp   open  snmp
2049/udp  open  nfs
10000/udp open  ndmp
MAC Address: 00:0C:29:8C:BA:B8 (VMware)

```

# RECON
- Running 4 web servers
  - Apache default page (80)
  - Zues-admin page running cockpit CMS (9090)
  - Webmin v (10000)
  - Some sort of forum type page (65000)
- NFS
  - RPC bind 
  - impacket script rpcdump.py
  - mounted shared file system for nfs (nothing inside)
- FTP
  - Allows for Anonymous login 
  - No directory traversal

## Zues-admin / cockpit
- Not vulnerable to typical RCE
- Potentially vulnerable to SSRF
- Potentially vulnerable to file disclosure attacks (LFI)

```bash
GET /assets/lib/fuc.js.php?url=dict://127.0.0.1:3306 HTTP/1.1
    Host: 127.0.0.1
    Connection: close
    Cache-Control: max-age=0
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
    Accept-Language: zh-CN,zh;q=0.8
    referer:http://127.0.0.1/index.php

```

