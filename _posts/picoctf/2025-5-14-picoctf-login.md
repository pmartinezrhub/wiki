---
title: login
date: 2025-05-14 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, login, webexplotation]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>login
Author: BrownieInMotion
Description
My dog-sitter's brother made this website but I can't get in; can you help? login.mars.picoctf.net
{: .prompt-tip }

Si fallamos el login aparece un mensaje alert. Esto indica que el login esta relacionado con javascript. 
Si procedemos a investigar el código de la página vemos que hay un enlace https://login.mars.picoctf.net/index.js
Pero el código esta escrito en una sola linea, para corregir esto podemos valernos de alguna web como https://beautifier.io/
No obstante aparece una serie de carácteres de lo que parece base64:

``` shell
pablo☠office ~$ echo "cGljb0NURns1M3J2M3JfNTNydjNyXzUzcnYzcl81M3J2M3JfNTNydjNyfQ" | base64 -d           
picoCTF{53rv3r_53rv3r_53rv3r_53rv3r_53rv3r}
```

flag: **picoCTF{53rv3r_53rv3r_53rv3r_53rv3r_53rv3r}**

Para ser de dificultad media, es bastante trivial. 
