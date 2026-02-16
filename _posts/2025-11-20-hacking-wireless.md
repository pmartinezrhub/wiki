---
title: Hacking Wireless
date: 2025-11-20 11:00:00 +0200
categories: [ciberseguridad, software]
tags: [aircrack, wpa2, fluxion]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/c/c3/Aircrack-ng-new-logo.jpg
    alt:  
---
## Acerca de este post
Este post es solo para recordar las herramientas más importante, no creo que me detenga en explicar en profundidad las herramientas, pero si podría servir como apuntes
Los repositorios a la herramietas aquí descritas están aquí => [hacktools/#wifi](../hacktools/#wifi)
## Aircrack-ng
Aircrack-ng no es un programa es una "suite". Un conjunto de programas y utilidades dedicadas al hacking de redes inalámbricas
Antes de tirarte a la piscina tengo que anticipar dos cosas fundamentales para usar Aircrack:
- Necesitas una tarjeta inalámbrica, obviamente, dicha tarjeta necesita un driver que permita el modo promiscuo. 
- El modo promiscuo normalmente se puede obtener como un driver en GNU/Linux, supongo que se puede usar en Windows si encuentras los drivers y la tarjeta adecuada,
pero aquí doy por hecho que se usará Linux.  

## Capturar paquetes wireless
Poner una tarjeta en modo monitor y empezar a guarda la escucha

``` shell
airmon-ng start wlo
airodump-ng mon0 -c 11 --essid MIWIFI -w output --wps --manufacturer 
```

## Cracking de contraseñas
Vale ya hemos capturado y podriamos tirar de aircrack-ng para intentar el crackeo, pero esto es muy ineficiente. 

``` shell
aircrack-ng output.cap -w wordlist/diccionario.txt
```

### Conversion de archivos .cap a .hccap 
Podemos usar las **hcxtools**

``` shell
hcxpcapngtool MIWIFI -o output.hccap
```
Ahora podemos usar [hashcat-cuda](../hashcat-cuda) para crackear la contraseña con ayuda de una GPU que mucho más eficiente que hacerlo con **aircrack-ng**

## Ataques al WPS
Ataques al sistema WPS. 
Hay varios ataques conocidos a los sistemas WPS que proporcionan algunos routers inalámbricos
Identificar redes con WPS

wash es parte de **reaver**.

``` shell
wash -i mon0 #reaver
aircrack-ng --wps #con aircrack
```
atacar el WPS de una red con reaver

``` shell
reaver -i mon0 -b 00:00:00:00:00:00
```

## Fluxion
Ataque Evil twin. Ataque MiTM Wireless
Fluxion es un programa que ejecuta una serie de scripts para realizar un tipo de ataque de gemelo malvado y de ingenería social.
Automatiza algunos casos de uso de Aircrack. 

![fluxion](https://github.com/FluxionNetwork/fluxion/raw/master/logos/logo.jpg)

Fluxion funciona en varios pasos para llevar a cabo el ataque de gemelo malvado.

- Crea una red que suplanta a la red objetivo
- Puede deautenticar a los clientes que esten en la red objetivo, con la intención de que se conecten a la red falsa (jamming)
- Ofrece un portal web donde solicita la clave de la red, imitando a un formulario de un router
- Una vez comprueba si esa contraseña funciona, desmonta la trampa. 


