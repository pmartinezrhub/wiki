---
title: findme
date: 2025-07-27 10:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web. base64]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>findme
Author: Geoffrey Njogu
Description
Help us test the form by submiting the username as test and password as test! The website running here.
{: .prompt-tip }

Accedemos al reto con una web, piden ayuda para testear un login test:test!

``` html
Help us test this form
username:test and password:test.
Username Password
```
Como es costumbre, comienzo capturando con Zap el request, y nos acaba llevando a otra página

```html
Welcome fellow Human

I was redirected here by a friend of mine but i couldnt find anything. Help me search for flags :-)
```

Al navegar hacia atrás para volver al formulario aparecen dos URL, que parecen que están haciendo ese redirect que aparece en las pistas, si ponemos atención parece que contienen información codificada en bas64. 

>http://saturn.picoctf.net:52464/next-page/id=bF90aGVfd2F5XzNkOWUzNjk3fQ==

Y al ir hacia atrás de nuevo otra URL

>http://saturn.picoctf.net:52464/next-page/id=cGljb0NURntwcm94aWVzX2Fs

``` shell
pablo☠office ~$ echo "bF90aGVfd2F5XzNkOWUzNjk3fQ==" | base64 -d
l_the_way_3d9e3697}%
pablo☠office ~$ echo "cGljb0NURntwcm94aWVzX2Fs" | base64 -d
picoCTF{proxies_al%   
```

Componemos la bandera 
flag: **picoCTF{proxies_all_the_way_3d9e3697}**

