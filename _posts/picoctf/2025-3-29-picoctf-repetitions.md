---
title: Repetitions
date: 2025-03-29 19:00:00 +0200
categories: [writeup, picoctf]
tags: [ctf, picoctf, base64]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

Repetitions
>
Author: Theoneste Byagutangaza
Description
Can you make sense of this file? Download the file [here](https://artifacts.picoctf.net/c/474/enc_flag).
{: .prompt-tip }

Al principio estaba un poco perdido con este reto, pero el reto se llama "Repetitions", así que después de el primer decodificado, segundo y 
tercero ya te vas dando cuenta que la decodificación resta peso al tamaño de los datos, después de 6 iteraciones aparece la flag.
``` shell
wget https://artifacts.picoctf.net/c/474/enc_flag
pablo☠office repetitions$ cat enc_flag                                   
VmpGU1EyRXlUWGxTYmxKVVYwZFNWbGxyV21GV1JteDBUbFpPYWxKdFVsaFpWVlUxWVZaS1ZWWnVh
RmRXZWtab1dWWmtSMk5yTlZWWApiVVpUVm10d1VWZFdVa2RpYlZaWFZtNVdVZ3BpU0VKeldWUkNk
MlZXVlhoWGJYQk9VbFJXU0ZkcVRuTldaM0JZVWpGS2VWWkdaSGRXCk1sWnpWV3hhVm1KRk5XOVVW
VkpEVGxaYVdFMVhSbFZhTTBKUFdXdGtlbVF4V2tkWGJYUllDbUY2UWpSWmEyaFRWakpHZEdWRlZs
aGkKYlRrelZERldUMkpzUWxWTlJYTkxDZz09Cg==
pablo☠office repetitions$ cat enc_flag | base64 -d                       
VjFSQ2EyTXlSblJUV0dSVllrWmFWRmx0TlZOalJtUlhZVVU1YVZKVVZuaFdWekZoWVZkR2NrNVVX
bUZTVmtwUVdWUkdibVZXVm5WUgpiSEJzWVRCd2VWVXhXbXBOUlRWSFdqTnNWZ3BYUjFKeVZGZHdW
MlZzVWxaVmJFNW9UVVJDTlZaWE1XRlVaM0JPWWtkemQxWkdXbXRYCmF6QjRZa2hTVjJGdGVFVlhi
bTkzVDFWT2JsQlVNRXNLCg==
pablo☠office repetitions$ cat enc_flag | base64 -d | base64 -d           
V1RCa2MyRnRTWGRVYkZaVFltNVNjRmRXYUU5aVJUVnhWVzFhYVdGck5UWmFSVkpQWVRGbmVWVnVR
bHBsYTBweVUxWmpNRTVHWjNsVgpXR1JyVFdwV2VsUlZVbE5oTURCNVZXMWFUZ3BOYkdzd1ZGWmtX
azB4YkhSV2FteEVXbm93T1VOblBUMEsK
pablo☠office repetitions$ cat enc_flag | base64 -d | base64 -d | base64 -d 
WTBkc2FtSXdUbFZTYm5ScFdWaE9iRTVxVW1aaWFrNTZaRVJPYTFneVVuQlpla0pyU1ZjME5GZ3lV
WGRrTWpWelRVUlNhMDB5VW1aTgpNbGswVFZkWk0xbHRWamxEWnowOUNnPT0K
pablo☠office repetitions$ cat enc_flag | base64 -d | base64 -d | base64 -d | base64 -d
Y0dsamIwTlVSbnRpWVhObE5qUmZiak56ZEROa1gyUnBZekJrSVc0NFgyUXdkMjVzTURSa00yUmZN
Mlk0TVdZM1ltVjlDZz09Cg==
pablo☠office repetitions$ cat enc_flag | base64 -d | base64 -d | base64 -d | base64 -d | base64 -d
cGljb0NURntiYXNlNjRfbjNzdDNkX2RpYzBkIW44X2Qwd25sMDRkM2RfM2Y4MWY3YmV9Cg==
pablo☠office repetitions$ cat enc_flag | base64 -d | base64 -d | base64 -d | base64 -d | base64 -d| base64 -d
picoCTF{base64_n3st3d_dic0d!n8_d0wnl04d3d_3f81f7be}                                                                                                                                                           
```
flag: **picoCTF{base64_n3st3d_dic0d!n8_d0wnl04d3d_3f81f7be}**

Trivial pero hay que insistir. 
