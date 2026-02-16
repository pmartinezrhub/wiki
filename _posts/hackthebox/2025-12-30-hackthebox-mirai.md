---
title: Mirai
date: 2025-12-30 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, granny ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/4ef1ea77e69185063d4200d7d0142baa.png
    alt: Hack The Box
---

> Mirai
Linux · Easy 

## Task 1

What is the name of the service running on TCP port 53 on Mirai? Don't include a version number.

```shell
┌──(pmartinezr㉿kali)-[~/htb/mirai]
└─$ nmap -p 53 -sVSC 10.129.29.47              
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-30 20:12 CET
Nmap scan report for 10.129.29.47
Host is up (0.044s latency).

PORT   STATE SERVICE VERSION
53/tcp open  domain  dnsmasq 2.76
| dns-nsid: 
|_  bind.version: dnsmasq-2.76

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 28.18 seconds
```

> dnsmasq

## Task 2

What unusual HTTP header is included in the response when visiting the service on port 80?

Si investigamos la respuesta del servidor aparece algo que parece interesante ```X-Pi-hole```, esto lo hice usando 
Zap proxy.

> X-Pi-hole

## Task 3

What relative path on the webserver presents the Pi-hole dashboard?

``` shell
┌──(pmartinezr㉿kali)-[~/htb/mirai]
└─$ dirsearch -u http://10.129.29.47/
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )
                                                                                                                                                            
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/pmartinezr/htb/mirai/reports/http_10.129.29.47/__25-12-30_20-24-36.txt

Target: http://10.129.29.47/

[20:24:36] Starting:                                                                                                                                        
[20:24:45] 301 -    0B  - /admin  ->  http://10.129.29.47/admin/          

```

> /admin

## Task 4

What was the default username on a Raspberry Pi device?

Si has tenido una ```Raspberry Pi``` sabrás que es ```pi``` 

> pi

## Task 5

What is the default password for the pi user?

Y la password ```raspberry```

> raspberry

## Submit User Flag

Submit the flag located on the pi user's desktop.

``` shell
pi@raspberrypi:~/Desktop $ cat user.txt 
ff837707441b257************
```

> ff837707441b257************

## Task 7

Can the pi user run any command as root on Mirai?

``` shell
pi@raspberrypi:~/Desktop $ sudo -l
Matching Defaults entries for pi on localhost:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User pi may run the following commands on localhost:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: ALL
```
De hecho puede ejecutar cualquier cosa como root

> yes

## Task 8 

The flag-less root.txt file mentions that it's on the USB stick. What is the mountpoint for a device that is labeled as a USB stick on this host?

``` shell
pi@raspberrypi:~/Desktop $ sudo su
root@raspberrypi:/home/pi/Desktop# cd /media/usbstick/
root@raspberrypi:/media/usbstick# ls
damnit.txt  lost+found
root@raspberrypi:/media/usbstick# cat damnit.txt 
Damnit! Sorry man I accidentally deleted your files off the USB stick.
Do you know if there is any way to get them back?

-James

```
La respuesta parece obvia sin embargo parece que quieren alargar el reto.

> /media/usbstick

## Task 9

What is the full path to the device that represents the raw USB media on Mirai?

``` shell
root@raspberrypi:/media/usbstick# lsblk 
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   10G  0 disk 
├─sda1   8:1    0  1.3G  0 part /lib/live/mount/persistence/sda1
└─sda2   8:2    0  8.7G  0 part /lib/live/mount/persistence/sda2
sdb      8:16   0   10M  0 disk /media/usbstick
sr0     11:0    1 1024M  0 rom  
loop0    7:0    0  1.2G  1 loop /lib/live/mount/rootfs/filesystem.squashfs
```

> /dev/sdb

## Task 10

When files are deleted from a drive, is the memory definitely immediately overwritten with something else?

> no

## Submit Root Flag

Submit the flag located on the USB device.

``` shell
root@raspberrypi:/media/usbstick/lost+found# strings /dev/sdb
>r &
/media/usbstick
lost+found
root.txt
damnit.txt
>r &
>r &
/media/usbstick
lost+found
root.txt
damnit.txt
>r &
/media/usbstick
2]8^
lost+found
root.txt
damnit.txt
>r &
3d3e483143ff12************
Damnit! Sorry man I accidentally deleted your files off the USB stick.
Do you know if there is any way to get them back?
-James
```
Como bien indica la tarea 10 borrar un archivo no significa que automáticamente desaparezca, desaparece el puntero en el 
sistema de ficheros pero no se borra realmente, al menos hasta que se reescriba el espacio, si bien esto con la llegada de los discos
SSD ha variado un poquito. 

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/64)