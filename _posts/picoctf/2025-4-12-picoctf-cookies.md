---
title: Cookies
date: 2025-04-11 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, cookies, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Cookies
Author: madStacks
Description
Who doesn't love cookies? Try to figure out the best one. http://mercury.picoctf.net:17781/
{: .prompt-tip }

Lo primero que veo es que al interceptar con un proxy Zap obtengo un resultado siguiente
``` html
GET http://mercury.picoctf.net:17781/ HTTP/1.1
Host: mercury.picoctf.net:17781
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Connection: keep-alive
Cookie: name=-1
Upgrade-Insecure-Requests: 1
Priority: u=0, i
```

Parece que podemos introducir un tipo de galleta y si es reconocida por la págna nos saldrá un mensaje,
por ejemplo si introduces la cookie que viene en el placeholder **snickerdoodle**. Nos sale un mensaje.
```
I love snickerdoodle cookies!
```
Hay que prestan atención a las herramientas de desarrollador del navegador Crtl + Shift + I, así es como se puede ver como cambia el valor de la cookie cuando introducimos una galleta. Para  modificar el valor usando Firefox vamos a la parte de Almacenamiento
y en la cookie vamos modificando el valor de esta.

¿Bueno y que pasa si vamos cambiando el valor de la cookie, que tanto llama la atención? Pues las cookies suelen ser largas y codificadas
Pues al hacerlo van cambiando los mensajes, lo que quieres decir que hay dos formas de interrogar esta página, por el formulario
o parametrizando por la cookie. Esto no es común en el desarrollo web pero es para este ejercicio parece interesante al fin y al cabo. 

```
I love chocolate chip cookies!
```

El el valor nº 18 aparece la bandera. Por desgracia al usar una herramienta automática no he podido obtener resultados positivos a la hora de fuzzear la cookie. Solo lo conseguí con Zap pero forzando el valor 18 (con lo que no lo considero como bueno, el fuzzeo)

flag: **picoCTF{3v3ry1_l0v3s_c00k135_bb3b3535}**