---
title: ZAP Zed Attack Proxy
date: 2025-07-14 00:00:00 +0200
categories: [hacking, herramientas]
tags: [hacking, herramientas, zap, pentesting web]     # TAG names should always be lowercase
image:
    path: https://www.zaproxy.org/img/zap-by-checkmarx.svg
    alt: 
---

## ZAP Zed Attack Proxy
Para iniciarse en el pententing web siempre te van a recomendar usar un proxy web.
En muchos foros recomiendan usar Burpsuite, ahora también está Caido, ambos tienen el mismo propósito que ZAP.

Yo también los usé hasta que descubrí que en Zap proxy podría hacer practicamente lo mismo pero sin limitaciones.
Estoy seguro de que los más pros de la ciberseguridad alegarán que las versiones de pago de Burpsuito o Caido son mucho
mejores porque incluyen funcionalidades que Zap no posee. Me conformaré con que Zap vaya poco a poco implementando más funcionalidades
de momento cumple sobradamente con su propósito. Burp que es el que más he utilizado después de Zap, tiene jugosos addons, sin embargo me parece horroroso el tener que pagar para no tener un límite de request por segundo. 

Zap ofrece un montón de personalización y configuración. Tiene herramientas integradas superinteresantes como spiders/crawlers de la página,
se le pueden añadir extensiones y es mantenido por una comunidad. Por su puesto ofrece maneras de hacer fuzzing, bruteforcing, etc...
Incluso en el menu encontrarás enlaces a vídeos con tutoriales para utilizar la herramienta. 

## Zap vs Burp vs Caido
Lo que posiblemente se hecha en falta en Zap es el renderizado de la página, cosa que sí hacer Burpsuite. Y si ponemos atención a los detalles
las funcionalidades de Zap son muchas, quizás demasiadas.
Caido no lo he probado demasiado pero parece que sigue la misma filosofía que Burp, paga para tener funcionalidades extra.
Pero al final viendo la cantidad de información y vídeos que puedes encontrar de Zap pues no veo razones para probar el resto. 

## Funcionalidades de Zap
Enumeraré las más importantes e útiles:
- Interceptor proxy: Podemos interceptar una petición para después manejarlas, luego con el requester podemos modificarlas.
- Requester: Nos permite manipular una petición y relanzarla, es la funcionalidad principal de estos proxies. Modificar método de request,
cabezeras, parámetros, lo que se quiera. 
- Fuzzer/Bruteforce: Zap ofrece una especie de asistente para hacer fuzzing o fuerza bruta, muy interesante y rico en opciones como el 
preprocesado. 
- Active Scan: Un scanner de vulnerabilidades y de posibles fugas de información, ficheros no bien protegidos, etc.
- Spider Ajax: El complemento AJAX Spider integra en ZAP un rastreador de sitios web con AJAX llamado Crawljax. Puedes usarlo para identificar las páginas del sitio objetivo.
- Client Spider: Funciona de manera similar al AJAX Spider, pero tiene acceso al DOM a través de la extensión del navegador ZAP, lo que significa que puede encontrar contenido que el AJAX Spider no puede encontrar.
- Tester de autenticación: automátiza el manejo y la verificación de la sesión

Desde luego no he utilizado todas las opciones ni todas las configuraciones de Zap que no son pocas. 

![Zap](https://upload.wikimedia.org/wikipedia/commons/b/b0/OWASP-ZAP.png)

Creo que seguiré usando Zap mientras no vea necesiario cambiarme a una herramienta "más profesional". 