---
title: EVEN RSA CAN BE BROKEN???
date: 2025-07-22 19:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, criptografía]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>EVEN RSA CAN BE BROKEN???
Author: Michael Crotty
Description
This service provides you an encrypted flag. Can you decrypt it with just N & e? Connect to the program with netcat: $ nc verbal-sleep.picoctf.net 51510 The program's source code can be downloaded here.
{: .prompt-tip }

```shell
pablo☠office pablo☠office EVEN-RSA-CAN-BE-BROKEN$ nc verbal-sleep.picoctf.net 51510
N: 19464700296545504943360349643885785244500838702379172331607798107569667137646252771323642762526628612861264627432535564405081850708415593669585875599284078
e: 65537
cyphertext: 7741921862502295378343494622143578417504200290986096113213693514908295903386350106897260361327523467308148104618734262671017238427508607426528014143546193

# Ctrl + c 
wget https://challenge-files.picoctf.net/c_verbal_sleep/3d9bb69ba246e705afca68c4ad295f7e603f0fd8ac7e6f25a409e3e5effd7d7c/encrypt.py
```
Que pueden ser N y e, pues si hablamos de problemas de factorización **N** debe ser un número y **e** un exponente, aunque yo no soy matemático, hay pistas en el código que podemos descargar en el enlace que nos proporcionan
En el código que hemos descargado **encrypt.py** nos dan pistas de como se cifró

``` python
from sys import exit
from Crypto.Util.number import bytes_to_long, inverse
from setup import get_primes

e = 65537

def gen_key(k):
    """
    Generates RSA key with k bits
    """
    p,q = get_primes(k//2)
    N = p*q
    d = inverse(e, (p-1)*(q-1))

    return ((N,e), d)

def encrypt(pubkey, m):
    N,e = pubkey
    return pow(bytes_to_long(m.encode('utf-8')), e, N)

def main(flag):
    pubkey, _privkey = gen_key(1024)
    encrypted = encrypt(pubkey, flag) 
    return (pubkey[0], encrypted)

if __name__ == "__main__":
    flag = open('flag.txt', 'r').read()
    flag = flag.strip()
    N, cypher  = main(flag)
    print("N:", N)
    print("e:", e)
    print("cyphertext:", cypher)
    exit()

```
Una cosa que sí se sobre la factorización es que se basa en números primos y este número, N, es par, En la vida real, n nunca sería par, porque p y q deben ser primos grandes y aleatorios, el 2, que es el único primo par. Así
que ya tenemos p y q. 

``` shell
pablo☠office EVEN-RSA-CAN-BE-BROKEN$ python 
Python 3.11.2 (main, Apr 28 2025, 14:11:48) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> print(19464700296545504943360349643885785244500838702379172331607798107569667137646252771323642762526628612861264627432535564405081850708415593669585875599284078 // 2)
9732350148272752471680174821942892622250419351189586165803899053784833568823126385661821381263314306430632313716267782202540925354207796834792937799642039
>>> 
```

Este reto es bastante duro si no eres matemático. Pero básicamente tenemos un problema de factorización. Así que le tuve que preguntar a una IA como hacer el descifrado basado en factorización y finalmente me dio este código en Python.

``` python
from Crypto.Util.number import long_to_bytes

# Datos del reto
n = 19464700296545504943360349643885785244500838702379172331607798107569667137646252771323642762526628612861264627432535564405081850708415593669585875599284078
e = 65537
c = 7741921862502295378343494622143578417504200290986096113213693514908295903386350106897260361327523467308148104618734262671017238427508607426528014143546193

# Factorización: n = 2 * q
p = 2
q = n // p

# Verificación
assert p * q == n  # Confirmar que la factorización es correcta

# --- Paso 2: Calcular φ(n) y la clave privada d ---
phi = (p - 1) * (q - 1)  # φ(n) = (2-1)*(q-1) = q-1
d = pow(e, -1, phi)      # d ≡ e⁻¹ mod φ(n)

# --- Paso 3: Descifrar c ---
m = pow(c, d, n)         # m ≡ cᵈ mod n
flag = long_to_bytes(m)

# --- Mostrar resultados ---
print("[+] p =", p)
print("[+] q =", q)
print("[+] φ(n) =", phi)
print("[+] d =", d)
print("\n[+] Flag (bytes):", flag)
```

``` shell
pablo☠office EVEN-RSA-CAN-BE-BROKEN$ python decrypt.py
[+] p = 2
[+] q = 9732350148272752471680174821942892622250419351189586165803899053784833568823126385661821381263314306430632313716267782202540925354207796834792937799642039
[+] φ(n) = 9732350148272752471680174821942892622250419351189586165803899053784833568823126385661821381263314306430632313716267782202540925354207796834792937799642038
[+] d = 1699006943350909578230509180124946739874682206951188722751459619365431448813729480726259951218908082150126864232842816976353368737926536209879396392354007

[+] Flag (bytes): b'picoCTF{tw0_1$_pr!m3625a858b}'
```

flag: **picoCTF{tw0_1$_pr!m3625a858b}**

El dilema de cuan seguro es la criptografía basadas en números primos pseudoaleatorios y la factoriazación de los mismos. ¿Se terminará la criptografía asimétrica basada en la factorización cuando los ordenadores cuanticos tengas muchos Qbits?. Quien sabe. 