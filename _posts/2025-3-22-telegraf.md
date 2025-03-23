---
title: Configurar Telegraf para InfluxDB
date: 2025-03-21 17:00:00 +0200
categories: [linux, software] 
tags: [telegraf, monitorización]     # TAG names should always be lowercase
author: pablo
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/c/c6/Influxdb_logo.svg/250px-Influxdb_logo.svg.png
    alt: Primer telégrafo
---

<h4>Enviar los datos configurados en telegraf a una API de influxdb</h4>
En este caso vamos a enviar los datos que cumplen unos patrones, para ellos me baso en un módulo de telegraf  "grok"
Configuramos telegraf para los input y para los outputs, para los segundos, queremos que se envíen a un bucket de influxdb_v2:
```
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
Añadimos una configuración a nginx 

Configuracion de site en nginx para crear una api de status que se enviará luego con telegraf 

/etc/nginx/sites-avaliable/nginx_status.conf

```
  ## An array of Nginx stub_status URI to gather stats. 
  urls = ["http://127.0.0.1:8089/nginx_status"]

  ## Optional TLS Config
  # tls_ca = "/etc/telegraf/ca.pem"
  # tls_cert = "/etc/telegraf/cert.pem"
  # tls_key = "/etc/telegraf/key.pem"
  ## Use TLS but skip chain & host verification
  # insecure_skip_verify = false

  ## HTTP response timeout (default: 5s)
  response_timeout = "5s"
```


Una vez configurado podemos acceder a [nginx_status](http://127.0.0.1:8089/nginx_status)
```
Active connections: 1 
server accepts handled requests
 1 1 1 
Reading: 0 Writing: 1 Waiting: 0 
```

Ahora usaremos Telegraf para enviar los datos a la api de influxdb u otras 
```
telegraf --config http://localhost:8086/api/v2/telegrafs/0e8b750ee8d03000
```