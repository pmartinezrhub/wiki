---
title: Linux basic network 
date: 2025-03-21 17:00:00 +0200
categories: [linux, redes] 
tags: [linux, redes]     # TAG names should always be lowercase
author: pablo
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/d/d2/Internet_map_1024.jpg/1024px-Internet_map_1024.jpg
    alt: Internet_map_1024
---

Cuando Linux Tolvard pubicó Linux no lo hizo en un CD, ni en un disquete, lo publicó en un servidor de su universidad. <br>
¿Que quiero decir con esto? Pues algo tan evidente como que Linux evolucionó con Internet y creció con él. <br>
La configuración básica de red en Linux debe ser el primer objetivo a aprender por un administrador de sistema Linux <br>
pues si sabe configurar el acceso a la red de redes entonces ya no solo se tiene un sistema, se tiene un sistema con acceso a la red!! <br>
No cantemos victoria, todavía no sabemos si tienes acceso a Internet<br>
Utilízalo como ejemplo o apunte, de todas formas deberías tener conocimiento de que es una IP, una puerta de enlace
 , la máscara de red y DHCP para saber que estás haciendo.<br>


<h4>Resumen comando ip (el comando ifconfig empieza a ser obsoleto)</h4>
ip addr add 192.168.1.100/24 dev eth0 #añadir una ip<br>
ip addr del 192.168.1.100/24 dev eth0 #eliminar una ip<br>
ip link set eth0 up<br>
ip link set eth0 down<br>
ip r // ip route show <br>
ip route add 192.168.2.0/24 via 192.168.1.1 dev eth0<br>
ip route del 192.168.2.0/24<br>

Este archivo contiene la configuración de red por norma general a no ser que se utilice otro medio como netplan u otros.
<h4>/etc/network/interfaces</h4>
Ejemplo config inalámbrica y ethernet de dos interfaces en /etc/network/interfaces<br>

auto wlan0<br>
iface wlan0 inet dhcp<br>
wpa-essid WifiHome<br>
wpa-psk ClaveWifiHome<br>

<br>
auto enp3s0<br>
iface enp3s0 inet static<br>
address 172.10.10.25<br>
netmask 255.255.255.0<br>

<br>
<br>
PD: Como seguro que se me olvida algo, este post se ampliará o modificará en el futuro