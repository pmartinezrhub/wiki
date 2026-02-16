---

title: 10 Comandos en Linux que me gustan
date: 2025-03-22 17:00:00 +0200
categories: [linux, software]
tags: [linux, comandos]     # TAG names should always be lowercase
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/8/82/Gnu-bash-logo.svg/200px-Gnu-bash-logo.svg.png
    alt: Shell
---

Hay muchos comandos, diría que no puedes acordarte de todos, por eso no voy a hacer el tipico artículo del estilo
"los 10 comandos más útiles en linux", tampoco voy a poner comandos básicos como 'cp' o 'ls', solo comandos que descubres o "redescubres",
pues muchos de estos comandos los habrás usado para alguna tarea y no conocías su verdadero propósito.


### 1 - pstree
pstree imprime un diagrama similar a un árbol de los procesos actualmente en ejecución, 
que muestra cómo los procesos están relacionados entre sí de manera jerárquica. Mucho más humano que hacerlo con ps
además te dará una visión de servicios que están corriendo
``` shell
pstree
```
### 2 - xargs 
Puede ser que hayas visto este comando en algún script, el propósito es que xargs ejecute un comando tantas veces como la entrada standard
se lo permita, es un conector muy bueno entre scripts y/o programas. Con imaginación este comando es superutil

ex:
``` shell
ls *.txt | xargs wc -l
```
### 3 - man  
man (manual) es la guía de todas las opciones que un comando proporciona, siempre es buena idea explorar los archivos man de cada comando 
antes de ejecutarlo, para explorar sus opciones más allá de los usos comunes del mismo. Más que un descubrimiento, esto es un consejo,
, en estos tiempos donde la gente copia y pega lo que una IA les dice sin entender que están haciendo 
podrías sorprenderte de que has usado un comando mil veces y no conoces muchas de sus funcionalidades. 

``` shell
man tee
```
### 4 - tee 
tee - lee de la entrada estándar y escribe en la salida estándar y en ficheros. 
Tal y como reza el manual de tee, este comando te permite mostrar la salida standard mientras la envías a un fichero. 
Es común que un administrador utilize el símbolo ">" para indicar que la salida standard por ejemplo:
``` shell
tail -f /var/log/nginx/access.log > nginx.log
```
Parecerá una tontería pero es posible que el administrador quiera revisar los logs mientras se envían a un fichero así que tee
te lo va a permitir:
``` shell
tail -f /var/log/nginx/access.log | tee nginx.log
```

### 5 - watch 
watch es un comando que repetira los comandos que hayamos pasado como parametros, por ejemplo se puede realizar una monitorización
de cualquier acción que requiera ser repetida con periodicidad
``` shell
watch -n 1 mysqladmin --user=<user> --password=<password> processlist
```

### Hasta aquí los comandos que vienen por defecto en sistemas Linux pero que no son muy utilizados
ahora vamos con los que suelen estar disponibles para instalar en casi todas las distribuciones

### 6 - socat 
socat es como un netcat con esteroides, es un conector multipropósito tiene soporte para múltiples protocolos: TCP, UDP, SCTP, UNIX sockets, etc.
Soporta cifrado con OpenSSL, además puede usarse en scripts para manipular flujos de datos. 
``` shell
socat TCP-LISTEN:4444 EXEC:/bin/bash
```
### 7 - bat 
bat o batcat(Debian) es como cat pero con capacidadees de resalte de texto, por ejemplo asignará colores a las palabras reservadas de los lenguajes
de programación, numerará la línea y además tiene capacidades como el comando less
``` shell
batcat archivo.py
```

### 8 - grc 
grc - Esta utilidad te permite ver los logs y los colorea, haciendo su visualización más fácil. No es que sea una revolución 
pero si tienes que monitorizar unos logs es perfecto.
``` shell
grc tail -f  /var/log/nginx/access.log 
```
### 9 - toilet 
toilet – Convierte texto en arte ASCII colorido. Leí una vez que no es un comando útil, pero tiene varios propósitos.
El primero es que puedes dejar un mensaje en la pantalla grandecito, por ejemplo: "NO TOCAR", el cual en entornos IT
puede ser de gran utilidad y ahorro ecológico, se puede utilizar con conky u otras utilidades para mejorar la usabilidad
o la apariencia de un escritorio o simplemente por el gusto de hacer "arte ASCII" 
``` shell
watch "echo 'NO TOCAR, ESTE PC ESTÁ TRABAJANDO' | toilet  -k"  
```

### 10 - mussh 
mussh - MUltihost SSH Wrapper . Esta utilidad te permite enviar comandos a multiples host por SSH. 
``` shell
mussh -h 192.168.100.{1..50} -i ~/.ssh/servers.pem -m -t 10 -c cat /etc/debian_version
```
### Menciones especiales 
Me gustaría haber hablado sobre otras utilidades del terminal, cabe mencionar los siguientes comandos:
sudo !! (cuando te das cuenta de que no eres root), tmux, screen, bmon, conky, etc. 
pero no encajarían muy bien en el objetivo de este post.

