---
title: Search source
date: 2025-05-14 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, search source, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Search source
Author: Mubarak Mikail
Description
The developer of this website mistakenly left an important artifact in the website source, can you find it?
Additional details will be available after launching your challenge instance.
{: .prompt-tip }

Caundo miramos el código fuente de la instancia podemos encontrar un easter egg o podría ser una pista "keep digging" - "continua cavando"

``` html

            <!-- six_box
            end six_box   The flag is not here but keep digging :)-- >

            <!-- We Do Yogas -->
```
Si usamos las pistas que nos da el reto nos indica los siguiente, que descarguemos la web para usar una herramienta de búsqueda potente

```
How could you mirror the website on your local machine so you could use more powerful tools for searching?
```
Ok, descarguemos pues el sitio entero

``` shell
pablo☠office Search-source$ wget -r http://saturn.picoctf.net:56031/
pablo☠office Search-source$ grep -r pico
css/style.css:/** banner_main picoCTF{1nsp3ti0n_0f_w3bpag3s_587d12b8} **/
```
Bien podíamos haber mirado de nuevo los archivos css, pero siguiendo las indicaciones y de paso le damos un uso no muy común a wget

flag **picoCTF{1nsp3ti0n_0f_w3bpag3s_587d12b8}**
