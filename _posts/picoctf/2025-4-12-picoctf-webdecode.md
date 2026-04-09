---
title: WebDecode
date: 2025-04-14 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, webdecode, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>WebDecode
Author: Nana Ama Atombo-Sackey
Description
Do you know how to use the web inspector? Start searching here to find the flag
{: .prompt-tip }


Si miramos el código fuente de las páginas encontraremos lo siguiente en about.html:
Se me ocurrió ver si esa etiqueta era base64 y efectivamente, lo era. 
``` html
<section class="about" notify_true="cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDdiOTFjNzl9">
```

```
pablo☠office ~$ echo "cGljb0NURnt3ZWJfc3VjYzNzc2Z1bGx5X2QzYzBkZWRfMDdiOTFjNzl9" | base64 -d
picoCTF{web_succ3ssfully_d3c0ded_07b91c79}%  
```

flag: **picoCTF{web_succ3ssfully_d3c0ded_07b91c79}**

Estamos con los fáciles, ya vendrán los difíciles.
