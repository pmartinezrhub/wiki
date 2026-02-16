---
title: crackme-py
date: 2025-05-16 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, crackme-py, ingenieria reversa, hashcat]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---

>crackme-py
Author: syreal
Description
crackme.py
{: .prompt-tip }


``` python
# Hiding this really important number in an obscure piece of code is brilliant!
# AND it's encrypted!
# We want our biggest client to know his information is safe with us.
bezos_cc_secret = "A:4@r%uL`M-^M0c0AbcM-MFE0d_a3hgc3N"

# Reference alphabet
alphabet = "!\"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ"+ \
            "[\\]^_`abcdefghijklmnopqrstuvwxyz{|}~"

def decode_secret(secret):
    """ROT47 decode

    NOTE: encode and decode are the same operation in the ROT cipher family.
    """

    # Encryption key
    rotate_const = 47

    # Storage for decoded secret
    decoded = ""

    # decode loop
    for c in secret:
        index = alphabet.find(c)
        original_index = (index + rotate_const) % len(alphabet)
        decoded = decoded + alphabet[original_index]

    print(decoded)

def choose_greatest():
    """Echo the largest of the two numbers given by the user to the program

    Warning: this function was written quickly and needs proper error handling
    """

    user_value_1 = input("What's your first number? ")
    user_value_2 = input("What's your second number? ")
    greatest_value = user_value_1 # need a value to return if 1 & 2 are equal

    if user_value_1 > user_value_2:
        greatest_value = user_value_1
    elif user_value_1 < user_value_2:
        greatest_value = user_value_2

    print( "The number with largest positive magnitude is "
        + str(greatest_value) )

choose_greatest()

```

Si nos fijamos en el código hay dos cosas que me llamaron la atención, la primera es el codigo secreto de bezos y que en ningun momento se llama a la función decode_secret
```
bezos_cc_secret = "A:4@r%uL`M-^M0c0AbcM-MFE0d_a3hgc3N"
def decode_secret(secret):
```

simplemente edito el fichero e llamo a función __decode_secret__, pasándole la variable __bezos_cc_secret__  justo antes de la llamada a choose_greatest

``` python
decode_secret(bezos_cc_secret)
choose_greatest()
```
Ahora lo lanzo y miro que ocurre

``` shell
pablo☠office crackme-py$ python crackme.py
picoCTF{1|\/|_4_p34|\|ut_502b984b}
What's your first number? 
```

flag: picoCTF{1|\/|_4_p34|\|ut_502b984b}

(bueno la que sale en la shell, porque el encoding no permite usar algunos carácteres que vienen en esta flag)