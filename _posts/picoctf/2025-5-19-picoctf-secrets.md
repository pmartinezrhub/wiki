---
title: Secrets
date: 2025-05-19 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, secrets, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Secrets
Author: Geoffrey Njogu
Description
We have several pages hidden. Can you find the one with the flag? The website is running here.
{: .prompt-tip }

Si empezamos por inspeccionar el código fuente de la web veremos que hay un enlace a una hoja de estilos css 

```
http://saturn.picoctf.net:62272/secret/assets/index.css
```

La pista habla de directorios por lo que adivino que la ruta /secret/ o /assets/ puede contener algo

accedemos a http://saturn.picoctf.net:62272/secret/ y encontramos lo siguiente:

``` html
<!DOCTYPE html>
<html>
  <head>
    <title></title>
    <link rel="stylesheet" href="hidden/file.css" />
  </head>

  <body>
    <h1>Finally. You almost found me. you are doing well</h1>
    <img src="https://media1.tenor.com/images/0a6aff9f825af62c05adfbd75039cc7b/tenor.gif?itemid=4648337" alt="Something Like That GIF - Andy Parksandrecreation Wtf GIFs" style="max-width: 833px; background-color: rgb(151, 121, 85);" width="833" height="937.125">
  </body>
</html>
```
Bamos buscando en los direcotorios que nos proponen en los enlaces hasta encontrar la flag en /secret/hidden/supperhidden/
http://saturn.picoctf.net:62272/secret/
http://saturn.picoctf.net:62272/secret/hidden/
http://saturn.picoctf.net:62272/secret/hidden/superhidden/

flag: **picoCTF{succ3ss_@h3n1c@10n_790d2615}**

Una intersante forma de explicar los paths en web y sus peligros. 