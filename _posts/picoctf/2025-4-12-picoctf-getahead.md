---
title: GET a HEAD
date: 2025-04-14 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, getahead, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>GET aHEAD
Author: madStacks
Description
Find the flag being held on this server to get ahead of the competition http://mercury.picoctf.net:28916/
{: .prompt-tip }

Al abrir la web aparece dos bontones, red y blue, parec euna página PHP y si nos fijamos parece que se lo podrían pasar parámetros.
http://mercury.picoctf.net:28916/index.php?

El reto nos da una pista de como debemos proceder, "GET a HEAD", por lo tanto podemos modificar el método GET 
Capturo con Zap el request y lo edito en el requester modificando GET por HEAD. 
Característica	GET	devuelve el BODY del recurso mientras que HEAD devuelve cabeceras (headers) sin cuerpo.

``` html
HEAD http://mercury.picoctf.net:28916/index.php HTTP/1.1
host: mercury.picoctf.net:28916
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Connection: keep-alive
Referer: http://mercury.picoctf.net:28916/index.php?
Upgrade-Insecure-Requests: 1
Priority: u=0, i
content-length: 4

HTTP/1.1 200 OK
flag: picoCTF{r3j3ct_th3_du4l1ty_70bc61c4}
Content-type: text/html; charset=UTF-8
```
Lo anterior también se puede hacer en Firefox desde la consosla del desarrollador y en la pestaña de red modficar el método y volver a enviar 
la petición

flag: **picoCTF{r3j3ct_th3_du4l1ty_70bc61c4}**

Este reto es una introducción al método HEAD, intersante no olvidarse que puede haber información en las cabeceras.
No obstante entiendo que GET debería mostrar ambas informaciones a no ser que el administrador del sistema manipule el servidor
para que esta información varie en función de la petición que se le haga a la web.
