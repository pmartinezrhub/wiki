---
title: dont-use-client-side
date: 2025-04-14 22:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, dont-use-client-side, explotación web, javascript]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>dont-use-client-side
Author: Alex Fulton/Danny
Description
Can you break into this super secure portal? https://jupiter.challenges.picoctf.org/problem/17682/ (link) or http://jupiter.challenges.picoctf.org:17682
{: .prompt-tip }

Si inspeccionas el código fuente de esta página:

``` html
<html>
<head>
<title>Secure Login Portal</title>
</head>
<body bgcolor=blue>
<!-- standard MD5 implementation -->
<script type="text/javascript" src="md5.js"></script>

<script type="text/javascript">
  function verify() {
    checkpass = document.getElementById("pass").value;
    split = 4;
    if (checkpass.substring(0, split) == 'pico') {
      if (checkpass.substring(split*6, split*7) == '706c') {
        if (checkpass.substring(split, split*2) == 'CTF{') {
         if (checkpass.substring(split*4, split*5) == 'ts_p') {
          if (checkpass.substring(split*3, split*4) == 'lien') {
            if (checkpass.substring(split*5, split*6) == 'lz_b') {
              if (checkpass.substring(split*2, split*3) == 'no_c') {
                if (checkpass.substring(split*7, split*8) == '5}') {
                  alert("Password Verified")
                  }
                }
              }
      
            }
          }
        }
      }
    }
    else {
      alert("Incorrect password");
    }
    
  }
</script>
<div style="position:relative; padding:5px;top:50px; left:38%; width:350px; height:140px; background-color:yellow">
<div style="text-align:center">
<p>This is the secure login portal</p>
<p>Enter valid credentials to proceed</p>
<form action="index.html" method="post">
<input type="password" id="pass" size="8" />
<br/>
<input type="submit" value="verify" onclick="verify(); return false;" />
</form>
</div>
</div>
</body>
</html>
```
Si nos fijamos en el código de la función javascript está haciendo una serie de "ifs" anidados comprobando si cada parte de un string,
split = 4 (está dividido en trozos de 4) el introducido por el usuario en la web corresponde con los substring hardcodeados,
Simplmente reordenamos fijándonos en el orden depués de esta línea de comienzo "pico" que corresponde con el substring (0,4).
Continuamos ordenando los  split*2, split*3, hasta el 7.... 


```javascript
if (checkpass.substring(0, split) == 'pico') {
```
flag: **picoCTF{no_clients_plz_b706c5}**

Si introcudes esta bandera, deberá salir un alert:
``` javascript
Password Verified
```