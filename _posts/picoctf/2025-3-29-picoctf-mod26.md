---
title: Mod26
date: 2025-03-30 17:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, criptografía]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Mod 26
Author: Pandu
Description
Cryptography can be easy, do you know what ROT13 is? cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_uJdSftmh}
{: .prompt-tip }

Este reto es una toma de contacto con un algoritmo de cifrado ROT13. Parece que nos ofrencen la flag cifrada.
La forma más rápida de hacer esto es valernos de un decoder online, 
[https://www.dcode.fr/rot-13-cipher](https://www.dcode.fr/rot-13-cipher)

Sin embargo tenemos una herramienta maravillosa que se llama Python, dispone de una librería codecs y esta 
contiene codecs (codificadores y decodificadores) que implementan a rot13. 

``` shell
pablo☠office ~$ python
Python 3.11.2 (main, Nov 30 2024, 21:22:50) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import codecs
>>> codecs.decode("cvpbPGS{arkg_gvzr_V'yy_gel_2_ebhaqf_bs_ebg13_uJdSftmh}", 'rot_13')
"picoCTF{next_time_I'll_try_2_rounds_of_rot13_hWqFsgzu}"
```

flag: **picoCTF{next_time_I'll_try_2_rounds_of_rot13_hWqFsgzu}**
  