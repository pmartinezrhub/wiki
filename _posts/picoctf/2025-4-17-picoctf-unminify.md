---
title: Unminify
date: 2025-04-17 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, unminify, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
> Unminify
Author: Jeffery John
Description
I don't like scrolling down to read the code of my website, so I've squished it. As a bonus, my pages load faster! Browse here, and find the flag!
{: .prompt-tip }

Según entramos la web arroja el mensaje de que ya hemos recibido la bandera, así que toca repasar 
```
Welcome to my flag distribution website!

If you're reading this, your browser has succesfully received the flag.

I just deliver flags, I don't know how to read them...
```
Si inpeccionamos el código de la web nos encontrmos la bandera:
``` html
<p class="picoCTF{pr3tty_c0d3_d9c45a0b}">
```
flag: **picoCTF{pr3tty_c0d3_d9c45a0b}**

Se acaban los retos web fáciles. Ahora comenzarán los difíciles