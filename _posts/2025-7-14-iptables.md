---
title: Iptables
date: 2025-07-14 17:00:00 +0200
categories: [linux, redes]
tags: [iptables, netfilter]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/1/11/Iptablesfb.png/250px-Iptablesfb.png
    alt: 
---

## Acerca de este artículo
Solo está enfocado en describir la lógica fundamental de iptables, no pretende ser una clase teórica. Iptables fue introducido en la version 3.13 del kernel de Linux y desde entonces podemos aprovechar cualquier Linux para construir un firewall. 

## Netfilter
Netfilter es el nombre de la comunidad de personas que mantienen este proyecto. 
Como ya dijimos viene en el kernel de Linux pero si quieres manejar correctamente iptables instala la persistencia, esta ofrece un sistema para almacenar las reglas una vez estemos conformes con ellas. 

``` shell
apt install iptables-persistent
``` 

## Lógica de las reglas
### Tablas de enrutado

``` shell
              +-----------------------------+
              |       Paquete entra         |
              +-------------+---------------+
                            |
                            v
                   +-----------------+
                   | TABLE: filter   |
                   +-----------------+
                            |
          +----------------+------------------+
          |                                   |
          v                                   v
   +-------------+                   +----------------+
   | CHAIN: INPUT| <--- tráfico ---> | CHAIN: FORWARD |
   +-------------+                   +----------------+
          |
          v
   +-------------+
   | Aplicación  |
   +-------------+

                            ^
                            |
                   +-----------------+
                   | TABLE: nat      |
                   +-----------------+
          +--------+--------+--------+--------+
          |                 |                 |
          v                 v                 v
   +----------+      +------------+     +-----------+
   | PREROUTING|      | POSTROUTING|     | OUTPUT    |
   +----------+      +------------+     +-----------+

```
El camino que recorre el paquete se decide en la tabla filtro y puede acabar en varios sitios. Pero una forma de resumirlo sería que el paquete acaba en INPUT o en FORWARD, si quiere se requiere un procesamiento anterior y/o posterior tendrá que pasar por la tabla nat o mangle.
Más adelante pondre unos ejemplos de uso de reglas de PREROUTING Y POSTROUTING. OUTPUT es cuando el paquete se envía afuera.

### Reglas de enrutados

🔗 Cadenas 

    INPUT       (filter):           Tráfico entrante al sistema.

    OUTPUT      (filter):           Tráfico generado por el sistema.

    FORWARD     (filter):           Tráfico que pasa por el sistema, pero no está destinado a él.

    PREROUTING  (nat, mangle, raw)  El paquete se procesa antes de ser enrutado.

    POSTROUTING (nat, mangle)       El paquete se procesa después del enrutamiento.

    OUTPUT      (nat, mangle)       Paquetes salientes antes de ser enrutados.

    INPUT       (mangle, raw)       Paquetes entrantes antes de ser procesados por el sistema.


✅ Acciones (Objetivos)

    ACCEPT: aceptar el paquete.

    DROP: descartar el paquete (silenciosamente).

    REJECT: rechazar con mensaje.

    DNAT, SNAT: cambiar dirección IP origen/destino.

    LOG: registrar información del paquete.

### Filosofías de las políticas de firewall
- Se permite todo lo que no se prohibe
- Se prohibe todo lo que no está permitido

Puede haber organizaciones que sigan una política permisiva sobre las aplicaciones que utilizan sus usuarios, etc y otras que justo hacen lo contrario, prohiben todo lo que no se haya permitido. Esto en términos de red se traduce en que puertos están abiertos hacia afuera y hacia adentro de la red. 
Por supuesto la mayoría de firewalls, ya te habrás dado cuenta de que el router de tu casa sigue en parte la lógica de prohibir todo lo que no está permitido, pues para que alguien se conecte a nuestra computadora tenemos que "abrir el puerto en el router" (técnicamente es una redirección de puerto), por lo tanto la política de acceso desde el exterior es restrictiva, sin embargo conectarnos desde el navegado o cualquier aplicación hacia Internet no tiene restricciones, casi nunca, en ese caso de uso es muy permisiva. 

Los scripts de firewall, suelen ser archivos que definen las reglas que se van a utilizar haciendo uso del comando iptables
Para este caso voy a proponer un script base de firewall iptables para empezar a jugar con el.

```  shell
#!/bin/bash
# basic_firewall.sh

# Limpiar reglas existentes
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X

# Políticas predeterminadas
iptables -P INPUT DROP 
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT 

# Permitir tráfico local (loopback)
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o lo -j ACCEPT

# Permitir conexiones ya establecidas o relacionadas y respuesta a solicitudes
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s -j ACCEPT
iptables -A INPUT -p tcp --syn -m limit --limit 1/s -j ACCEPT

# Registrar paquetes descartados (limitado) 
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables-dropped: " --log-level 4

# ejemplo abrir un servicio de ssh para la tener acceso a nuestra máquina desde la red local
iptables -A INPUT -p tcp --dport 22 -s 192.168.0.1/24 -m conntrack --state NEW,ESTABLISHED -j ACCEPT

echo "Reglas del firewall aplicadas correctamente."
```
## INPUT

