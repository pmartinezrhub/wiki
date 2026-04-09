---
title: Insp3ct0r
date: 2025-04-14 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, insp3ct0r, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Insp3ct0r
Author: zaratec/danny
Description
Kishor Balan tipped us off that the following code may need inspection: https://jupiter.challenges.picoctf.org/problem/44924/ (link) or http://jupiter.challenges.picoctf.org:44924
{: .prompt-tip }

Cuando entras a la web aparece una web con dos botones y un mensaje
``` html
Inspect Me
How

I used these to make this site:
HTML
CSS
JS (JavaScript) 
```

Simplemente inspeccionarmos el código fuente de la página y encontramos la primera parte de la bandera, en algún sitio más debe estar el resto... 1/3
``` html
<!-- Html is neat. Anyways have 1/3 of the flag: picoCTF{tru3_d3 -->
 ```
Inpeccionamos el códido del archivo mycss.css 2/3
```
/* You need CSS to make pretty pages. Here's part 2/3 of the flag: t3ct1ve_0r_ju5t */
```

si inpeccionamos el códido de my.js 3/3
```
/* Javascript sure is neat. Anyways part 3/3 of the flag: _lucky?f10be399} */
```
****

 flag: **picoCTF{tru3_d3t3ct1ve_0r_ju5t_lucky?f10be399}**

 Moraleja: Acostrumbrarse a ver el código fuente de la página, en busca de variables hardocodeadas y comentarios que revelan información
 importante.