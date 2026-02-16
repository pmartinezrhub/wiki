---
title: Cocodrile
date: 2025-04-5 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, cocodrile, gobuster, fuzzing]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/00935a242722b9a2c700bdb6b65195f6.png
    alt: Hack The Box
---
## Cocodrile
Esta vez el reto de principiantes comienza con algo relacionado con el puerto de FTP 21 el puerto 80 y algo de fuzzing con gobuster. 

``` shell
└─$ nmap -p- 10.129.14.219  -vv    
canning 10.129.14.219 [65535 ports]
Discovered open port 21/tcp on 10.129.14.219
Discovered open port 80/tcp on 10.129.14.219
```
 ## Task 1

What Nmap scanning switch employs the use of default scripts during a scan?
> -sC

## Task 2

What service version is found to be running on port 21?
``` shell
 nmap -p 21 10.129.14.219  -sC -vv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-06 16:16 EDT
NSE: Loaded 126 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 16:16
Completed NSE at 16:16, 0.00s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 16:16
Completed NSE at 16:16, 0.00s elapsed
Initiating Ping Scan at 16:16
Scanning 10.129.14.219 [4 ports]
Completed Ping Scan at 16:16, 0.07s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 16:16
Completed Parallel DNS resolution of 1 host. at 16:16, 13.00s elapsed
Initiating SYN Stealth Scan at 16:16
Scanning 10.129.14.219 [1 port]
Discovered open port 21/tcp on 10.129.14.219
Completed SYN Stealth Scan at 16:16, 0.07s elapsed (1 total ports)
NSE: Script scanning 10.129.14.219.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 16:16
NSE: [ftp-bounce 10.129.14.219:21] PORT response: 500 Illegal PORT command.
Completed NSE at 16:16, 2.70s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 16:16
Completed NSE at 16:16, 0.00s elapsed
Nmap scan report for 10.129.14.219
Host is up, received echo-reply ttl 63 (0.048s latency).
Scanned at 2025-04-06 16:16:50 EDT for 3s

PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 63
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.15.134
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
|_-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 16:16
Completed NSE at 16:16, 0.00s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 16:16
Completed NSE at 16:16, 0.01s elapsed
Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 16.69 seconds
           Raw packets sent: 5 (196B) | Rcvd: 2 (72B)
```
> vsFTPd 3.0.3

## Task 3

What FTP code is returned to us for the "Anonymous FTP login allowed" message?
>230

## Task 4

After connecting to the FTP server using the ftp client, what username do we provide when prompted to log in anonymously?
>anonymous

## Task 5

After connecting to the FTP server anonymously, what command can we use to download the files we find on the FTP server?
>get

## Task 6

What is one of the higher-privilege sounding usernames in 'allowed.userlist' that we download from the FTP server?

``` shell
┌──(kali㉿kali25)-[~]
└─$ ftp 10.129.14.219    
Connected to 10.129.14.219.
220 (vsFTPd 3.0.3)
Name (10.129.14.219:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||40057|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            33 Jun 08  2021 allowed.userlist
-rw-r--r--    1 ftp      ftp            62 Apr 20  2021 allowed.userlist.passwd
226 Directory send OK.
ftp> get allowed.userlist
local: allowed.userlist remote: allowed.userlist
229 Entering Extended Passive Mode (|||49909|)
150 Opening BINARY mode data connection for allowed.userlist (33 bytes).
100% |********************************|    33        6.97 KiB/s    00:00 ETA
226 Transfer complete.
33 bytes received in 00:00 (0.31 KiB/s)
ftp> get 
allowed.userlist        allowed.userlist.passwd
ftp> get allowed.userlist.passwd
local: allowed.userlist.passwd remote: allowed.userlist.passwd
229 Entering Extended Passive Mode (|||47280|)
150 Opening BINARY mode data connection for allowed.userlist.passwd (62 bytes).
100% |********************************|    62       88.77 KiB/s    00:00 ETA
226 Transfer complete.
62 bytes received in 00:00 (0.15 KiB/s)
ftp> 
```
> admin

## Task 7

What version of Apache HTTP Server is running on the target host?

``` shell
└─$ nmap -p 80 10.129.14.219  -sV -vv 
PORT   STATE SERVICE REASON         VERSION
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
```
> Apache httpd 2.4.41

## Task 8

What switch can we use with Gobuster to specify we are looking for specific filetypes?
> -x

## Task 9

Which PHP file can we identify with directory brute force that will provide the opportunity to authenticate to the web service?

Nos valemos de gobuster y un diccionario de wfuzz para encontrar posibles páginas php
```shell
┌──(kali㉿kali25)-[/usr/share/wordlists]
└─$ gobuster dir -u http://10.129.14.219/ -w /usr/share/wordlists/wfuzz/general/admin-panels.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.14.219/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/wfuzz/general/admin-panels.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/login.php            (Status: 200) [Size: 1577]
Progress: 134 / 135 (99.26%)
===============================================================
Finished
===============================================================

```
>login.php

## Submit Flag

Ahora que sabamos donde está la página de login introducimos la password que capturamos durante la parte de FTP
``` shell
┌──(kali㉿kali25)-[~]
└─$ cat allowed.userlist*      
aron
pwnmeow
egotisticalsw
admin
root
Supersecretpassword1
@BaASD&9032123sADS
rKXM59ESxesUFHAd
```
Entramos en  http://10.129.14.219/login.php
introducimos admin:rKXM59ESxesUFHAd

## Submit root flag [achivement](https://www.hackthebox.com/achievement/machine/2336390/404)