---
title: Information
date: 2025-03-31 17:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, forense, mat2, esteganografía]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>information
Author: susie
Description
Files can always be changed in a secret way. Can you find the flag? [cat.jpg](https://mercury.picoctf.net/static/d1375e383810d8d957c04eef9e345732/cat.jpg)
{: .prompt-tip }

Siendo un ejercicio de la categoría forense imagino que habrá que investigar los metadatos de la imagen. 

``` shell
pablo☠office Information$ mat2 -s cat.jpg   
[+] Metadata for cat.jpg:
    ApplicationRecordVersion: 4
    CopyrightNotice: PicoCTF
    CurrentIPTCDigest: 7a78f3d9cfb1ce42ab5a3aa30573d617
    License: cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9
    Rights: PicoCTF
    XMPToolkit: Image::ExifTool 10.80
```
Parece que podría contener la flag, nos da una pista ese PicoCTF aunque parece también que si fuese la flag debería estar codificada en en los metadatos. El hash MD5 7a78f3d9cfb1ce42ab5a3aa30573d617 (parece MD5) es unidireccional e irreversible por lo que centro los esfuerzos en lo que parece base64

``` shell
pablo☠office Information$ echo "cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9" | base64 -d
picoCTF{the_m3tadata_1s_modified}%    
```

flag: **picoCTF{the_m3tadata_1s_modified}**

