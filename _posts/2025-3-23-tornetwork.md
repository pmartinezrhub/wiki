---
title: La red de Tor 
date: 2025-03-23 16:00:00 +0200
categories: [redes]
tags: [darknet, tor, network, onion]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/b/b1/Tor_Circuit_Diagram.svg
    alt: Tor Circuit
---

#### Un poco de historia
Tor nació en el Laboratorio de Investigación Naval de los Estados Unidos (NRL): La tecnología detrás de Tor fue desarrollada inicialmente por el matemático Paul Syverson y los científicos informáticos Michael G. Reed y David Goldschlag en el NRL a mediados de la década de 1990. Su objetivo era proteger las comunicaciones gubernamentales de los Estados Unidos, especialmente las operaciones de inteligencia.

#### La defensa en profundidad, la seguridad en capas
La información en la red de Tor viaja encriptada, en varias capas, de ahí que se le llame "The Onion Router", "El enrutador cebolla".
Una capa por cada nodo de la red de Tor por el que viaja un paquete en la red de Tor esto no elude otros protocolos de seguridad como el **HTTPS** y que suponen añadir otras capas de cifrado a la comunicación. 
 
#### Tor proxy
Es el software que se encarga de conectarse a la red de Tor. Si ejecutas el navegador de Tor también estás ejecutando un proxy de Tor
Este proxy se encarga de preguntarle a la red de Tor que nodos de Tor tiene a su alcance.

#### Como asegura el anonimato
Tor proxy elige a los host con los que va a establecer un circuito. Cada circuito se compone de un nodo de entrada **G** (guard), un nodo intermedio **M** (middle) y un nodo de salida **E** (exit node).  
Cada nodo le entrega a proxy su clave pública. 
Supongamos que Alice quiere enviarle un mensaje a Bob tal que el circuito completo del paquete a través de la red de Tor podemos representarlo asi:
Alice(HTTPS) - G - M - E - Bob(HTTPS) 

Alice a con el navegador encripta los paquetes que quiere enviar a Bob con la clave pública del servidor web, hasta aquí si ignoramos el resto 
de las rutas que llevan el paquete de un cliente a un servidor, Alice y Bob inician una comunicación por el protocolo **HTTPs**. Ojo porque HTTPs 
no cifra la información de las cabecerás dejando ver el origen y destino de los paquetes, eso sí, se cifra el mensaje entre el cliente y servidor, pero a su vez el protocolo de enrutado de Tor va a sobreescribir esa información impidiendo que los paquetes TCP revelen esa información. 

Alice a través de Tor proxy encrypta el paquete HTTPs que quiere enviar a Bob con la clave del servidor E, luego con el resultado hace los mismo con la clave del servidor M y por último hace lo mismo con G.

Ahora cuando el paquete salga de Alice, al llegar a A (nodo de entrada) la primera capa será la que está cifrada con su clave, la descifra y descubre la dirección del paquete a cual hay que enviarlo a, o sea la dirección de M pero el resto de la información está encriptada, solo M puede verla aparentemente.
El paquete ahora viaja a M, M lo descifra con su clave y descubre que hay una dirección de destino E y un mensaje (de nuevo cifrado) por lo cual se lo envía. 
Ahora C descifra la tercera capa de la cebolla y descubre un paquete HTTPs se tiene que enviar a Bob y eso es lo que hace. 
Todo se ha descifrado en el orden correcto, o sea a la inversa de como se cifró.

#### Llaves y cofres
>Si te parece difícil de entender, haré una analogía: 
Imagina que quieres enviar un mensaje secreto y que para enviarlo lo harás mediante tres intermediarios,
Para ello tienes una lista de intermediarios y de todos ellos eliges a tres. 
Uno por uno cada intermediario te entregará su cofre y una copia de la llave del mismo.
Tú le das las siguiente instrucciones: el deberá entregar el contenido al siguiente intermediario una vez reciba un cofre.
Cabe decir que los cofres caben unos dentro de otros. 
Por supuesto los intermediarios han prometido discreción y no revelarán a nadie a quien va dirigido, se limitarán a entregar el cofre 
¿Podrías conseguir que solo enviando un cofre el mensaje llegase a su destino? ¿Como evitas que se se conozcan entre sí todos los intermediarios?

