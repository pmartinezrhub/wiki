---
title: Where are the robots
date: 2025-04-1 12:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, ingeniería reversa]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>where are the robots
Author: zaratec/Danny
Description
Can you find the robots? https://jupiter.challenges.picoctf.org/problem/56830/ (link) or http://jupiter.challenges.picoctf.org:56830
{: .prompt-tip}

El fichero robots.txt es el fichero donde se declaran si se desea permitir robots para indexar una página web, normalmente se almacena en la raiz del servidor para que los robots "sonda" de distintas compañías como google [https://es.wikipedia.org/wiki/Est%C3%A1ndar_de_exclusi%C3%B3n_de_robots](https://es.wikipedia.org/wiki/Est%C3%A1ndar_de_exclusi%C3%B3n_de_robots)  

Abrimos el enlace y nos aparce una Web con el texto

```
# Welcome

Where are the robots?
```

Sospecho el fichero robots debería encontrarse justo en la raíz del servidor así que añado __/robots.txt__ https://jupiter.challenges.picoctf.org/problem/56830/robots.txt 

```
User-agent: *
Disallow: /1bb4c.html
```

El fichero robots.txt contiene una regla para evitar indexar en buscadores la página /1bb4c.html. Así que vamos a ver que contiene
https://jupiter.challenges.picoctf.org/problem/56830/1bb4c.html

```
Guess you found the robots
picoCTF{ca1cu1at1ng_Mach1n3s_1bb4c}
```

flag: **picoCTF{ca1cu1at1ng_Mach1n3s_1bb4c}**