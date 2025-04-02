---
title: Securizar routers inalámbricos
date: 2025-03-24 11:00:00 +0200
categories: [ciberseguridad, redes]
tags: [redes, wifi, seguridad, router]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/3/30/Fritz%21Box_Fon_WLAN_7141_-_Typenschild-3743.jpg
    alt: WPA key
---

## Instalar un router hoy en día, parece una cosa trivial y sencilla
Puedes tener tu equipo navegando por internet en tres sencillos pasos:
- Lo enchufas a la corriente
- Luego lo conectas a un modem por el puerto WAN 
- Copias la contraseña que trae en la etiqueta del fabricante y la aplicas a la configuración
de red inalámbrica de un equipo

Listo! ya está funcionando, todo es felicidad y una sensación de omnipotencia invade al usuario medio, pues esto para el,
es cosa de "hackers" pues el deconocía que el 5 puerto (el que estaba separado un pelín del resto) era el puerto WAN hasta que lo ha leído. 

## Evaluar los riesgos
Mucha gente instala aparatos como enrutados en sus viviendas, negocios
y oficinas sin haber hecho una evaluación de los riesgos que corren. 
Evaluar que pasaría si su información o su red se viera comprometida
> Manolo quiere poner un router wifi para que sus camareros trabajen con PDAs en su bar, de esa forma moderniza el negocio,
es más ecológico que el papel además de que una compañía tecnológica se ha trasladado a unas oficinas cercanas 
y quiere atraer clientes. Por ahorrarse una 27001 decide que lo montará el mismo,
así que ni corto ni perezoso compra un router y sustituye el router de su operador que tenía conectado a 
al pc de facturación y en su lugar pone un router wifi supermolón, con 6 antenas que le recomendó su cuñado, una ganga. 

Manolo, desconoce que ese router tiene una configuración de red para invitados donde podría poner "WIFI-BARMANOLO" y otra con SSID oculta.
Pero el mayor fallo de Manolo es no haber pensado en ningún momento si hacer esta instalación podría haber supuesto un riesgo para la integridad 
de su negocio. 
Si Manolo tuviera la capacidad de haber pensado en que aislaría la red de invitados de la red interna donde está el PC facturación, 
que además, ¿Quien sabe si todavía usa Windows7 o Windows XP? entonces habría evaluado el riesgo que correría si no hacía una segmentación de la red.
Ahora calculen los riesgos, si un adversario, realizase un ataque al pc de facturación y consiguiese instalar algún tipo de troyano de control.

