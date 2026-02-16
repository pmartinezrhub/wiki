---
title: Scavenger Hunt
date: 2025-04-17 12:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, scavengerhunt, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Scavenger Hunt
Author: madStacks
Description
There is some interesting information hidden around this site http://mercury.picoctf.net:44070/. Can you find it?
{: .prompt-tip }

Si inspeccionamos el código de la web vamos a encontrarnos la primera parte de la bandera o eso indican
```
<!-- Here's the first part of the flag: picoCTF{t -->
```
Si inspeccionamos en el archivo mycss.css, la parte 2:
``` html 

/* CSS makes the page look nice, and yes, it also has part of the flag. Here's part 2: h4ts_4_l0 */
```
myjs.js no contiene la tercera parte pero puede que nos esté dando una pista
/* How can I keep Google from indexing my website? */

Para evitar que google y otros robots indexen nuestro sitio web se usa el fichero robots.txt, visitando http://mercury.picoctf.net:44070/robots.txt obtenemos la tercera parte.

```
User-agent: *
Disallow: /index.html
# Part 3: t_0f_pl4c
# I think this is an apache server... can you Access the next flag?
```
En servidores apache es común este archivo, hubiera usado una herramienta automática pero te piden específicamente que no uses
fuerza bruta pero usando el spider de Zap lo encontré rápidamente. 
http://mercury.picoctf.net:44070/.htaccess

```
# Part 4: 3s_2_lO0k
# I love making websites on my Mac, I can Store a lot of information there.```
```

La siguiente pista nos habla de hacer sitios con su mac http://mercury.picoctf.net:44070/.DS_Store 
Los archivos .DS_Store (Desktop Services Store) son ficheros que contienen información de personalización del sistema, como puede ser los iconos, tipo de fondo, fuente, columnas, etc. El porque lo mete en el servidor web, sería una cuestión a determinar.
PicoCTF se ha tomado la molestia de dejar un archivo .ds_store que podría confundirnos, pero al usar Zap he podido darme cuenta.

```
Congrats! You completed the scavenger hunt. Part 5: _7a46d25d}
```

flag: **picoCTF{th4ts_4_l0t_0f_pl4c3s_2_lO0k_7a46d25d}**

