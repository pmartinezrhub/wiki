---
title: 
date: 2025-07-15 19:00:00 +0200
categories: [linux]
tags: [bridge]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/Temporary_wooden_footbridge_leading_to_the_city_of_Luang_Prabang.jpg/1920px-Temporary_wooden_footbridge_leading_to_the_city_of_Luang_Prabang.jpg
    alt: 
---

## Instalar 
Antes de poder utilizar las interfaces puente necesitas instalar en tu sistema las utilidades necesarias, en Debian:

```shell
sudo apt install bridge-utils
```

## Interface de red puente 
Una interfaz de red puente (bridge) agrupa una o más interfaces físicas o virtuales para que actúen como si estuvieran en la misma red física. Funciona como un conmutador (switch) a nivel de enlace que permite que paquetes de red se pasen entre las interfaces conectadas. Conecta la interfaz física (enp3s0) con las interfaces de red virtuales de tus máquinas, permitiendo que las VMs aparezcan en la red local con direcciones IP propias
XEN/KVM/LXC/QEMU, etc se basan en mayor medida en este tipo de interfaces para su funcionamiento . 

``` shell
# Ponemos enp3s0 sin IP, solo para el bridge
auto enp3s0
iface enp3s0 inet manual

# Configuración del bridge br0
auto vmbridge
iface vmbridge inet static
    address 192.168.2.125
    netmask 255.255.255.0
    gateway 192.168.2.1
    bridge_ports enp3s0 # interface física
    bridge_stp off
    bridge_fd 0
    bridge_maxwait 0
```
En esta configuración de **/etc/network/interfaces** puedes ver que la cláusula **bridge_ports enp3s0** indica que interface física se conecta al puente.  

