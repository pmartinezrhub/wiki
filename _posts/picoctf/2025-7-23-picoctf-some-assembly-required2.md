---
title: Some Assembly Required 2
date: 2025-07-23 11:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web, flask, cookies]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Some Assembly Required 2
Author: Sears Schulz
Description
http://mercury.picoctf.net:48841/index.html
{: .prompt-tip }

El reto parece más simple de lo esperado, abrimos la web y vemos un formulario
``` html
Enter flag:
```
Ahora miremos el código fuente de la página

```html
<html>
<head>
	<meta charset="UTF-8">
	<script src="Y8splx37qY.js"></script>
</head>
<body>
	<h4>Enter flag:</h4>
	<input type="text" id="input"/>
	<button onclick="onButtonPress()">Submit</button>
	<p id="result"></p>
</body>
</html>
```
Y luego inspeccionamos el código fuente de **Y8splx37qY.js**
He ordenado el código con una utilidad [https://beautifier.io/](https://beautifier.io/) me he dado cuenta de que Duckduckgo ofrece esta utilidad si haces la búsqueda parecida a **javascript beautifier**, mostrando un recuadro para introducir el código. 

``` javascript
const _0x6d8f = ['copy_char', 'value', '207aLjBod', '1301420SaUSqf', '233ZRpipt', '2224QffgXU', 'check_flag', '408533hsoVYx', 'instance', '278338GVFUrH', 'Correct!', '549933ZVjkwI', 'innerHTML', 'charCodeAt', './aD8SvhyVkb', 'result', '977AzKzwq', 'Incorrect!', 'exports', 'length', 'getElementById', '1jIrMBu', 'input', '615361geljRK'];
const _0x5c00 = function(_0x58505a, _0x4d6e6c) {
    _0x58505a = _0x58505a - 0xc3;
    let _0x6d8fc4 = _0x6d8f[_0x58505a];
    return _0x6d8fc4;
};
(function(_0x12fd07, _0x4e9d05) {
    const _0x4f7b75 = _0x5c00;
    while (!![]) {
        try {
            const _0x1bb902 = -parseInt(_0x4f7b75(0xc8)) * -parseInt(_0x4f7b75(0xc9)) + -parseInt(_0x4f7b75(0xcd)) + parseInt(_0x4f7b75(0xcf)) + parseInt(_0x4f7b75(0xc3)) + -parseInt(_0x4f7b75(0xc6)) * parseInt(_0x4f7b75(0xd4)) + parseInt(_0x4f7b75(0xcb)) + -parseInt(_0x4f7b75(0xd9)) * parseInt(_0x4f7b75(0xc7));
            if (_0x1bb902 === _0x4e9d05) break;
            else _0x12fd07['push'](_0x12fd07['shift']());
        } catch (_0x4f8a) {
            _0x12fd07['push'](_0x12fd07['shift']());
        }
    }
}(_0x6d8f, 0x4bb06));
let exports;
(async() => {
    const _0x835967 = _0x5c00;
    let _0x1adb5f = await fetch(_0x835967(0xd2)),
        _0x355961 = await WebAssembly['instantiate'](await _0x1adb5f['arrayBuffer']()),
        _0x5c0ffa = _0x355961[_0x835967(0xcc)];
    exports = _0x5c0ffa[_0x835967(0xd6)];
})();

function onButtonPress() {
    const _0x50ea62 = _0x5c00;
    let _0x5f4170 = document[_0x50ea62(0xd8)](_0x50ea62(0xda))[_0x50ea62(0xc5)];
    for (let _0x19d3ca = 0x0; _0x19d3ca < _0x5f4170['length']; _0x19d3ca++) {
        exports[_0x50ea62(0xc4)](_0x5f4170[_0x50ea62(0xd1)](_0x19d3ca), _0x19d3ca);
    }
    exports['copy_char'](0x0, _0x5f4170[_0x50ea62(0xd7)]), exports[_0x50ea62(0xca)]() == 0x1 ? document['getElementById'](_0x50ea62(0xd3))[_0x50ea62(0xd0)] = _0x50ea62(0xce) : document[_0x50ea62(0xd8)](_0x50ea62(0xd3))['innerHTML'] = _0x50ea62(0xd5);
}
```
Por supuesto el código viene ofuscado, pero ya habíamos hecho un reto similar [Some Assembly Required 1](../picoctf-someassemblyrequired1). La idea es que la constante declarada al principio contiene la clave desordenada, el código ofuscado hace más difícil pero no imposible componer la clave. 

Si accedo a la supuesta web me ofrece un archivo, lo descargo para su análisis

``` shell
wget mercury.picoctf.net:48841/aD8SvhyVkb
pablo☠office Some-assembly-required2$ file aD8SvhyVkb 
aD8SvhyVkb: WebAssembly (wasm) binary module version 0x1 (MVP)
```

Bueno pues tendré que buscar alguna utilidad para intentar desensamblar el binario. Encontré esta herramienta [watb](https://github.com/WebAssembly/wabt) Watb, es un conjunto de herramientas para  manejar WebAssembly. 

``` shell
pablo☠office src$ ./wasm-decompile  ~/Documentos/ctfs/picoctf/Some-assembly-required2/aD8SvhyVkb 
export memory memory(initial: 2, max: 0);

global g_a:int = 66864;
export global input:int = 1072;
export global dso_handle:int = 1024;
export global data_end:int = 1328;
export global global_base:int = 1024;
export global heap_base:int = 66864;
export global memory_base:int = 0;
export global table_base:int = 1;

table T_a:funcref(min: 1, max: 1);

data d_xakgKNsjm8mk110j88lj0l11nnmu(offset: 1024) =
"xakgK\5cNs>j:<?m8>m;>k110<j?=88lj0l11:n;nmu\00\00";

export function wasm_call_ctors() { // func0
}

export function strcmp(a:int, b:int):int { // func1
  var c:int = g_a;
  var d:int = 32;
  var e:int = c - d;
  e[6]:int = a;
  e[5]:int = b;
  var f:int = e[6]:int;
  e[4]:int = f;
  var g:int = e[5]:int;
  e[3]:int = g;
  loop L_b {
    var h:ubyte_ptr = e[4]:int;
    var i:int = 1;
    var j:int = h + i;
    e[4]:int = j;
    var k:int = h[0];
    e[11]:byte = k;
    var l:ubyte_ptr = e[3]:int;
    var m:int = 1;
    var n:int = l + m;
    e[3]:int = n;
    var o:int = l[0];
    e[10]:byte = o;
    var p:int = e[11]:ubyte;
    var q:int = 255;
    var r:int = p & q;
    if (r) goto B_c;
    var s:int = e[11]:ubyte;
    var t:int = 255;
    var u:int = s & t;
    var v:int = e[10]:ubyte;
    var w:int = 255;
    var x:int = v & w;
    var y:int = u - x;
    e[7]:int = y;
    goto B_a;
    label B_c:
    var z:int = e[11]:ubyte;
    var aa:int = 255;
    var ba:int = z & aa;
    var ca:int = e[10]:ubyte;
    var da:int = 255;
    var ea:int = ca & da;
    var fa:int = ba;
    var ga:int = ea;
    var ha:int = fa == ga;
    var ia:int = 1;
    var ja:int = ha & ia;
    if (ja) continue L_b;
  }
  var ka:int = e[11]:ubyte;
  var la:int = 255;
  var ma:int = ka & la;
  var na:int = e[10]:ubyte;
  var oa:int = 255;
  var pa:int = na & oa;
  var qa:int = ma - pa;
  e[7]:int = qa;
  label B_a:
  var ra:int = e[7]:int;
  return ra;
}

export function check_flag():int { // func2
  var a:int = 0;
  var b:int = 1072;
  var c:int = 1024;
  var d:int = strcmp(c, b);
  var e:int = d;
  var f:int = a;
  var g:int = e != f;
  var h:int = -1;
  var i:int = g ^ h;
  var j:int = 1;
  var k:int = i & j;
  return k;
}

function copy(a:int, b:int) { // func3
  var c:int = g_a;
  var d:int = 16;
  var e:int_ptr = c - d;
  e[3] = a;
  e[2] = b;
  var f:int = e[3];
  if (eqz(f)) goto B_a;
  var g:int = e[3];
  var h:int = 8;
  var i:int = g ^ h;
  e[3] = i;
  label B_a:
  var j:int = e[3];
  var k:byte_ptr = e[2];
  k[1072] = j;
}

```

Parece que el código define que se está haciendo XOR con clave 8
```
var h:int = 8;
var i:int = g ^ h; 

```

Implemento en Python un XOR con esta cadena declarada en el código, pues parece la flag. Imagino que el mecanismo es descrifrar la bandera hardcodeada 

``` python
original = "xakgK\5cNs>j:<?m8>m;>k110<j?=88lj0l11:n;nmu\00\00"
result = ''.join(chr(ord(c) ^ 8) for c in original)
print(result)
```
El resultado parecía incorrecto sin embargo por un lado si conforma las llaves "\{\}" y al introducirlo en picoCTF fue validada así que es la buena.

```shell
pablo☠office Some-assembly-required2$ python decrypt_xor.py
kF{6b247e06e36c9984b7500db8d992f3fe}
```

flag: **picoCTF{6b247e06e36c9984b7500db8d992f3fe}**

Si ahora introduces la bandera en el formulario, verás un correcto!

``` html
Enter flag:
Correct!
```
¿Dije que parecía simple? Me equivoqué. 