---
title: morse-code
date: 2025-05-15 11:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, morse, criptografía]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>morse-code
Author: Will Hong
Description
Morse code is well known. Can you decrypt this? Download the file here. Wrap your answer with picoCTF{}, put underscores in place of pauses, and use all lowercase.
{: .prompt-tip }


Este reto es muy original, pues nos propone criptografía a través de audio

``` shell
pablo☠office morse$ wget https://artifacts.picoctf.net/c/79/morse_chal.wav

```

Descargado el archivo procedemos a su análisis, para eso podemos usar herramienta online o bien alguna herramienta como audacity
Como ya suponemos morse se basa en pulsos de sonido, cortos o largos, por lo que es sencillo identificarlos con un analizador de este tipo de archivos. Resulta que hay una web que te ahorrará darle muchas vueltas y lo hará por ti. 
[https://morsecode.world/international/decoder/audio-decoder-adaptive.html](https://morsecode.world/international/decoder/audio-decoder-adaptive.html) 

Subo el archivo y me devuelve este resultado que parece que tiene algo de sentido:

```
WH47 H47H 90D W20U9H7

```

flag: **PicoCTF{WH47_H47H_90D_W20U9H7}**

Una de las cosas que más me gustan de PicoCTF es su originalidad a la hora de proponer retos y por ende descubrir nuevas herramientas y enfoques de como resolverlos. 