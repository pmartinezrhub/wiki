---
title: Bookmarklet
date: 2025-04-17 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, bookmarklet, explotación web, javascript]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Bookmarklet
Author: Jeffery John
Description
Why search for the flag when I can make a bookmarklet to print it for me? Browse here, and find the flag!
{: .prompt-tip }

``` javascript
  javascript:(function() {
            var encryptedFlag = "àÒÆÞ¦È¬ëÙ£ÖÓÚåÛÑ¢ÕÓÔÅÐÙí";
            var key = "picoctf";
            var decryptedFlag = "";
            for (var i = 0; i < encryptedFlag.length; i++) {
                decryptedFlag += String.fromCharCode((encryptedFlag.charCodeAt(i) - key.charCodeAt(i % key.length) + 256) % 256);
            }
            alert(decryptedFlag);
        })();

```
Copiamos parte de el código y lo llevamos a la consola de desarrollador antes de pegar tendrás que introducir 'permitir pegar' en firefox o en chromium 'allow pasting' antes de poder lanzar javascript desde el propio navegador, por motivos de seguridad. 

``` javascript
var encryptedFlag = "àÒÆÞ¦È¬ëÙ£Ö�ÓÚåÛÑ¢ÕÓ�Ô�ÅÐ�Ù�í";
            var key = "picoctf";
            var decryptedFlag = "";
            for (var i = 0; i < encryptedFlag.length; i++) {
                decryptedFlag += String.fromCharCode((encryptedFlag.charCodeAt(i) - key.charCodeAt(i % key.length) + 256) % 256);
                process.stdout.write(String.fromCharCode((encryptedFlag.charCodeAt(i) - key.charCodeAt(i % key.length) + 256) % 256));

            }
           alert(decryptedFlag);
```

flag: **picoCTF{p@g3_turn3r_1d1ba7e0}**