---
title: Cloudflare servicios gratuitos
date: 2025-07-21 11:00:00 +0200
categories: [ciberseguridad, redes]
tags: [Cloudflare, servicios gratuitos]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/4/4b/Cloudflare_Logo.svg/330px-Cloudflare_Logo.svg.png
    alt: Logo de Cloudflare
---

# Cloudflare

Según Wikipedia:  
> *Cloudflare, Inc.* es una empresa estadounidense que proporciona una red de entrega de contenido, servicios de seguridad de Internet y servicios de servidores de nombres de dominio distribuidos, localizados entre el visitante y el proveedor de alojamiento del usuario de Cloudflare, y que actúan como proxy inverso para sitios web.

Sin entrar demasiado en qué es Cloudflare, iré más a la parte práctica, o mejor dicho, intentaré resumir qué servicios podemos utilizar de forma gratuita a día de hoy, este artículo está sujeto a los cambios que Cloudflare pueda introducir en el futuro y por lo tanto en el futuro puede que los servicios hayan variado o sido modificados o incluso que dejen de ser gratuitos.

## Servicios gratuitos 

### Análisis de tráfico web
Ofrece estadísticas del tráfico, un mapa geográfico por países y una vista más detallada de la actividad web.

### Registros DNS
Cloudflare permite usar su DNS para registrar dominios, CNAMEs, etc. Esto significa que los dominios registrados se resuelven mediante los DNS de Cloudflare, lo que suele ofrecer una mejora de velocidad y seguridad.

### Enrutamiento de correos electrónicos
Una opción muy interesante: permite enrutar el correo y redirigirlo a la dirección deseada sin necesidad de instalar un servidor de correo en el dominio, a no ser que quieras enviar correos desde dicho dominio, para eso Cloudflare todavía no nos da un buzón aunque desde Gmail se puede agregar la cuenta y cambiar el remitente. Incluso existe una opción para automatizar el procesado de correos con scripting, mediante los llamados **mailworkers**.

### Certificados SSL/TLS
Cloudflare cifra el tráfico entre los visitantes y su red mediante HTTPS (puerto 443). Funciona como un servidor proxy inverso, ofreciendo sus propios certificados SSL/TLS. Esto también ayuda a ocultar la IP real del servidor de origen, aumentando la seguridad.

![proxy inverso](https://upload.wikimedia.org/wikipedia/commons/thumb/6/67/Reverse_proxy_h2g2bob.svg/330px-Reverse_proxy_h2g2bob.svg.png)

### Seguridad
Cloudflare permite configurar reglas de protección contra ataques básicos, como bloqueo por IP, país o patrones de tráfico sospechoso.

### Auditoría de IA
Una opción útil para controlar accesos de bots e inteligencias artificiales al sitio. Puede utilizarse para detectar, limitar o bloquear este tipo de tráfico automatizado.

### Reglas
Permite modificar solicitudes y respuestas HTTP, redireccionar URLs, configurar parámetros de Cloudflare y ejecutar acciones específicas cuando se cumplen ciertas condiciones en las solicitudes.

### Scrape Shield
Una herramienta que ofusca el contenido frente a bots e IAs, y ayuda a evitar scraping masivo y spam automatizado.

### Otras funcionalidades no descritas
Existen muchas otras opciones, pero este artículo se centra en lo esencial. Algunas características avanzadas como registros detallados (logs), firewall de aplicaciones web (WAF), almacenamiento en caché, etc., están disponibles solo en planes de pago.

Aun así, para quienes deseen usarlo de forma gratuita, Cloudflare ofrece una excelente gama de herramientas y un nivel de seguridad muy decente sin coste alguno.

### Resumen Cloudflare Gratuito
Desplegar una aplicación web en servidores como Apache, Nginx o Httpd generalmente implica configurar certificados SSL/TLS para asegurar las conexiones y proteger el sitio contra amenazas como ataques DDoS o vulnerabilidades web. Tradicionalmente, esto requería:

- Certificados SSL (usualmente gestionados con Let's Encrypt y herramientas como Certbot)
- Un balanceador como HAProxy para mejorar la seguridad y la disponibilidad
- Configuraciones adicionales de firewall (WAF) y mitigación de ataques

Estas soluciones requieren tiempo, conocimientos técnicos y recursos de infraestructura.
Sin embargo, con el plan gratuito de Cloudflare, se puede:

- Obtener y gestionar certificados SSL automáticos
-  Activar protección contra ataques DDoS
-  Usar un WAF básico para proteger la aplicación
-  Evitar la configuración manual de servicios como HAProxy y Certbot

Se simplifica la arquitectura, se reducen costes en infraestructura y mantenimiento, y se mejora la seguridad y rendimiento desde el primer momento. ¿Tampoco está tan mal no?

