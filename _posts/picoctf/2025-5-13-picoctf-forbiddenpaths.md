---
title: Forbidden Paths
date: 2025-05-13 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, forbidden paths, explotación web, path transversal]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Forbidden Paths
Author: LT 'syreal' Jones
Description
Can you get the flag? We know that the website files live in /usr/share/nginx/html/ and the flag is at /flag.txt but the website is filtering absolute file paths. Can you get past the filter to read the flag? Here's the website.
{: .prompt-tip }

Si capturamos el request vemos como se hace el GET
``` html
GET http://saturn.picoctf.net:50455/divine-comedy.txt HTTP/1.1
host: saturn.picoctf.net:50455
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Priority: u=0, i

```
Si por ejemplo enviamos divine-comedy.txt estos serían los parámetros. 
filename=divine-comedy.txt&read=

Por otro lado el ejercicio nos da una pista de la ubicación /usr/share/nginx/html/  pues dice que esta en la raíz del sistema 
/flag.txt .Si contamos hacia arriba tenemos 4 directorios hacia el raíz. 
Así que probamos a introducir en el formultario lo siguiente.  
../../../../flag.txt

flag:  **picoCTF{7h3_p47h_70_5ucc355_e5fe3d4d}**

Sanitizar tus formularios u obtendrás un clásico de vulnerabilidad path traversal.