---
title: Configurar Telegraf para InfluxDB
date: 2025-03-21 17:00:00 +0200
categories: [linux, software] 
tags: [telegraf, monitorización]     # TAG names should always be lowercase
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/c/c6/Influxdb_logo.svg/250px-Influxdb_logo.svg.png
    alt: Primer telégrafo
---

Para ponerte en contexto deberías leer antes [GeoIP Nginx](../nginx)
#### Enviar los datos configurados en telegraf a una API de influxdb
En este caso vamos a enviar los datos de los logs de Nginx que cumplen unos patrones, para ellos me baso en un módulo de telegraf  "grok"
Configuramos telegraf para los input y para los outputs, para los segundos, queremos que se envíen a un bucket de influxdb_v2:
``` shell
[[inputs.tail]]
  files = ["/var/log/nginx/access_geo.log"]
  from_beginning = false
  name_override = "nginx_logs"
  data_format = "grok"

  grok_patterns = [
    '%{IP:client_ip} - - \[%{HTTPDATE:time}\] "%{WORD:method} %{URIPATHPARAM:request} HTTP/%{NUMBER:http_version}" %{NUMBER:status} %{NUMBER:bytes} "%{DATA:referrer}" "%{DATA:user_agent}" %{WORD:country_code} %{DATA:country_name} %{DATA:city_name} %{NUMBER:latitude} %{NUMBER:longitude}'
  ]


[[outputs.influxdb_v2]]
  urls = ["http://localhost:8086"]
  token = "HCjyQlB2spu_eB6Jd5bKGMyTfezVyiYgbh-BTMbzgkB9oHAzTD5zb6kd4va659X408dxknh_MM12AMvuNNY2ZQ=="
  organization = "home"
  bucket = "nginx_geo_logs"
  timeout = "5s"
```

Ahora usaremos Telegraf para enviar los datos a la api de influxdb u otras 
``` shell
telegraf --config http://localhost:8086/api/v2/telegrafs/0e8b750ee8d03000
```