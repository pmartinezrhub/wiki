---
title: vault-door-training
date: 2025-03-30 17:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, ingenieriareversa]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>vault-door-training
Author: Mark E. Haase
Description
Your mission is to enter Dr. Evil's laboratory and retrieve the blueprints for his Doomsday Project. The laboratory is protected by a series of locked vault doors. Each door is controlled by a computer and requires a password to open. Unfortunately, our undercover agents have not been able to obtain the secret passwords for the vault doors, but one of our junior agents obtained the source code for each vault's computer! You will need to read the source code for each level to figure out what the password is for that vault door. As a warmup, we have created a replica vault in our training facility. The source code for the training vault is here: [VaultDoorTraining.java](https://jupiter.challenges.picoctf.org/static/03c960ddcc761e6f7d1722d8e6212db3/VaultDoorTraining.java)
{: .prompt-tip }

Descargamos el archivo java y vemos el contenido 
``` java
import java.util.*;

class VaultDoorTraining {
    public static void main(String args[]) {
        VaultDoorTraining vaultDoor = new VaultDoorTraining();
        Scanner scanner = new Scanner(System.in); 
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
	}
   }

    // The password is below. Is it safe to put the password in the source code?
    // What if somebody stole our source code? Then they would know what our
    // password is. Hmm... I will think of some ways to improve the security
    // on the other doors.
    //
    // -Minion #9567
    public boolean checkPassword(String password) {
        return password.equals("w4rm1ng_Up_w1tH_jAv4_3808d338b46");
    }
}
```
Ya puede distinguir lo que podría componer la flag "picoCTF{" , "w4rm1ng_Up_w1tH_jAv4_3808d338b46" y cerramos con "}"
el tema del hardcodeo de claves siempre ha estado presente en el mundo de los chapuzas de la programación. 
Pero para corroborar que es lo que hace el programa lo copilamos y lo lanzamos:

```
pablo☠office vault-door-training$ javac VaultDoorTraining.java 
pablo☠office vault-door-training$ java VaultDoorTraining
Enter vault password: picoCTF{w4rm1ng_Up_w1tH_jAv4_3808d338b46}
Access granted.
```

flag: **picoCTF{w4rm1ng_Up_w1tH_jAv4_3808d338b46}**