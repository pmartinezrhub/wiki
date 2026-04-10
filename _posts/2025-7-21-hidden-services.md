---
title: Hidden Services
date: 2025-07-21 11:00:00 +0200
categories: [ciberseguridad, redes]
tags: [hidden services, tor]     
image: https://upload.wikimedia.org/wikipedia/commons/1/14/Wat_is_Tor_%28The_onion_routing%29%3F.png
---

## Hidden services

Los servicios ocultos en la deepweb. Son servicios de red que no puedes acceder utilizando una IP, sino que 
necesitas un software conocido como Tor proxy. Hay muchos tutoriales en Internet de como publicar un hidden service pero es algo bastante sencillo. Si no sabes nada sobre Tor puedes leer también esta introducción [La red de Tor](../tornetwork)

### Descriptores DHTs y HSDirs

Un descriptor es un archivo que contiene la información necesaria para que los clientes puedan conectarse a un servicio oculto en la red Tor (dirección .onion). Este archivo está firmado criptográficamente, se publica en la red, y solo puede ser recuperado si se conoce previamente la dirección .onion correspondiente.

Cuando un servicio oculto se pone en línea, genera un par de claves criptográficas (pública y privada). La dirección .onion es una representación codificada de la clave pública, lo que garantiza su autenticidad y anonimato.

El descriptor se distribuye a través de la red Tor en ubicaciones determinadas matemáticamente, calculadas a partir de la dirección .onion y el momento actual. Estas ubicaciones corresponden a ciertos nodos conocidos como Hidden Service Directories (HSDirs). Para mejorar la disponibilidad y resiliencia del servicio, el descriptor se publica en varios HSDirs simultáneamente.

DHT = Distributed Hash Table. Es un sistema descentralizado de almacenamiento y búsqueda de datos, donde cada nodo de la red almacena una parte de la información y colabora para resolver consultas.Muy usado en tecnologías como BitTorrent y Tor.

Todo estos elementos forman parte del protocolo Rendezvous y la explicación técnica la puedes encontrar el propia página del proyecto tor [https://community.torproject.org/es/onion-services/overview/](https://community.torproject.org/es/onion-services/overview/)

🔒 ¿Qué significa esto en términos de seguridad en las comunicaciones?
- El servicio no es visible para nadie, salvo quien tenga la dirección .onion.
- Solo si conoces la dirección puedes calcular cómo y dónde encontrar el descriptor.
- Es como un buzón cifrado sin nombre: solo quien tiene la llave puede saber dónde mirar.

## Configuración servicio oculto

Como configurar uno o más servicio onion en el fichero **/etc/tor/torrc**. Supongamos que tenemos la configuración típica donde hemos descomentado la línea de ejemplo que trae este fichero solo para publicar en la deepweb nuestro servidor web.  

``` shell
 HiddenServiceDir /var/lib/tor/onion_service/
 HiddenServicePort 80 127.0.0.1:80
```
**HiddenServiceDir** Simplemente indica el directorio donde se generará el hostname y la claves del servicio
**HiddenServicePort** Indica por un lado 80 es el puerto donde se publica en la red de tor y la dirección 127.0.0.1:80 es donde se encuentra escuchando el servicio actualmente en el sistema

Una vez tenemos nuestra configuración salvada se generará **/var/lib/tor/xmpp_onion_service/** al reiniciar el servicio y se cargue la nueva configuración. Por ejemplo queremos añadir el servicio de XMPP como servicio oculto, este servicio utilizaré dos puertos puertos, pues lo definimos de la siguiente manera:

``` shell
HiddenServiceDir var/lib/tor/xmpp_hidden_service
HiddenServicePort 5222 # cliente-servidor
HiddenServicePort 5269 # federación
```

Luego reiniciamos el servicio Tor
```shell
sudo systemctl restart tor
```
## Comprobar servicio

Para comprobar que todo ha ido bien podemos hacer varias cosas. 
- Una comprobar que se han generado las carpetas con las claves y el archivo hostname que contendrá el nombre del  dominio generado seguido de **.onion**
``` shell
cat /var/lib/tor/hidden_service/hostname 
fwyyvyi6hobn3kpfyq47msxz4wgy7xjfri2ssykvsy4m7fzrvkbe64yd.onion
cat /var/lib/tor/xmpp_hidden_service/hostname 
ufidtyp6uofryknd3ksiokoerr2gcbdk7vqbrmcxsnym3fzdxvfuicad.onion
```
- Dos y la más obvia conectarnos al hidden service con una aplicación que nos permita usar Tor como proxy o al menos que deje usar un Socks5 como proxy. Normalmente Tor escucha en la dirección 127.0.0.1:9050

## Ciberseguridad en hidden services

Los hidden services en teoría hacen casi imposible la localización IP del servidor que está alojando el servicio, no obstante, el anonimato no garantiza la seguridad de los servicios que se publiquen en la red de tor, especialmente si se publica la dirección .onion, es más el hecho de estar en al red de Tor permite que los ataques se realicen de forma anónima y dificulta las medidas de protección. 




