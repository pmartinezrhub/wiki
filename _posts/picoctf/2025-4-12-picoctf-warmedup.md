---
title: Warmed Up
date: 2025-04-11 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, warmedup]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Warmed Up
Author: Sanjay C/Danny Tunitis
Description
What is 0x3D (base 16) in decimal (base 10)?
{: .prompt-tip }


Que cuanto es 0x3D(base 16) en decimal
A tener en cuenta que 0x desaparece y nos quedamos con 3d. El resto de la tarea la podemos resolver de nuevo con Python:

``` python
Python 3.11.2 (main, Nov 30 2024, 21:22:50) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> hexadecimal="3d"
>>> print(str(int(hexadecimal, 16)))
61
```

flag: **picoCTF{61}**

PicoCTF asegurándose de que conocemos los sistemas de numeración básicos. Hay más ejercicios simples y necesarios para hacer otros luego
más complejos