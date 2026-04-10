---
title: Pivoting en Metasploit
date: 2026-4-1 10:00:00 +0200
categories: [hacking, ciberseguridad]
tags: [ciberseguridad, metasploit, pivoting ]     
image:
 image:
    path: https://upload.wikimedia.org/wikipedia/en/thumb/a/a7/Pivoting_series_logo.jpeg/250px-Pivoting_series_logo.jpeg
    alt: 
---

Toda la documentación de pivoting con Metasploit está en el siguiente enlace:
[https://docs.metasploit.com/docs/using-metasploit/intermediate/pivoting-in-metasploit.html](https://docs.metasploit.com/docs/using-metasploit/intermediate/pivoting-in-metasploit.html)

Pivotar es similar a crear un túnel SSH, se crea una conexión intermedia con una máquina a la que ya se tiene acceso



```shell
route add 10.10.0.15 255.255.255.0 1
```
Una vez tengamos por ejemplo una sessión de meterpreter podemos añadir la ruta por la que esta session va a 
utilizarse como sistema de pivotage. 

```
portfwd add -l 5555 -p 80 -r 10.10.10.25
``` 

