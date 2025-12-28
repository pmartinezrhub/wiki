---
title: hashcrack
date: 2025-05-16 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, hashcrack, criptografía, hashcat]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>hashcrack
Author: Nana Ama Atombo-Sackey
Description
A company stored a secret message on a server which got breached due to the admin using weakly hashed passwords. Can you gain access to the secret stored within the server? Access the server using nc verbal-sleep.picoctf.net 61522
{: .prompt-tip }


``` shell
pablo☠office pentest$ nc verbal-sleep.picoctf.net 61522
Welcome!! Looking For the Secret?

We have identified a hash: 482c811da5d5b4bc6d497ffa98491e38

```
Con hash-identifier puedes intentar identificar a que tipo de cifrado pertenece el hash [hacktools/#password-cracking](../hacktools/#password-cracking) Si bien hashcat por si mismo es capaza de indentificar algunos de ellos.

``` shell
pablo☠office hash-identifier$ python hash-id.py 482c811da5d5b4bc6d497ffa98491e38
   #########################################################################
   #     __  __                     __           ______    _____           #
   #    /\ \/\ \                   /\ \         /\__  _\  /\  _ `\         #
   #    \ \ \_\ \     __      ____ \ \ \___     \/_/\ \/  \ \ \/\ \        #
   #     \ \  _  \  /'__`\   / ,__\ \ \  _ `\      \ \ \   \ \ \ \ \       #
   #      \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \      \_\ \__ \ \ \_\ \      #
   #       \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/      #
   #        \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.2 #
   #                                                             By Zion3R #
   #                                                    www.Blackploit.com #
   #                                                   Root@Blackploit.com #
   #########################################################################
--------------------------------------------------

Possible Hashs:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))
```

Ahora con Hashcat y una versión de el diccionario más conocido del mundo del pentesting rockyou.txt
``` shell
pablo☠office pentest$ hashcat "482c811da5d5b4bc6d497ffa98491e38" -m 0 wordlist/rockyou.txt 

Dictionary cache built:
* Filename..: wordlist/rockyou.txt
* Passwords.: 10000000
* Bytes.....: 97540867
* Keyspace..: 10000000
* Runtime...: 2 secs

482c811da5d5b4bc6d497ffa98491e38:password123   
```
>password123

``` shell
pablo☠office hash-identifier$ nc verbal-sleep.picoctf.net 61522
Welcome!! Looking For the Secret?

We have identified a hash: 482c811da5d5b4bc6d497ffa98491e38
Enter the password for identified hash: password123
Flag is yet to be revealed!! Crack this hash: b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3
Enter the password for the identified hash:
```

Presupongo que tengo que crackear este segundo password así que trato de identificarlo

```
python hash-id.py b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3

Possible Hashs:
[+] SHA-1
[+] MySQL5 - SHA-1(SHA-1($pass))

pentest$ hashcat -m 100 "b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3" wordlist/rockyou.txt 
Dictionary cache built:
* Filename..: wordlist/rockyou.txt
* Passwords.: 14344390
* Bytes.....: 139922176
* Keyspace..: 14344383
* Runtime...: 2 secs

b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3:letmein       
```
>letmein 

El resto no termina aquí
```
pablo☠office pentest$ nc verbal-sleep.picoctf.net 61522
Welcome!! Looking For the Secret?

We have identified a hash: 482c811da5d5b4bc6d497ffa98491e38
Enter the password for identified hash: password123
Correct! You've cracked the MD5 hash with no secret found!

Flag is yet to be revealed!! Crack this hash: b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3
Enter the password for the identified hash: letmein
Correct! You've cracked the SHA-1 hash with no secret found!

Almost there!! Crack this hash: 916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745
Enter the password for the identified hash: 
```

Así que procedemos a indentificar el hash y crackearlo

```shell
pablo☠office pentest$ python hash-identifier/hash-id.py 
HASH: 916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745

Possible Hashs:
[+] SHA-256
[+] Haval-256

hashcat "916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745" -m 1400 wordlist/rockyou.txt 
916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745:qwerty098
```

>qwerty098

``` shell
pablo☠office pentest$ nc verbal-sleep.picoctf.net 61522
Welcome!! Looking For the Secret?

We have identified a hash: 482c811da5d5b4bc6d497ffa98491e38
Enter the password for identified hash: password123
Correct! You've cracked the MD5 hash with no secret found!

Flag is yet to be revealed!! Crack this hash: b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3
Enter the password for the identified hash: letmein
Correct! You've cracked the SHA-1 hash with no secret found!

Almost there!! Crack this hash: 916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745
Enter the password for the identified hash: qwerty098    
Correct! You've cracked the SHA-256 hash with a secret found. 
The flag is: picoCTF{UseStr0nG_h@shEs_&PaSswDs!_36a1cf73}
```

flag **picoCTF{UseStr0nG_h@shEs_&PaSswDs!_36a1cf73}**

Indentificar los hashes y hacer fuerza bruta contra ellos. 