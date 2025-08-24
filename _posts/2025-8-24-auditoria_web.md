---
title: Como hacer una auditoría web
date: 2025-8-24 11:00:00 +0200
categories: [ciberseguridad, redes]
tags: [nuclei, escaner vulnerabilidades]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/e/e0/Deface_KenFM_AnonLeaks.png
    alt: Defacement 
---

# Como hacer una auditoría web paso por paso.

## 📐 1. Planificación y alcance

- Definir qué se va a auditar (dominio, subdominios, APIs, aplicaciones web).
- Obtener permisos formales (evitar problemas legales, papeles).
- Definir objetivos: cumplimiento normativo (ej. ISO 27001, OWASP, PCI-DSS) o búsqueda de vulnerabilidades críticas.
- Establecer el nivel de profundidad (caja negra, caja gris, caja blanca).

### Niveles de profundidad en una auditoría de ciberseguridad.
En cualquiera de los tres escenarios los pasos deberían ser los mismos pero,, cuando lo hacemos en el escenario de caja blanca o negra partimos con la ventaja de tener accesos directos en muchos casos. Evidentemente cuando alguien realiza una auditoría de caja blanca o gris, siempre pare lógico que se vayan a encontrar más vulnerabilidades, pues en muchos casos la ciberseguridad se aplica con más cuidado en aquellas partes sensibles de un sistema, como puede ser la vía de entrada de conexiones de usarios. 

## 🔎 2. Reconocimiento

## Reconocmiento pasivo, el que podemos realizar con la vista, mirando el código de la web, con extensiones del navegador no agresivos y/o fuentes OSINT y todos aquellos métodos que no levantarían sospechas de ser un reconocimiento. 
- Recolección de información pública (OSINT).
- Identificación de tecnologías usadas (CMS, frameworks, servidores, librerías). Wappalyzer, Firefox, CheckWeb, WhatWeb 
- Identificar los puntos de entrada de información, formularios, componentes, controles, etc. 

## Reconocimiento activo, usar técnicas de reconocimiento más agresivas como el fuzzing
- Descubirmiento de protecciones  Nmap,  wafw00f. 
- Enumeración de subdominios con dnsenum, subbrute, amass, fwuzz
- Descubrimiento de sitios no indexados (Path / Directory Traversal) 
- Fuzzing de directorios y archivos ocultos (ej. con ffuf, gobuster, dirsearch)
- Escaneo de puertos y servicios (Nmap con scripts NSE, masscan)
- Enumeración de APIs expuestas (buscando endpoints ocultos o mal configurados)
- Descubrimiento de paneles de administración ocultos
- Fingerprinting avanzado de aplicaciones web (versiones exactas de CMS, frameworks, plugins)
- Exploración de buckets y almacenamiento en la nube mal configurados (S3, Azure Blob, GCP buckets)
- Enumeración de usuarios o emails válidos (con técnicas como fuzzing en formularios de login o password reset)
- Crawling agresivo (rastreadores que ignoran robots.txt y siguen rutas no indexadas)
- Identificación de endpoints de debug o de prueba
- Pruebas de parámetros (descubrir parámetros ocultos en aplicaciones web con Arjun, ParamSpider)
- Detección de inyecciones SQL/NoSQL
- Detección de inyecciones XXS, CRSF
- Descubrimiento de debilidad de sesiones JWT, OAuth, sesiones, roles.
 
## 🔬 3. Análisis de Configuración

- Revisar encabezados HTTP (seguridad, caché, CORS).
- Verificar configuraciones SSL/TLS (fuerza de cifrado, certificados válidos).
- Detección de versiones obsoletas de software.
- Configuración de permisos en directorios, archivos y bases de datos.

## 🐞 4. Escaneo de Vulnerabilidades

- Escaneo automático con herramientas como OWASP ZAP, Nikto, Burp Suite, Nessusmm Nuclei.

### Identificación de vulnerabilidades OWASP Top 10:

1. A01:2021 – Control de Acceso Roto sube desde la quinta posición; el 94% de las aplicaciones fueron evaluadas por algún tipo de control de acceso roto. Las 34 debilidades comunes (CWEs) mapeadas a esta categoría tuvieron más ocurrencias en aplicaciones que cualquier otra categoría.

2. A02:2021 – Fallos Criptográficos sube una posición al #2, anteriormente conocido como Exposición de Datos Sensibles, que era más un síntoma amplio que una causa raíz. El nuevo enfoque aquí está en fallos relacionados con la criptografía, que a menudo conducen a la exposición de datos sensibles o al compromiso del sistema.

