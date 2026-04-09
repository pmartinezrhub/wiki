---
title: Fast Flux
date: 2025-4-8 11:00:00 +0200
categories: [ciberseguridad, redes]
tags: [fastflux, botnets, cnc]     
image:
    path: https://cdn.haproxy.com/documentation/assets/img/dns_srv_records.png?v=1744051514698
    alt: Fast Flux
---

## Fast Flux

De un tiempo a esta parte me he dado cuenta de que se está hablando mucho sobre Fast Flux. 
Voy a intentar explicar como realmente funciona una botnet que se precie hoy en día para arrojar luz sobre que es Fast Flux y
para que se utiliza.  

### Botnets 

Las botnes son redes de computadoras infectadas con malware, hay que hacer un stop aquí para los despistados:
Tú móvil también es una computadora. Desde el momento que se infecta este terminal comienza a llamarse **zombie**.

#### Problemas de las botnets

Una botnet necesita de un **CnC**, un centro de control para ejecutar acciones en los **zombies**. 
Si tu fueras un investigador de seguridad que descubre un troyano que se conecta a una IP digamos de un hosting. 
Podrías establecer una relación entre el CnC y esa botnet. Hay varias ténicas para conseguir esto que corresponden 
a la informática forense, entornos controlados para ver a donde se conecta el malware (sandboxing), capturar el tráfico generado por el
zombie, etc.

Una vez el investigador descubre el posible CnC, puede iniciar acciones para reportar al hosting y a las autoridades el incidente e indicar
la IP y/o los datos recabados para que tomen las acciones pertinentes, como norma general apagar el CnC y/o retirar los sistemas que lo sostienen
con lo que la botnet tiene los días o las horas contadas. Todos los zombies deberán ser reprogramados para contectarse a otra IP antes de 
que se pierda el control sobre ellos y por lo tanto la botnet quede inutilizada. 

#### Fast Flux

Aquí es donde los ciberdelincuentes encuentran una manera de prolongar la "estancia" de sus sistemas en un hosting y seguir operando desde los 
mismos, escondiendo su botnet detrás de distintos dominios que van rotando cada X minutos de IP.

No es magia negra, es una configuración que puedes realizar siguiente este manual de [Haproxy](
https://www.haproxy.com/documentation/haproxy-configuration-tutorials/dns-resolution/). 
Sí, es balance de carga ni más ni menos y Cloudfare hace algo muy parecido. 

### Double Fast Flux

Esto es igual que el Fast Flux pero es que además se van rotando los DNS que el malware va a utilizar para confundir más a los investigadores y la trazabilidad. 

### Fast Flux as Service (FFaS)

¿Me lo acabo de inventar? Pero es que Cloudflare y otros CDNs hacen esto (o algo muy parecido) y por lo tanto podemos decir que efectivamente podemos llamar a esto un servicio. Cualquiera podría solicitar estos sistemas para proteger su plataforma/web/empresa de ataques externos y tener más resilencia frente a adversarios. De hecho es por eso que se inventaron estos sistemas que fueron pensados para un uso legítimo. 

### Resumen

En cualquier caso el malware utilizado tiene que apuntar a un DNS, que le dirá donde tiene que resolver. Las IPs y dominios a los que
se apunta dicho resolver tienen rotaciones constantes, dificultando la localización de el CnC 

Con direcciones IP en constante rotación, Fast flux logra ignorar los intentos de neutralizar la infraestructura de por ejemplo una botnet. El bloqueo de una o varias IP tiene poco impacto en la funcionalidad general y además pueden ser un indicador para los ciberdelincuentes
de que su infraestructura está siendo desmantelada/atacada/apagada.

Las defensas estáticas basadas en el filtrado por IP se vuelven obsoletas cuando la infraestructura maliciosa cambia constantemente su huella digital y su IP.

Los rápidos cambios dificultan enormemente que las fuerzas del orden o los equipos de ciberseguridad rastreen la actividad maliciosa hasta su origen.


