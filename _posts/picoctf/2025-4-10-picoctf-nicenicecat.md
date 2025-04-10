---
title: Nice netcat...
date: 2025-04-10 19:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, nicenetcat, ascii]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>
Author: syreal
Description
There is a nice program that you can talk to by using this command in a shell: $ nc mercury.picoctf.net 43239, but it doesn't speak English...
{: .prompt-tip }

Para empezar habrá que seguir lo indicado y conectar con netcat tal y como nos indican en el prompt
```shell
pablo☠office ~$  nc mercury.picoctf.net 43239
112 
105 
99 
111 
67 
84 
70 
123 
103 
48 
48 
100 
95 
107 
49 
116 
116 
121 
33 
95 
110 
49 
99 
51 
95 
107 
49 
116 
116 
121 
33 
95 
55 
99 
48 
56 
50 
49 
102 
53 
125 
10 
```

Copiamos los números y los metemos a un archivo, como están en vertical podemos convertirlo a una sola línea en horizontal
``` shell
pablo☠office Nicenetcat$ cat list.txt| xargs
112 105 99 111 67 84 70 123 103 48 48 100 95 107 49 116 116 121 33 95 110 49 99 51 95 107 49 116 116 121 33 95 55 99 48 56 50 49 102 53 125 10
```
Codificio un script para convertir la lista de números a su correspondiente en ascii aunque lo más cómodo sería utilizar [hacktools/#encoding-and-ciphers](../hacktools/#encoding-and-ciphers) que es igual de efectivo y rápido pero vamos a jugar un poco con Python para descifrar lo que parec ascii. Sabemos que ascii es un código 
que se representa con un byte, un byte son 8bits y como 2^8 = 256 y ninguno d elos números supera a 125 y sabiendo que los últimos carácteres ascii corresponden a carácteres
especiales podemos apostar a que es ascii codificado (además es un reto fácil, debería ser ascii)

``` python                                                                             
numeros="112 105 99 111 67 84 70 123 103 48 48 100 95 107 49 116 116 121 33 95 110 49 99 51 95 107 49 116 116 121 33 95 55 99 48 56 50 49 102 53 125 10"
result = ""
for numero in numeros.split():
    result+=(chr(int(numero)))

print(result)

picoCTF{g00d_k1tty!_n1c3_k1tty!_7c0821f5}
```

flag: **picoCTF{g00d_k1tty!_n1c3_k1tty!_7c0821f5}**

