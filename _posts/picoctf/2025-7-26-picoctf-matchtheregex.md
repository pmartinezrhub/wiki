---
title: MatchTheRegex
date: 2025-07-26 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>MatchTheRegex
Author: Sunday Jacob Nwanyim
Description
How about trying to match a regular expression The website is running here.
{: .prompt-tip }

La pista nos dice que intentemos igualar la expresión regular asociada con el campo de texto.
Vamos que adivinemos la cadena a introducir, la web se presenta como un formulario

```
Valid Input

```

Si miramos el código fuente de la página nos encontramos lo que parece otra pista en el script de envío del request.
``` javascript
<script>
	function send_request() {
		let val = document.getElementById("name").value;
		// ^p.....F!?
		fetch(`/flag?input=${val}`)
			.then(res => res.text())
			.then(res => {
				const res_json = JSON.parse(res);
				alert(res_json.flag)
				return false;
			})
		return false;
	}

</script>
```

Este código comentado parece el típico error de haber dejado "hardcodeado" parte de la clave, mejor dicho la  expresión regular necesaria. Si analizamos esta expresión entonces se puede deducir que la cadena a introducir debería comenzar por p, luego 5 carácteres y acabar por F. La "?" significa que el carácter anterior "!" es opcional, con lo cual da a enteder que podemos ignorar esos dos últimos. Ya con esto podemos resolver que la cadena será **picoCTF**, si bien puedes introducir cualquier expresión que cumpla con esta expresión por ejemplo **pabcdeF**

flag: **picoCTF{succ3ssfully_matchtheregex_9080e406}**


