---
title: Chirpy 
date: 2025-03-21 17:00:00 +0200
categories: [software, blog]
tags: [software, blog]     # TAG names should always be lowercase
image:
    path: https://chirpy-img.netlify.app/commons/avatar.jpg
    alt: https://chirpy.cotes.page/
---


Estrenando un blog en github en unos poco pasos con Chirpy 


<a href="https://chirpy.cotes.page">https://chirpy.cotes.page</a>

<h4>Pasos a seguir</h4> 

Inicia sesión en GitHub y accede a la página <a href="https://github.com/cotes2020/chirpy-starter">https://github.com/cotes2020/chirpy-starter</a>

Haz clic en el botón "Usar esta plantilla" y selecciona "Crear un nuevo repositorio".

Nombra el nuevo repositorio <nombreusuario>.github.io, reemplazando <b>"nombreusuario"</b> por tu nombre de usuario de GitHub en minúsculas.

Ahora debes editar el archivo _config.yml en donde pone url debemos rellenar como nos indica el ejemplo 

``` 
# Fill in the protocol & hostname for your site.
# E.g. 'https://username.github.io', note that it does not end with a '/'. 
url: "https://pmartinezrhub.github.io" (en mi caso)
```

Ahora desde github Actions podrás publicar el blog. 

Y ya está? Bueno ahora te falta configurar el estilo, crear unos favicons y meterlos en  assets/img/favicons (si no exite lo creas),  crear algún post 

¿Ya, pero una vez que lo tenga, como puedo probarlo localmente?

``` shell
bundle exec jekyll s
```
 Si necesitas instalar las dependencias te indicará que ejecutes
 ```
 bundle install 
 ```
