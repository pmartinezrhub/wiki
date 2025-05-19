---
title: head-dump
date: 2025-04-17 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, head-dump, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>head-dump
Author: Prince Niyonshuti N.
Description
Welcome to the challenge! In this challenge, you will explore a web application and find an endpoint that exposes a file containing a hidden flag. The application is a simple blog website where you can read articles about various topics, including an article about API Documentation. Your goal is to explore the application and find the endpoint that generates files holding the server’s memory, where a secret flag is hidden. The website is running picoCTF News.
{: .prompt-tip }

En este reto lo que tenemos que hacer es visitar la API sustituyendo en la URL /heapdump Diagnosing the memory allocation.
Nos permite bajarnos un archivo de diagnostico que incluye la flag. 

flag: **picoCTF{Pat!3nt_15_Th3_K3y_bed6b6b8}**

Se acaban los retos web fáciles. Ahora comenzarán los difíciles