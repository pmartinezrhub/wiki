---
title: SLQ Direct
date: 2025-05-21 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, sqldirect, psql]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>SQL Direct
Author: Mubarak Mikail / LT 'syreal' Jones
Description
Connect to this PostgreSQL server and find the flag! psql -h saturn.picoctf.net -p 52406 -U postgres pico Password is postgres
{: .prompt-tip }

Se nos indica utilizar la consola de psql para conetarnos a un servidor postgres y la base de dados __pico__. Una vez conectados podemos ejecutar los comandos típicos de postgres 

```
pablo☠office ~$ psql -h saturn.picoctf.net -p 52406 -U postgres -d pico
Contraseña para usuario postgres: 
psql (15.12 (Debian 15.12-0+deb12u2), servidor 15.2 (Debian 15.2-1.pgdg110+1))
Digite «help» para obtener ayuda.

pico=# \dt
        Listado de relaciones
 Esquema | Nombre | Tipo  |  Dueño   
---------+--------+-------+----------
 public  | flags  | tabla | postgres
(1 fila

pico=# \dt public
No se encontró relación llamada «public».
pico=# SELECT * FROM flags;
 id | firstname | lastname  |                address                 
----+-----------+-----------+----------------------------------------
  1 | Luke      | Skywalker | picoCTF{L3arN_S0m3_5qL_t0d4Y_73b0678f}
  2 | Leia      | Organa    | Alderaan
  3 | Han       | Solo      | Corellia
(3 filas)

```

flag: **picoCTF{L3arN_S0m3_5qL_t0d4Y_73b0678f}**

Primer contacto con psql.