3. A03:2021 – Inyección baja a la tercera posición. El 94% de las aplicaciones fueron evaluadas por algún tipo de inyección, y las 33 CWEs asociadas a esta categoría tienen la segunda mayor cantidad de ocurrencias en aplicaciones. El Cross-Site Scripting ahora forma parte de esta categoría en esta edición.

4. A04:2021 – Diseño Inseguro es una categoría nueva en 2021, con foco en riesgos relacionados con fallos de diseño. Si realmente queremos “movernos a la izquierda” como industria, se requiere un mayor uso de modelado de amenazas, patrones y principios de diseño seguro, y arquitecturas de referencia.

5. A05:2021 – Configuración de Seguridad Incorrecta sube desde la posición #6 en la edición anterior; el 90% de las aplicaciones fueron evaluadas por algún tipo de mala configuración. Con el aumento del uso de software altamente configurable, no sorprende que esta categoría haya subido. La antigua categoría de Entidades Externas XML (XXE) ahora forma parte de esta categoría.

6.  A06:2021 – Componentes Vulnerables y Obsoletos antes titulada Uso de Componentes con Vulnerabilidades Conocidas, ocupa el puesto #2 en la encuesta comunitaria del Top 10, y también tuvo suficientes datos para entrar en el Top 10 por análisis. Esta categoría sube desde la posición #9 en 2017 y sigue siendo un problema conocido que es difícil de evaluar y gestionar. Es la única categoría sin vulnerabilidades conocidas (CVEs) mapeadas a las CWEs incluidas, por lo que se aplican valores por defecto de explotación e impacto de 5.0 en sus puntajes.

7. A07:2021 – Fallos de Identificación y Autenticación anteriormente llamada Autenticación Rota, baja desde la segunda posición y ahora incluye CWEs más relacionados con fallos de identificación. Esta categoría sigue siendo una parte esencial del Top 10, pero la mayor disponibilidad de marcos estandarizados parece estar ayudando.

8. A08:2021 – Fallos de Integridad de Software y Datos es una nueva categoría en 2021, enfocada en suposiciones relacionadas con actualizaciones de software, datos críticos y pipelines de CI/CD sin verificar su integridad. Tiene uno de los impactos más altos ponderados según datos de CVE/CVSS mapeados a las 10 CWEs en esta categoría. La Deserialización Insegura de 2017 ahora forma parte de esta categoría más amplia.

9. A09:2021 – Fallos en Registro y Monitoreo de Seguridad antes conocida como Registro y Monitoreo Insuficientes, se incluyó desde la encuesta de la industria (#3), subiendo desde la posición #10. Esta categoría se amplió para incluir más tipos de fallos, es difícil de evaluar y no está bien representada en los datos de CVE/CVSS. Sin embargo, las fallas en esta categoría pueden impactar directamente la visibilidad, alertas de incidentes y labores forenses.

10. A10:2021 – Server-Side Request Forgery (SSRF) fue añadida a partir de la encuesta comunitaria del Top 10 (#1). Los datos muestran una tasa de incidencia relativamente baja con una cobertura de pruebas superior al promedio, junto con calificaciones de explotación e impacto por encima del promedio. Esta categoría representa el escenario donde la comunidad de seguridad indica que es importante, incluso si los datos aún no lo reflejan.

## 🔓 5. Pruebas Manuales de Penetración

- Validar falsos positivos de los escáneres y los hayazgos.
- Intentar explotación controlada de vulnerabilidades.
- Analizar lógica de negocio (ej. saltar validaciones, manipular flujos de pago).
- Revisar autenticación, gestión de sesiones y control de accesos.
- Fuerza bruta contra formularios de login y/o críticos. 

## 📊 6. Evaluación de Impacto y Riesgos

- Clasificicón vulnerabilidades por criticidad (alta, media, baja).
- Analisis de impacto en confidencialidad, integridad y disponibilidad.
- Revisar riesgos de cumplimiento legal (ej. GDPR, PCI-DSS).

## 📝 7. Informe de Resultados

Documentar hallazgos con:

- Descripción de la vulnerabilidad.
- Evidencia (capturas, logs).
- Riesgo asociado.
- Recomendación de mitigación.
- Entregar un resumen ejecutivo para directivos y un detalle técnico para el equipo de desarrollo.

## 🛠️ 8. Corrección y Seguimiento

- Aplicar parches y medidas de seguridad.
- Configurar monitoreo y alertas.
- Repetir pruebas para verificar correcciones.
- Establecer políticas de mejora continua.



Este documento está sujeto a cambios por las propia naturaleza de la tecnología, evolución. 