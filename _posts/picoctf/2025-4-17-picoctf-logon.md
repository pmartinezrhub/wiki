---
title: logon
date: 2025-04-17 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, logon, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>logon
Author: bobson
Description
The factory is hiding things from all of its users. Can you login as Joe and find what they've been looking at? https://jupiter.challenges.picoctf.org/problem/44573/ (link) or http://jupiter.challenges.picoctf.org:44573
{: .prompt-tip }


Logeate como Joe introduce una password y verás el siguiente mensaje en la siguiente web:
https://jupiter.challenges.picoctf.org/problem/44573/login

> I'm sorry Joe's password is super secure. You're not getting in that way. 

Cualquier otro login te lleva a la web con el siguiente mensaje
https://jupiter.challenges.picoctf.org/problem/44573/flag
> Success: You logged in! Not sure you'll be able to see the flag though. 

Bueno si es una password tan segura como dice debería conseguirse de alguna otra manera.... 
Debería intentarlo con la fuerza bruta? 
Por una parte se ve que el usuario indicado Joe, nos deriva a una zona distinta, por otra la única pista que tenemos 
es que parece que solo se checkea el password para el usuario Joe

Ahora con Zap Proxy procedo a capturar la request y si nos fijamos vamos a encontrar que hay un parámetro "oculto"
que se envía por POST

``` html
POST https://jupiter.challenges.picoctf.org/login HTTP/1.1
host: jupiter.challenges.picoctf.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/x-www-form-urlencoded
Content-Length: 30
Origin: https://jupiter.challenges.picoctf.org
Connection: keep-alive
Referer: https://jupiter.challenges.picoctf.org/problem/44573/
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
Cookie: password=adfasdfasd; username=admin; admin=False
```
> admin=False 

Que pasa si manipulamos esto mismamente en firefox?
Vamos a almacenamiento cookies en las herramientas de developer de Firefox y editamos este valor en la cookie que enviamos en __True__ , luego refrescamos la página.

flag: **picoCTF{th3_c0nsp1r4cy_l1v3s_0c98aacc}**