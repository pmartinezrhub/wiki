---
title: Cookie Monster Secret Recipe
date: 2025-03-31 17:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web, cookies]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Cookie Monster Secret Recipe
Author: Brhane Giday and Prince Niyonshuti N.
Description
Cookie Monster has hidden his top-secret cookie recipe somewhere on his website. As an aspiring cookie detective, your mission is to uncover this delectable secret. Can you outsmart Cookie Monster and find the hidden recipe? You can access the Cookie Monster here and good luck
{: .prompt-tip}

El enlace nos lleva a una página con un login 
Si intentamos logearnos nos saldrá un mensaje:
>Access Denied
Cookie Monster says: 'Me no need password. Me just need cookies!'
Hint: Have you checked your cookies lately?
Go back>

Hay tres pistas para este reto una de ellas dice:
>Web browsers often have tools that can help you inspect various aspects of a webpage, including things you can't see directly.

Abro el deporador del navegador y compruebo las cookies en la pestaña de red y tanto en el POST como en la respuesta aparece esta cookie:
```
{
	"Cookies de la petición": {
		"secret_recipe": "cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzc3MUQ1RUIwfQ=="
	}
}
```
Parece base64 así que pruebo a decodificar:
``` shell
pablo☠office Information$ echo "cGljb0NURntjMDBrMWVfbTBuc3Rlcl9sMHZlc19jMDBraWVzXzc3MUQ1RUIwfQ==" | base64 -d
picoCTF{c00k1e_m0nster_l0ves_c00kies_771D5EB0}%        
```
flag: **picoCTF{c00k1e_m0nster_l0ves_c00kies_771D5EB0}**

Bueno no es exactamente explotación web pero te enseña a buscar la cookie. 