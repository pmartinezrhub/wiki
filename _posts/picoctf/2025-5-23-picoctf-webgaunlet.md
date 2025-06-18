---
title: Web Gauntlet
date: 2025-05-23 12:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, web gaunlet, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Web Gauntlet
Author: madStacks
If the flag is not displayed after completing this challenge, try clearing your cookies. Cookies set by other challenges may prevent the flag from displaying properly.
Description
Can you beat the filters? Log in as admin http://jupiter.challenges.picoctf.org:54319/ http://jupiter.challenges.picoctf.org:54319/filter.php
{: .prompt-tip }

Este reto comienza mostrando una web y un formulario encabezado con lo que parece un contado de rounds o puede que sean "intentos", pues el reto recomienda limpiar cookies. Además nos indican una web que contiene una serie de filtros

```
Filters: or and true false union like = > < ; -- /* */ admin
```


```
Round 1 / 5
```