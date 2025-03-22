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

<h4>Enviar los datos configurados en telegraf a una API de influxdb</h4><br>
En este caso vamos a enviar los datos que cumplen unos patrones, para ellos me baso en un módulo de telegraf  "grok"
Configuramos telegraf para los input y para los outputs, para los segundos, queremos que se envíen a un bucket de influxdb_v2:<br>

[[inputs.tail]]<br>
  files = ["/var/log/nginx/access_geo.log"]<br>
  from_beginning = false<br>
  name_override = "nginx_logs"<br>
  data_format = "grok"<br>

  grok_patterns = [<br>
    '%{IP:client_ip} - - \[%{HTTPDATE:time}\] "%{WORD:method} %{URIPATHPARAM:request} HTTP/%{NUMBER:http_version}" %{NUMBER:status} %{NUMBER:bytes} "%{DATA:referrer}" "%{DATA:user_agent}" %{WORD:country_code} %{DATA:country_name} %{DATA:city_name} %{NUMBER:latitude} %{NUMBER:longitude}'<br>
  ]<br>


[[outputs.influxdb_v2]]<br>
  urls = ["http://localhost:8086"]<br>
  token = "HCjyQlB2spu_eB6Jd5bKGMyTfezVyiYgbh-BTMbzgkB9oHAzTD5zb6kd4va659X408dxknh_MM12AMvuNNY2ZQ=="<br>
  organization = "home"<br>
  bucket = "nginx_geo_logs"<br>
  timeout = "5s"<br>

<br>

Añadimos una configuración a nginx <br>

Configuracion de site en nginx para crear una api de status que se enviará luego con telegraf <br>

/etc/nginx/sites-avaliable/nginx_status.conf

<shell>
  ## An array of Nginx stub_status URI to gather stats. <br>
  urls = ["http://127.0.0.1:8089/nginx_status"]<br>

  ## Optional TLS Config<br>
  # tls_ca = "/etc/telegraf/ca.pem"<br>
  # tls_cert = "/etc/telegraf/cert.pem"<br>
  # tls_key = "/etc/telegraf/key.pem"<br>
  ## Use TLS but skip chain & host verification<br>
  # insecure_skip_verify = false<br>

  ## HTTP response timeout (default: 5s)<br>
  response_timeout = "5s"<br>
</shell>

<br>
Una vez configurado podemos acceder a [[http://127.0.0.1:8089/nginx_status]]<br>
<shell>
Active connections: 1 <br>
server accepts handled requests<br>
 1 1 1 <br>
Reading: 0 Writing: 1 Waiting: 0 <br>
</shell>

Ahora usaremos Telegraf para enviar los datos a la api de influxdb u otras <br>

telegraf --config http://localhost:8086/api/v2/telegrafs/0e8b750ee8d03000
