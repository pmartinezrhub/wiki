---
title: Linux basic network 
date: 2025-03-21 17:00:00 +0200
categories: [linux, redes] 
tags: [linux, redes]     # TAG names should always be lowercase
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d2/Internet_map_1024.jpg/1024px-Internet_map_1024.jpg
    alt: Internet_map_1024
---

Cuando Linus Torvalds publicó Linux no lo hizo en un CD, ni en un disquete, lo publicó en un servidor de su universidad. 
¿Que quiero decir con esto? Pues algo tan evidente como que Linux evolucionó con Internet y creció con él. 
La configuración básica de red en Linux debe ser el primer objetivo a aprender por un administrador de sistema Linux 
pues si sabe configurar el acceso a la red de redes entonces ya no solo se tiene un sistema, se tiene un sistema con acceso a la red!! 

No cantemos victoria, todavía no sabemos si tienes acceso a Internet
Utilízalo como ejemplo o apunte, de todas formas deberías tener conocimiento de que es una IP, una puerta de enlace
 , la máscara de red y DHCP para saber que estás haciendo.


<h4>Resumen comando ip (el comando ifconfig empieza a ser obsoleto)</h4>
``` shell
ip addr add 192.168.1.100/24 dev eth0 #añadir una ip
ip addr del 192.168.1.100/24 dev eth0 #eliminar una ip
ip link set eth0 up
ip link set eth0 down
ip r // ip route show 
ip route add 192.168.2.0/24 via 192.168.1.1 dev eth0
ip route del 192.168.2.0/24
```

Este archivo contiene la configuración de red por norma general a no ser que se utilice otro medio como netplan u otros.
<h4>/etc/network/interfaces</h4>
Ejemplo config inalámbrica y ethernet de dos interfaces en /etc/network/interfaces
``` shell
auto wlan0
iface wlan0 inet dhcp
wpa-essid WifiHome
wpa-psk ClaveWifiHome


auto enp3s0
iface enp3s0 inet static
address 172.10.10.25
netmask 255.255.255.0
```


PD: Como seguro que se me olvida algo, este post se ampliará o modificará en el futuro