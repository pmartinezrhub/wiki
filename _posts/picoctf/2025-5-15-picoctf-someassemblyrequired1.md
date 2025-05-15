---
title: Some Assembly Required 1
date: 2025-05-15 11:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, some assembly required 1, webexplotation]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>Some Assembly Required 1
Author: Sears Schulz
Description
http://mercury.picoctf.net:37669/index.html
{: .prompt-tip }


Si miramos el código fuente de la página observamos el enlace al código fuente de javascript 

``` html
<html>
<head>
	<meta charset="UTF-8">
	<script src="G82XCw5CX3.js"></script>
</head>
<body>
	<h4>Enter flag:</h4>
	<input type="text" id="input"/>
	<button onclick="onButtonPress()">Submit</button>
	<p id="result"></p>
</body>
</html>
```

El contenido del archivo G82XCw5CX3.js sería el siguiente teniendo en cuenta que esta todo en una línea, uso [https://beautifier.io/](https://beautifier.io/) que nos permite arreglar un poco la identación y hacer más comprensible el código. Bueno solo en parte pues
como se puede apreciar el nombre de las variables no son lo más apropiado para la programación

``` javascript
const _0x402c = ['value', '2wfTpTR', 'instantiate', '275341bEPcme', 'innerHTML', '1195047NznhZg', '1qfevql', 'input', '1699808QuoWhA', 'Correct!', 'check_flag', 'Incorrect!', './JIFxzHyW8W', '23SMpAuA', '802698XOMSrr', 'charCodeAt', '474547vVoGDO', 'getElementById', 'instance', 'copy_char', '43591XxcWUl', '504454llVtzW', 'arrayBuffer', '2NIQmVj', 'result'];
const _0x4e0e = function(_0x553839, _0x53c021) {
    _0x553839 = _0x553839 - 0x1d6;
    let _0x402c6f = _0x402c[_0x553839];
    return _0x402c6f;
};
(function(_0x76dd13, _0x3dfcae) {
    const _0x371ac6 = _0x4e0e;
    while (!![]) {
        try {
            const _0x478583 = -parseInt(_0x371ac6(0x1eb)) + parseInt(_0x371ac6(0x1ed)) + -parseInt(_0x371ac6(0x1db)) * -parseInt(_0x371ac6(0x1d9)) + -parseInt(_0x371ac6(0x1e2)) * -parseInt(_0x371ac6(0x1e3)) + -parseInt(_0x371ac6(0x1de)) * parseInt(_0x371ac6(0x1e0)) + parseInt(_0x371ac6(0x1d8)) * parseInt(_0x371ac6(0x1ea)) + -parseInt(_0x371ac6(0x1e5));
            if (_0x478583 === _0x3dfcae) break;
            else _0x76dd13['push'](_0x76dd13['shift']());
        } catch (_0x41d31a) {
            _0x76dd13['push'](_0x76dd13['shift']());
        }
    }
}(_0x402c, 0x994c3));
let exports;
(async () => {
    const _0x48c3be = _0x4e0e;
    let _0x5f0229 = await fetch(_0x48c3be(0x1e9)),
        _0x1d99e9 = await WebAssembly[_0x48c3be(0x1df)](await _0x5f0229[_0x48c3be(0x1da)]()),
        _0x1f8628 = _0x1d99e9[_0x48c3be(0x1d6)];
    exports = _0x1f8628['exports'];
})();

function onButtonPress() {
    const _0xa80748 = _0x4e0e;
    let _0x3761f8 = document['getElementById'](_0xa80748(0x1e4))[_0xa80748(0x1dd)];
    for (let _0x16c626 = 0x0; _0x16c626 < _0x3761f8['length']; _0x16c626++) {
        exports[_0xa80748(0x1d7)](_0x3761f8[_0xa80748(0x1ec)](_0x16c626), _0x16c626);
    }
    exports['copy_char'](0x0, _0x3761f8['length']), exports[_0xa80748(0x1e7)]() == 0x1 ? document[_0xa80748(0x1ee)](_0xa80748(0x1dc))[_0xa80748(0x1e1)] = _0xa80748(0x1e6) : document[_0xa80748(0x1ee)](_0xa80748(0x1dc))[_0xa80748(0x1e1)] = _0xa80748(0x1e8);
}
```

Después de un análisis de código me doy cuenta de que el código javascript hace uso de WebAssembly, esto me hizo pensar que pasaba si
accedo a esa web que parece involucrada de alguna manera [http://mercury.picoctf.net:37669/JIFxzHyW8W](http://mercury.picoctf.net:37669/JIFxzHyW8W)

```
�asm���`��``�`��p�1A°�A°�A�A°
�A�A°�A��A¡memory�__wasm_call_ctors��strcmp�
check_flag�input	copy_char�__dso_handle
__data_end
__global_base__heap_base
__memory_base__table_base
ú�ç*#�!A !  k!  �6  6 (!  6 (!  6@@ (!A!  j!	  	6 -��!
  
:� (!A!  j!
  
6 -��!  :�
 -�!Aÿ!  q!@ 
� -�!Aÿ!  q! -�
!Aÿ!  q!  k!  6 -�!Aÿ!  q! -�
!Aÿ!  q! ! !    F!!A!" ! "q!# #
� -�!$Aÿ!% $ %q!& -�
!'Aÿ!( ' (q!) & )k!*  *6 (!+ +LA�!�A°�!A�!  �! ! �!  G!A!  s!A!	  	q!
 
?#�!A!  k!  �6  6 (! (!  :�°�2�A+picoCTF{a8bae10f4d9544110222c2d639dc6de6}��
```
flag **picoCTF{a8bae10f4d9544110222c2d639dc6de6}**

Por una parte este reto requiere un análisis de código "ofuscado" y por otra la respuesta estaba más cerca de lo que llegué a pensar
que tendría que reescribir el código para intentar darle forma pero no es necesario.  