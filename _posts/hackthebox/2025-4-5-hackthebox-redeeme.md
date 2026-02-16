---
title: Redeemer
date: 2025-04-5 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, redeeme, redis]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/cdf77651ab0a4eca65acd5cf388b4c66.png
    alt: Hack The Box
---

## Redeemer
Siguiendo la línea de hacer unas tareas y contestar a las preguntas que se van planteando. 

 ## Task 1

Which TCP port is open on the machine?
```
┌──(kali㉿kali25)-[~]
└─$ nmap -sV -p 0-65000 --open    10.129.155.79 -vv
PORT     STATE SERVICE REASON         VERSION
6379/tcp open  redis   syn-ack ttl 63 Redis key-value store 5.0.7
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 92.94 seconds
           Raw packets sent: 89997 (3.960MB) | Rcvd: 71479 (2.859MB)
``` 
>6379

## Task 2

Which service is running on the port that is open on the machine?

>redis

## Task 3
What type of database is Redis? Choose from the following options: (i) In-memory Database, (ii) Traditional Database

>In-memory Database
## Task 4

Which command-line utility is used to interact with the Redis server? Enter the program name you would enter into the terminal without any arguments.

>redis-cli

## Task 5

Which flag is used with the Redis command-line utility to specify the hostname?
>-h

## Task 6

Once connected to a Redis server, which command is used to obtain the information and statistics about the Redis server?
>info

## Task 7


What is the version of the Redis server being used on the target machine?
``` shell
┌──(kali㉿kali25)-[~]
└─$ nmap -sV -p 6379 -sV  10.129.155.79 -vv 
PORT     STATE SERVICE REASON         VERSION
6379/tcp open  redis   syn-ack ttl 63 Redis key-value store 5.0.7
┌──(kali㉿kali25)-[~]
└─$ nmap  --script redis-info -p 6379  10.129.155.79 -vv
PORT     STATE SERVICE REASON
6379/tcp open  redis   syn-ack ttl 63
| redis-info: 
|   Version: 5.0.7
|   Operating System: Linux 5.4.0-77-generic x86_64
|   Architecture: 64 bits
|   Process ID: 754
|   Used CPU (sys): 2.062208
|   Used CPU (user): 1.839607
|   Connected clients: 1
|   Connected slaves: 0
|   Used memory: 839.48K
|   Role: master
|   Bind addresses: 
|     0.0.0.0
|     ::1
|   Client connections: 
|_    10.10.15.134
┌──(kali㉿kali25)-[~]
└─$ redis-cli -h 10.129.155.79
10.129.155.79:6379> info
# Server
redis_version:5.0.7
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:66bd629f924ac924

```
> 5.0.7

## Task 8

Which command is used to select the desired database in Redis?
>select

## Task 9
How many keys are present inside the database with index 0?
>4

## Task 10

Which command is used to obtain all the keys in a database?
>keys *

## Submit Flag
>get flag.txt

## Submit root flag 
