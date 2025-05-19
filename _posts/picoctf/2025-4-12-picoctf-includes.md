---
title: Includes
date: 2025-04-14 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, includes, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Includes
Author: LT 'syreal' Jones
Description
Can you get the flag? Go to this website and see what you can discover.
{: .prompt-tip }


Que se puede descubrir? Pues en el código de la página no gran cosa, pero si inspeccionamos los archivos relacionados:
style.css
``` 
body {
  background-color: lightblue;
}

/*  picoCTF{1nclu51v17y_1of2_  */
```
script.js
```
function greetings()
{
  alert("This code is in a separate file!");
}

//  f7w_2of2_6edef411}
```

flag: **picoCTF{1nclu51v17y_1of2_f7w_2of2_6edef411}**

Hay otro reto practicamente idéntico a este. 