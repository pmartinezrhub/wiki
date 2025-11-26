---
title: Who are you?
date: 2025-05-21 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, whoareyou, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Who are you?
Author: madStacks
Description
Let me in. Let me iiiiiiinnnnnnnnnnnnnnnnnnnn http://mercury.picoctf.net:36622/
{: .prompt-tip }

Al conectarnos a la web veresmos la foto de un niño sonriente en un gif que pregunta ¿Who are you? y una frase:

>Only people who use the official PicoBrowser are allowed on this site!

Como ya suponemos no existe un __PicoBrowser__ pero podemos cambiar el User Agent en cualquier momento como ya vimos en otro post anterior.

Al hacer esto veremos que cambia el contenido de la web y en el código aparece lo siguiente

``` html
<h3 style="color:red">I don&#39;t trust users visiting from another site.</h3>
```

La única pista que nos dan es un enlace a un RFC [https://datatracker.ietf.org/doc/html/rfc2616](https://datatracker.ietf.org/doc/html/rfc2616). Un poco largo.

Supongo que como no le gusta que se visite el contenido de la web desde otros sitios, habría que hacerlo desde la propia web
Podría intentar averiguar el idioma cambiando la cabezera de idioma en las cabeceras. La geolocalización de esa IP me envía a Amazon, Ohio, por lo que debe ser una instancia de Amazon. Lo único que se me ocurre en este punto es añadir el Referer a la cabecera
Consigo avanzar pero con una salvedad

>Referer: http://mercury.picoctf.net:36622/

``` html
<h3 style="color:red">Sorry, this site only worked in 2018.</h3>
```

Habrá que volver al pasado?
Añado ahora Date con el siguiente resultado al enviar la request

>Date: 20 May 2018

``` html
<h3 style="color:red">I don&#39;t trust users who can be tracked.</h3>
```

No confía en usuarios que pueden ser trackeados, así que siguiendo el guión añado otra cosa más a las cabeceras "Do Not Track", el resultado en cuanto menos sorprendente pues dice "Este sitio es solo para personas de Suecia", pero podría ser.

> DNT: 1

```  html 
<h3 style="color:red">This website is only for people from Sweden.</h3>
```

Pues siguiendo el guión ya preestablecido de añadir valores a los parámetros del request, busco una dirección ip aleatoria que pertenezca a Suecia. Puedes obtenerla de esta web [https://lite.ip2location.com/sweden-ip-address-ranges](https://lite.ip2location.com/sweden-ip-address-ranges) pero otra vez nos hace una vez más una solicitud para el request, el idioma

> X-Forwarded-For: 102.177.146.15

``` html
<h3 style="color:red">You&#39;re in Sweden but you don&#39;t speak Swedish?</h3>
```

Al final con el idioma incluído, sv-SE Sueco, conseguimos la bandera.

> Accept-Language: sv-SE

``` html
GET http://mercury.picoctf.net:36622/ HTTP/1.1
host: mercury.picoctf.net:36622
User-Agent: PicoBrowser
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: sv-SE
content-length: 0
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Referer: http://mercury.picoctf.net:36622/
Date: 20 May 2018
DNT: 1
X-Forwarded-For: 102.177.146.15
```

flag:  **picoCTF{http_h34d3rs_v3ry_c0Ol_much_w0w_0da16bb2}**