Posiblemente la regla más buscada en Internet cuando alguien instala su primer servidor GNU/Linux. Como se intuye, INPUT es una regla de entrada y por lo tanto es común cuando queremos definir un puerto al que queremos permitir acceso.

```shell
iptables -A INPUT -p tcp --dport 22 -s 192.168.0.1/24 -m conntrack --state NEW,ESTABLISHED -j ACCEPT
```
- -A (append) INPUT	Agrega esta regla al final de la cadena INPUT (para tráfico que entra al sistema).
- -p tcp (protocol)	Se aplica solo a paquetes TCP.
- --dport 22 (destination port) Aplica a paquetes destinados al puerto 22 (por defecto: SSH).
- -s 192.168.0.1/24 (source)	Solo aplica a paquetes provenientes del rango 192.168.0.0 a 192.168.0.255.
- -m (match)conntrack --state NEW,ESTABLISHED	Se aplica si la conexión es nueva (NEW) o ya está establecida (ESTABLISHED).
- -j  (jump) ACCEPT	Permite el tráfico que cumpla esas condiciones.

O dicho de otro modo, añade esta regla TCP puerto 22 con origen de la red 192.168.0.1/24 y con (conntrack) con salto a ACCEPT

## El orden es importante.

Por ejemplo:

``` shell
iptables -A INPUT -s 192.168.1.100 -j DROP
iptables -A INPUT -s 192.168.1.100 -j ACCEPT
```

>Resultado:
El paquete de 192.168.1.100 será descartado, aunque después haya una regla que dice ACCEPT, porque la regla DROP fue evaluada en primer lugar. 
Esta ejecutó su eliminación, así que es importante el orden de las reglas. La primera regla que coincida con el paquete será la que se ejecute, y las demás se ignorarán.
{: .prompt-tip }

## Políticas predeterminadas
Determinan como por defecto se va a comportar tu firewall. Cuanto más restrictiva sea la política más complicada va a ser la configuración. Se hace uso del flag -P --policy

``` shell
iptables -P INPUT DROP 
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT 
```

## Ejemplos Cadenas

### FORWARD
Se utiliza para controlar el tráfico que atraviesa el equipo, es decir, aquel que se enruta entre diferentes interfaces de red, y no el tráfico destinado al propio equipo o generado por él. Esta regla es especialmente útil en escenarios donde el sistema actúa como router entre dos o más redes, como por ejemplo al conectar una subred a Internet. 
Esta regla permite el reenvío de paquetes provenientes de la interfaz eth1 hacia eth0, siempre que se trate de conexiones ya establecidas o relacionadas. Es común usarla en conjunto con reglas que permiten nuevas conexiones salientes desde la red interna, y luego permitir el retorno del tráfico con reglas como esta.


``` shell
iptables -A FORWARD -i eth1 -o eth0 -m state --state ESTABLISHED,RELATED -j ACCEPT
```

Ejemplos de reglas tabla NAT(Network Address Traslation):

### PREROUTING
Enrutado para redirigir a otro host las peticiones al puerto 80 (http). El paquete será redirigido a otra máquina.

``` shell
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.1.100:8080

```

### POSTROUTING
Se usa para modificar paquetes justo antes de que salgan del equipo.Este ejemplo se hace uno de MASQUERADE que hará que el paquete de una red privada, salga por la inteface pública. Es muy útil cuando usamos dos o más interfaces de red. 

``` shell
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

## Resumen de comandos

``` shell
iptables -nvL #lista las reglas de INPUT, OUTPUT y FORWARD
iptables -t nat -nvL # listar las reglas de mangle  PREROUTING INPUT OUTPUT Y POSTROUTING
iptables -L --line-numbers # listar las reglas , acordarse de -t nat para nat 
iptables -D INPUT 3 # borrar una regla, iptables -t nat -D PREROUTING 2
iptables-save > /etc/iptables/rules.v4 # salvar las reglas a un archivo
iptables -A INPUT -s 222.35.38.25/32 -j DROP # bloquear una IP
netfilter-persistent save # salva las regals actuales a los archivos de sistemas correspondientes
```

## Troubleshooting

 En alguna situación puede pasar algo malo con la red....

 ```shell
 systemctl restart networking
 systemctl status networking
  ```

Buscando un poquito en Internet encontrarás miles de ejemplos de todo tipo de reglas, además hay un montón de herramientas que
se basan en iptables para hacer más segura la máquina que hostean sevicios como **Fail2ban**. En cualquier caso siempre es buena idea pensar que esta característica de GNU/Linux es muy potente, flexible y configurable. 
Se pueden aplicar reglas anti DDos en el propio host y por supuesto reglas de bastionado para servidores, especialmente en sistemas críticos aunque puedes usarlo perfectamente para securizar tu Linux de escritorio. 

Como siempre escribo este artículo queda sujeta a futuras modificaciones, correciones y/o mejoras. 