>La solución es meter en cada cofre el nombre del siguiente intermediario al lado del cofre que tiene la copia de la llave del mismo.
De esta forma el primer intermediario recibirirá el cofre del que posee la llave, lo abre pues es el único que puede, al abrirlo encuentra el nombre del siguiente intermediario y un cofre. Ahora ya sabe a quien tiene que llevarle ese cofre, así que lo saca y se lo lleva al segundo. El segundo intermediario recibe el cofre y le ocurre exactamente lo mismo, finalmente el tercer intermediario entrega el contenido del cofre a su destinatario, pero si todo el mundo ha sido discreto y no han revelado quien les ha entregado el cofre, el que lo recibe solo podría reconocer a los dos inmediatos en la cadena.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/8b/Orbot-logo.svg/1024px-Orbot-logo.svg.png" alt="Orbot Logo" width=200px>

#### ¿Quien conoce la IP de quien en la red de tor?
Si has entendido lo que ha ocurrido en el parrafo anterior podemos resumir que
Alice envía el paquete a  G y G lo envía a M
por lo que G conoce las direcciones de Alice y M
M recibe el paquete de G y envía el paquete a E 
Por lo que conoce las direcciones de M y E
Solo cuando el paquete llega a E se descubre la dirección a la que va el paquete (a Bob)
Por lo que E conoce a M y Bob 

En resumen de quien conoce a quien durante la transmisión de los paquetes:

>En esta cadena Alice <=> G <=> M <=> E <=> Bob. Alice establece el circuito por lo tanto conoce las IP de todos los implicados
{: .prompt-tip }

Para el resto de participantes:
- **Alice <=> G <=> M <=> ?** G se comunica con Alice y con M pero desconoce el nodo de salida E, es información está reservada a M
- **? <=> E <=> M <=> G <=> ?** M comunica con E y con G pero no ignora la existencia de Alice y Bob
- **? <=> E <=> Bob** Bob recibe paquetes de E nodo de salida sin saber que proceden de M. Como dijimos esa información esta reservada a E
- **? <=> M <=> E <=> Bob** E comunica con Bob y con M porque intervienen en la comunicación pero ignora a G y a Alice

![](https://upload.wikimedia.org/wikipedia/commons/thumb/3/37/Funcionamiento_red_tor2.svg/1920px-Funcionamiento_red_tor2.svg.png)

Cadena hacia arriba o hacia abajo, excepto el que inicia la comunicación el resto de participantes desconoce siempre el otro extremo 
de la cadena, permitiendo el anonimato.

Se que hay ataque teóricos sobre la red de Tor, casi todos pasan por correlacionar los datos siempre y cuando se controlen una cantidad de nodos de la red suficientes, pero por el momento sigue siendo seguro si se usa con precaución, conocimiento y sentido común. 

> Tor es legal en la mayoría de los países. Algunos países como Turquía, Rusia, Bielorrusia, Irán y  Arabia Saudí tienen leyes específicas para Tor, China bloquea todo el tráfico de Tor. Si vives en alguno de estos países informate antes de usar Tor
{: .prompt-warning }
Descargar el navegador [Tor Browser](https://www.torproject.org/download/)

#### Hidden services
Los hidden services son servicios en la red de tor, son puertos de escucha ofuscados por direcciones ***.onion***, pueden albergar cualquier cosa pero mayormente se utiliza para páginas web, lo que se conoce como Deepweb
[../HiddenServices](../hidden-services)

#### Confía pero no confíes
> Tor es una red formada por voluntarios, esto supone un eslabón débil en la cadena de la seguridad, porque ellos no disponen de los recursos que puede disponer un estado o organización, por lo que mi recomendación es siempre usar Tor acompañado de otras medidas de seguridad.
Por ejemplo usar una VPN, DNScrypt, Privoxy y/o cualquier herramienta que pueda mejorar la seguridad y minimizar los riesgos que pudiesen afectar a nuestras actividades en la red
{: .prompt-danger }




