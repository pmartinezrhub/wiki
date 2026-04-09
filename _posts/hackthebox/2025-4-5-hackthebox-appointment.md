---
title: Appointment
date: 2025-04-5 00:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, sqli, sqlinjection, appointment]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/a9ddcda8d2f6eb388c6717de2caff896.png
    alt: Hack The Box
---
## Appointment
Otra vez una lista de tareas con preguntas a contestar

 ## Task 1

What does the acronym SQL stand for?
>Structured Query Language

## Task 2

What is one of the most common type of SQL vulnerabilities?
>sql injection

## Task 3

What is the 2021 OWASP Top 10 classification for this vulnerability?
A03:2021–Injection 

## Task 4

What does Nmap report as the service and version that are running on port 80 of the target?
Apache httpd 2.4.38 ((Debian))

## Task 5

What is the standard port used for the HTTPS protocol?
>443


## Task 6

What is a folder called in web-application terminology?
>directory

## Task 7

What is the HTTP response code is given for 'Not Found' errors?
>404

## Task 8

Gobuster is one tool used to brute force directories on a webserver. What switch do we use with Gobuster to specify we're looking to discover directories, and not subdomains?
>dir

## Task 9

What single character can be used to comment out the rest of a line in MySQL?

## Task 10
>#

If user input is not handled carefully, it could be interpreted as a comment. Use a comment to login as admin without knowing the password. What is the first word on the webpage returned?

Introducimos la "'" y la almohadilla para cortar la consulta 
```
admin'# 
```

>Congratulations


## Submit Flag
>

## Submit root flag https://www.hackthebox.com/achievement/machine/2336390/402
