---
title: Local Authority
date: 2025-04-17 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, localauthority, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
> Local Authority
Author: LT 'syreal' Jones
Description
Can you get the flag? Go to this website and see what you can discover.
{: .prompt-tip }

Al entrar en la web nos encontramos el típico formulario de login 

>Secure Customer Portal
Only letters and numbers allowed for username and password.

Introducimos cualquier usuario y password fijándonos en la consola de desarrollador del navegador
Uno de los recursos que la página carga es el archivo secure.js, también puedes encontrarlo en el código de la paǵina login.php:

``` javascript
function checkPassword(username, password)
{
  if( username === 'admin' && password === 'strongPassword098765' )
  {
    return true;
  }
  else
  {
    return false;
  }
}
```
Nos indica que podemos logear con admin:strongPassword098765
Nos logeamos pues:

flag: **picoCTF{j5_15_7r4n5p4r3n7_b0c2c9cb}**

Recuerda no harcodear passwords en tu código o al menos no llames secure.js a algo que es de todo menos seguro. Reto trivial.