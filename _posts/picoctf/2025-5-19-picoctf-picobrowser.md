---
title: picobrowser
date: 2025-05-19 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, picobrowser, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>picobrowser
Author: Archit
Description
This website can be rendered only by picobrowser, go and catch the flag! https://jupiter.challenges.picoctf.org/problem/26704/ (link) or http://jupiter.challenges.picoctf.org:26704
{: .prompt-tip }

Aparece una botón "Flag", al pulsarlo  no saldrá un mensaje

>You're not picobrowser! Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0 

¿Que pasa si cambiamos el User Agent? Pues nos da una pista de que no necesitamos un nuevo navegador. Esto de cambiar el User Agent
se puede hacer de varias formas, con una extensión para el navegador o con Burp/Zap capturando el request y alterandolo, voy a usar picobrowser como UserAgent y lo voy a retulizar para la request.

```
GET https://jupiter.challenges.picoctf.org/flag HTTP/1.1
Host: jupiter.challenges.picoctf.org
User-Agent: picobrowser
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Connection: keep-alive
Referer: https://jupiter.challenges.picoctf.org/problem/26704/flag
Cookie: cf_clearance=46OM_JMkFbGYAXFui9eqQzSrq2aYINwQsYrNYiDRFTE-1747690053-1.2.1.1-BHFuPEyGmJizR.qqYJshGbxA1m22Xy_OLBa5W.Dw.EvIbw7xYvaN411GqlXRIXfg8FWIEvJywFWWhwFaTbMj.Rx8gqwY2y95ly2TBazHDZrbdDJm5QZwfeKSA56fhodWn8eVJvrSzupf8wF631v2WIdiw9hGiIfIzNWJdYEnGsDv3RMKZWsyEjlW492Dob52PTkRdpAsaiXHg_Ah81_fT9xvkdWEmyB8fFBGuNhDmO1MIjEmV5pERWLb0YdkQ2hsgC8VPakmgC7tJNsdY4XxXXvdfbqO6E6NCGnlaoFaKgbzvFfGWeiIWm.PglLshYVbsRr1FcR1Ee5KJtUWkuo1WGLn_IaJkuDxzNzN1vk8Io0
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
```


```
picoCTF{p1c0_s3cr3t_ag3nt_e9b160d0}</code></p>
```

Otra forma de hacerlo es desde el propio navegador, en herramientos, editamos el request y cambiamos el user agent y volvemos a enviar la petición.

```
 picobrowser!

Flag: picoCTF{p1c0_s3cr3t_ag3nt_e9b160d0}

```

flag:  **picoCTF{p1c0_s3cr3t_ag3nt_e9b160d0}**