---
title: Roboto Sans
date: 2025-05-20 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, robotosans, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Roboto Sans
Author: Mubarak Mikail
Description
The flag is somewhere on this web application not necessarily on the website. Find it. Check this out.
{: .prompt-tip }


El título del reto ya nos da una pista "Roboto", así que después de echar un ojo en el código de la web, pruebo a visitar 
el fichero robots.txt, además aparecen tres pistas, algo que parece bas64, algún tipo de cifrado, como son letras imagino algún tipo de 
¿transposición?.

```html
User-agent *
Disallow: /cgi-bin/
Think you have seen your flag or want to keep looking.

ZmxhZzEudHh0;anMvbXlmaW
anMvbXlmaWxlLnR4dA==
svssshjweuiwl;oiho.bsvdaslejg
Disallow: /wp-admin/
```

Por una lado aparece el nombre de un posible fichero interesante flag1.txt, por otro no parece que la flag se encuentre en /cgi-bin/ o en /wp-admin/ una pregunta es saber si "js/myfif2זfRG@" es una pista del reto a resolver.

```
pablo☠office ~$ echo "ZmxhZzEudHh0;anMvbXlmaW
anMvbXlmaWxlLnR4dA==" |  base64 -d  
flag1.txtbase64: entrada inválida
```

Desde https://www.base64decode.org/ voy provando los fragmentos hasta que me doy cuenta de que realmente puede hacerlo separando las líneas

```
flag1.txtjs/myfi
js/myfile.txt
,z/u%z8
```

Compruebo en la consola que efectivamente /js/myfile.txt es la ruta donde encontraremos la flag que tenemos que encontrar. 
Luego en el navegador.

```
pablo☠office ~$ echo "anMvbXlmaWxlLnR4dA==" | base64 -d
js/myfile.txt% 

```

flag: **picoCTF{Who_D03sN7_L1k5_90B0T5_032f1c2b}**