## Formación sobre ciberseguridad
El primer eslabón débil de la cadena en la cibersegurida es el usuario mal formado. 
Herramientas como Fluxión o Wifiphisher pueden hacer que el usuario participe de la exposición de un sistema por el hecho de no haber sido instruído en materia de seguridad, enlaces a estas herramientas en este otro post => [Hacktools#wifi](../hacktools#wifi) 

Hoy en día nos llegan SMS con origen de nuestro propio banco, correos que parecen suplantar a distintas entidades gubernamentales o finacinacieras pidiendo que leamos documentos curiosamente sospechosos de contener troyanos, mensajes de Whatsapp pidiéndonos visitar enlaces, etc, etc..
La falta de formación es lo que puede marcar la diferencia entre un desastre y un simple incidente de ciberseguridad. Aprender a identificar las amenazas y actuar correctamente cuando ocurren es la mejor forma de protegerse ante los adversarios.

## Actualización de los sistemas de red
Como cualquier sistema informático los routers también se actualizan, mantenerlos actualizados ayuda a mejorar la seguridad. En general siguiendo los pasos del fabricante se les puede actualizar el firmware y/o cambiarlo sin ningún problema o incidencia. Hay gente que no se atreve porque lo consideran un paso muy delicado y que pueden "romper" (en realidad el termino se conoce como brikear) el router. La mayoría de veces también se puede recuperar un router brikeado

## Custodia de las contraseñas
Como siempre las passwords son un factor esencial en este proceso, un teléfono sin bloquear o sin cierta seguridad podría permitir a un atacante obtener la información sobre las contraseñas de las redes wifi, en Android este paso es tan fácil como compartir la red con un código QR, o simplemente apuntarse la contraseña para utilizarla posteriormente. 

## Activos del sistema
En este aspecto de la segurida hay que tener muy presente los activos en materia de ciberseguridad y cuales de ellos puede o no participar
 en los sistemas. Si entre tus activos , en este caso, tablets, móviles y demás elementos que se conecten a la red suponen una debilidad 
 en el sistema, retíralos del mismo. 

## Claves por defecto
### Claves en etiquetas del fabricante
Sí, hay que tener cuidado con las contraseñas por defecto, pues muchas de ellas podrían estar al alcance del atacante simplemente dándole la vuelta al router y sacándo una foto o apuntando la clave. 
Evaluar si el dispositivo estaría al posible alcance de un adversario y ubicarlo de la mejor manera posible 
### Claves de administración por defecto
Como ya muchos saben los ruters tienen unas contraseñas por defecto para entrar en su configuración y alterala.
Típico es que el usuario sea __admin__ con password __admin__. 

## Ocultar SSID
Ocultar la SSID, es un tema interesante. Aunque esto se conoce como seguridad por oscuridad, no evita que con herramientas con aircrack-ng se pueda detectar una red inalámbrica con SSID oculto.
Además el activarlo puede causar problemas con algunos dispositivos. Pero como toda medida es poca, siempre cabe esta posibilidad.

## Sistemas de autenticación
Hoy por hoy casi todos los routers utilizar WPA2 a diferencia con WPE, que fue un desastre en seguridad y que muchos tuvimos diversión infinita con ellos. 
WPA y sucesivos son utilizan sistemas de cifrado mucho más seguros y difíciles de romper. Eso sí, usa claves decentes. 
Hubo mucha obsesión con intentar romper claves WPA a través de tablas precomputadas Rainbow Tables, pero además de costoso,
si la contraseña es suficientemente larga además es inútil.
WPA3 es más seguro. 

## Control horario
Es una opción en ciberseguridad el control por horario y algunos routers permiten justamente apagar la wifi según los horarios que especifiquemos.
¿Tu negocio, hogar necesita la Wifi encendida 24hx365dias? Si la respuesta es no, por seguridad comprueba si tu router te permite configurar esta opción.
Además ahorrarás unos pocos watios anuales de electricidad.

## Desactivación WPS
Hay que tener mucho cuidado con el WPS, parece ser útil en algunas circunstancias pero en el hogar nadie lo usa. 
Además cualquier sistema de autentificación basada en un PIN siempre es más vulnerable a cualquier tipo de ataque de fuerza bruta. 
Conozco varias herramientas automáticas para explotar esta vulnerabilidad dos de ellas puedes verlas en la lista [Hacktools#wifi](../hacktools#wifi) 

## Filtrado por MAC
El filtrado por MAC es otra medida de seguridad posible pero que los hackers pueden salvar, 
simplemente clonando la MAC de algunos de los dispostivos conectaods a la red.

## Red de invitados
Es siempre una buena idea de seguridad la segmentación de la red. Por lo que separar a los invitados de la red princpial 
siempre debe considerarse como una opción.
La recomiendo incluso para usarla con los terminales inalámbricos, o sea, todos esos cacharros que no necesitan 
conectarse a la infraestructura de la red interna, claro está, previa activación de la autenticación en dicha red pues por defecto suelen venir sin contraseña.

## Auditoría interna
Para asegurarno de que no se encuentran vulnerabilidades en nuestros dispositivos inalámbricos
se deberán realizar auditorías de seguridad interna, valiéndonos de herramienta para tal fin.
Esta herramienta permite hacer un escaneo automático de vulnerabilidades sobre tu router
Puedes ver el repositorio en la lista de herramientas [Hacktools#router](../hacktools#router)

## Otras configuraciones
Algunos routers permite desactivar la administración remota, también no contestar a ping remotos. 
Cada fabricante ofrece opciones diferentes, revisar sus opciones para mejorar la seguridad.
Algunos firmwares como OpenWRT o similares ofrecen módulos que pueden ofrecer un extra de seguridad.
Cuidado con utilizar opciones como activar servidores minidnla, compartir discos USB , samba, etc.


