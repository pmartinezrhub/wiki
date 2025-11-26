---
title: It is my Birthday
date: 2025-05-23 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, is my birdthday, md5]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>It is my Birthday
Author: madStacks
Description
I sent out 2 invitations to all of my friends for my birthday! I'll know if they get stolen because the two invites look similar, and they even have the same md5 hash, but they are slightly different! You wouldn't believe how long it took me to find a collision. Anyway, see if you're invited by submitting 2 PDFs to my website. http://mercury.picoctf.net:57247/
{: .prompt-tip }


Cuando abre la web podrás ver que hay un formulario con dos selectores para subir dos archivos en teoría admitiría pdf, podemos intentar por ejemplo mirar si la entrada está sanitizada probando a subir algún archivo del estilo 
"pdf.php?". Otra opción es algo por el estilo pero que el pdf tenga embebido código php, hubo un reto anterior [picoctf-trickster](../picoctf-trickster) sobre subida de archivos sin sanitizar, si embargo al intentar esta técnica en este reto e intentar embeber código html o php en el request capturado con Zap, veo el siguiente mensaje, lo que me hace pensar que debo crear el archivo primero. 

>MD5 hashes do not match!

Dado que no veo nada destacable en el código fuente de la página y tampoco tiene archivo robots.txt continuo por capturar el request y ver si al subir los archivos pdf, podemos luego verlos o si nos diese una pista de a donde se envían, como la ruta. 
Así que creo los dos archivos pdf, con dos imágenes y procedo a subirlos, como ya lo teníamos codificda, nuestra shell mínima de comandos para PHP, lo guardo como webshell.pdf.php para ver si se lo traga al subirlo.
El truco de cambiar la extensión no ha funcionado y así no los indica, sin embargo si subo el archivo webshell.php.pdf, si que parece funcionar. el caso es que sea explotable. De todas formas al hacer esto sigue saliendo el mensaje anterior de que los hashes no coinciden. Tendŕe que falsear los md5 en el request?

>Not a PDF! 

Otra cosa que probé fue intentar subir dos ficheros iguales con distinto nombre para que los hashes MD5 fueran iguales pero me devuelve el siguiente mensaje

>Files are not different!

Esto me da una idea de que la sanitización consiste en subir dos ficheros con distinto contenido, pero con el mismo 
MD5, esto parece complicado, a nos ser que se simiplifique mucho el MD5 para econtrar una colisión. 
Pero bueno para eso está Internet para encontrar cosas como este interesante artículo y dos archivos hello y erase los cuales puedo descargarme de esta web [https://www.mscs.dal.ca/~selinger/md5collision/](https://www.mscs.dal.ca/~selinger/md5collision/) 

Renombro estos dos archivos e intento subirlos como pdf para ver que pasa

```
pablo☠office Is_my_Birthday$ md5sum erase.pdf hello.pdf 
da5c61e1edc0f18337e46418e48c1290  erase.pdf
da5c61e1edc0f18337e46418e48c1290  hello.pdf

```

Aparece el código fuente de la página donde podemos encontrar la flag

``` html
// FLAG: picoCTF{c0ngr4ts_u_r_1nv1t3d_40d81ca2}
```

flag: **picoCTF{c0ngr4ts_u_r_1nv1t3d_40d81ca2}**

Interesante reto sobre las colisiones de MD5. 