---
title: BigZip
date: 2025-04-11 19:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, bigzip, grep]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Big Zip
Author: LT 'syreal' Jones
Description
Unzip this archive and find the flag.
Download [zip file](https://artifacts.picoctf.net/c/504/big-zip-files.zip)
{: .prompt-tip }


Parece que tenemos que descargar un archivo Zip grande y buscar la bandera entres los archivos
```shell
wget https://artifacts.picoctf.net/c/504/big-zip-files.zip
unzip big-zip-files.zip  
cd big-zip-files
grep -r picoCTF
pablo☠office big-zip-files$ grep -r picoCTF
folder_pmbymkjcya/folder_cawigcwvgv/folder_ltdayfmktr/folder_fnpfclfyee/whzxrpivpqld.txt:information on the record will last a billion years. Genes and brains and books encode picoCTF{gr3p_15_m4g1c_ef8790dc}
```

flag: **picoCTF{gr3p_15_m4g1c_ef8790dc}**

Este reto es una simple introducción a buscar patrones con el comando grep