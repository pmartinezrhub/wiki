---
title: Hashcat + CUDA
date: 2025-11-19 11:00:00 +0200
categories: [ciberseguridad, software]
tags: [hashcat, cuda, cracking]     
image:
    path: https://upload.wikimedia.org/wikipedia/commons/thumb/b/b9/Nvidia_CUDA_Logo.jpg/375px-Nvidia_CUDA_Logo.jpg
    alt:  
---
## Hashcat 
Hay otras herramientas de cracking, John, rcracki-mt, etc. pero Hashcat parece la más completa y eficiente 
pues puedes usarlo en conjunto con CUDA.
En este artículo encontrarás esta y otras herramientas [hacktools/#password-cracking](../hacktools/#password-cracking)

## CUDA
CUDA es una plataforma de programación crada por NVIDIA que permite usar
GPUs para paralelizar procesos de cálculo. En concreto para el cracking de contraseñas permite procesar los hashes 
en mucha cantidad, lo que le da una ventaja frente a las CPUs. 

### Instalación 
En Debian necesitas instalar los drivers de nvidia y las herramientas de CUDA.

```shell
apt install nvidia-driver
apt install nvidia-cuda-toolkit
```

### Test de instalación - benchmarking
Este comando te mostrará cuantos Hashes por segundo (H/s) o Megahashes (Mh/s) puede generar tu sistema
eso sí, genera una entrada por cada algoritmo MD4, MD5, SHA1,SHA2-256, etc.

``` shell
┌──(pablo☠office)
└─$ hashcat --benchmark
hashcat (v7.1.2-345-g49d61d2f3) starting in benchmark mode

Benchmarking uses hand-optimized kernel code by default.
You can use it in your cracking session by setting the -O option.
Note: Using optimized kernel code limits the maximum supported password length.
To disable the optimized kernel code in benchmark mode, use the -w option.

CUDA API (CUDA 12.4)
====================
* Device #01: NVIDIA GeForce GTX 1050 Ti, 3504/4031 MB, 6MCU

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #02: cpu-bdver1-AMD FX(tm)-4300 Quad-Core Processor, 6954/13909 MB (2048 MB allocatable), 4MCU

Benchmark relevant options:
===========================
* --backend-devices-virtmulti=1
* --backend-devices-virthost=1
* --optimized-kernel-enable

---------------------
* Hash-Mode 900 (MD4)
---------------------

Speed.#01........: 14123.7 MH/s (94.91ms) @ Accel:288 Loops:1024 Thr:768 Vec:8
Speed.#02........:   233.0 MH/s (15.50ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8
Speed.#*.........: 14356.7 MH/s

-------------------
* Hash-Mode 0 (MD5)
-------------------

Speed.#01........:  7733.8 MH/s (90.43ms) @ Accel:128 Loops:1024 Thr:896 Vec:8
Speed.#02........:   188.4 MH/s (21.62ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8
Speed.#*.........:  7922.2 MH/s

----------------------

```

A partir de este momento puedes indicar a hashcat para que solo utilize la GPU 
 
  ``` shell
 hashcat -m 3200 -d 1 hashes ~/pentest/wordlist/SecLists/Passwords/Common-Credentials/Pwdb_top-10000000.txt
 ```

Obviamente la clave en el crackeo de contraseñas reside en la generación de buenos diccionarios. 

## Algoritmos
Aquí la lista de Hashes que Hashcat es capaz de descifrar
[https://hashcat.net/wiki/doku.php?id=example_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)


> La mejor contraseña, es aquella imposible de adivinar
{: .prompt-tip }