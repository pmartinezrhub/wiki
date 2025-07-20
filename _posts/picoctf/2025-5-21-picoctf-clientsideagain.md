---
title: Client-side-again
date: 2025-05-21 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, client side again, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Client-side-again
Author: Danny
Description
Can you break into this super secure portal? https://jupiter.challenges.picoctf.org/problem/6353/ (link) or http://jupiter.challenges.picoctf.org:6353
{: .prompt-tip }

Al entrar a la página veremos un formulario que solicita una contraseña por supuesto al introducirla saltará un mensaje de incorrecto, pero como ya habíamos visto en un post muy parecido dejar esto del lado del cliente es muy peligroso

``` javascript
// This is just a sample script. Paste your real code (javascript or HTML) here.

var _0x5a46 = ['0a029}', '_again_5', 'this', 'Password\x20Verified', 'Incorrect\x20password', 'getElementById', 'value', 'substring', 'picoCTF{', 'not_this'];
(function(_0x4bd822, _0x2bd6f7) {
    var _0xb4bdb3 = function(_0x1d68f6) {
        while (--_0x1d68f6) {
            _0x4bd822['push'](_0x4bd822['shift']());
        }
    };
    _0xb4bdb3(++_0x2bd6f7);
}(_0x5a46, 0x1b3));
var _0x4b5b = function(_0x2d8f05, _0x4b81bb) {
    _0x2d8f05 = _0x2d8f05 - 0x0;
    var _0x4d74cb = _0x5a46[_0x2d8f05];
    return _0x4d74cb;
};
print(_0x5a46)

function verify() {
    checkpass = document[_0x4b5b('0x0')]('pass')[_0x4b5b('0x1')];
    split = 0x4;
    if (checkpass[_0x4b5b('0x2')](0x0, split * 0x2) == _0x4b5b('0x3')) {
        if (checkpass[_0x4b5b('0x2')](0x7, 0x9) == '{n') {
            if (checkpass[_0x4b5b('0x2')](split * 0x2, split * 0x2 * 0x2) == _0x4b5b('0x4')) {
                if (checkpass[_0x4b5b('0x2')](0x3, 0x6) == 'oCT') {
                    if (checkpass[_0x4b5b('0x2')](split * 0x3 * 0x2, split * 0x4 * 0x2) == _0x4b5b('0x5')) {
                        if (checkpass['substring'](0x6, 0xb) == 'F{not') {
                            if (checkpass[_0x4b5b('0x2')](split * 0x2 * 0x2, split * 0x3 * 0x2) == _0x4b5b('0x6')) {
                                if (checkpass[_0x4b5b('0x2')](0xc, 0x10) == _0x4b5b('0x7')) {
                                    alert(_0x4b5b('0x8'));
                                }
                            }
                        }
                    }
                }
            }
        }
    } else {
        alert(_0x4b5b('0x9'));
    }
}
```

Haciendo un analisis va haciendo una serie de comprobaciones de trozos de la password introducida, en principio parece que tenemos 
la password hardcodeada, véase 'picoCTF{', '_again_5' , 'not_this' , 'this', '0a029}' 

``` html
var _0x5a46 = ['0a029}', '_again_5', 'this', 'Password\x20Verified', 'Incorrect\x20password', 'getElementById', 'value', 'substring', 'picoCTF{', 'not_this'];
```
Así que copio el código javascript y lo ejecuto con gjs (un interprete de javascript para el terminal), eso sí, introduczco una línea de debug que va dejando las cosas aún más claras

```
pablo☠office client-side-again$ gjs test.js
getElementById,value,substring,picoCTF{,not_this,0a029},_again_5,this,Password Verified,Incorrect password
```

Y por deducción lógica y un ejercicio realizado muy parecido a este, además debería de calificarlo como ingeniería reversa?, pruebo componiendo al estilo concurso de la tele "resuelvo", ya que sabemos que las flags utilizan los guiones bajos.

>picoCTF{not_this_again_50a029}

En la alerta obtendremos un 
>Password Verified

flag: **picoCTF{not_this_again_50a029}**