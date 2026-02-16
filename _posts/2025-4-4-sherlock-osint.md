---
title: Sherlock OSINT
date: 2025-04-4 13:00:00 +0200
categories: [hacking, osint]
tags: [osint, sherlock]     
image:
    path: https://github.com/sherlock-project/sherlock/raw/master/docs/images/sherlock-logo.png
    alt: Sherlock
---

## Sherlock
Sherlock es una herramienta de OSINT (Open Source Intelligence) [Hacktools/#osint](../hacktools/#osint)
Básicamente dice ser capaz de buscar un usuario en más de 400 redes sociales. 
**Importante** Antes de acusar a nadie de tener una cuenta en tal sitio o tal otro.
**¡Puede haber falsos positivos!!!** Esto ocurre porque muchos usuarios conciden en nombre y se ven obligados a cambiarlo 
y/o porque hay sitios que responden a estas peticiones de forma positiva sin serlo realmente. 
Un nickname/alias/nombredeusuario no es lo mismo que un correo electrónico. Y aún así hay sitios que dejan registrada tu 
cuenta sin haber sido verificada. 

## Instalar Sherlock en entorno virtual Python (venv)
``` shell
git clone https://github.com/sherlock-project/sherlock
cd sherlock 
# activamos el entorno Python con el módulo de Python (venv)
python -m venv .
source bin/activate
# instalamos las dependencias
pip install sherlock_project
```

## Usando Sherlock
``` shell
(sherlock) pablo☠office sherlock$ python sherlock_project/sherlock.py JMilei
[*] Checking username JMilei on:

[+] Academia.edu: https://independent.academia.edu/JMilei
[+] Blogger: https://JMilei.blogspot.com
[+] Discord: https://discord.com
[+] Duolingo: https://www.duolingo.com/profile/JMilei
[+] Freelance.habr: https://freelance.habr.com/freelancers/JMilei
[+] GNOME VCS: https://gitlab.gnome.org/JMilei
[+] Giphy: https://giphy.com/JMilei
[+] GitHub: https://www.github.com/JMilei
[+] HudsonRock: https://cavalier.hudsonrock.com/api/json/v2/osint-tools/search-by-username?username=JMilei
[+] Lichess: https://lichess.org/@/JMilei
[+] Linktree: https://linktr.ee/JMilei
[+] Periscope: https://www.periscope.tv/JMilei/
[+] Reddit: https://www.reddit.com/user/JMilei
[+] Roblox: https://www.roblox.com/user.aspx?username=JMilei
[+] Rumble: https://rumble.com/user/JMilei
[+] RuneScape: https://apps.runescape.com/runemetrics/app/overview/player/JMilei
[+] Scribd: https://www.scribd.com/JMilei
[+] Smule: https://www.smule.com/JMilei
[+] Steam Community (User): https://steamcommunity.com/id/JMilei/
[+] Strava: https://www.strava.com/athletes/JMilei
[+] Telegram: https://t.me/JMilei
[+] TradingView: https://www.tradingview.com/u/JMilei/
[+] Trello: https://trello.com/JMilei
[+] Twitter: https://x.com/JMilei
[+] Xbox Gamertag: https://xboxgamertag.com/search/JMilei
[+] YandexMusic: https://music.yandex/users/JMilei/playlists
[+] YouTube: https://www.youtube.com/@JMilei

[*] Search completed with 27 results

```
Como podemos ver en los resultados de JMilei, hemos obtenido 27 resultados donde claramente el usuario que el 
actual presidente de Argentina usa en X(antes twitter) JMilei está siendo utilizado en varios usuarios de distintas webs y no creo que 
el señor Milei tenga una cuenta de Steam, ni de Snapchat, ni de Roblox, bueno en esto último no estaría tan seguro. 

## Conclusiones en base a resultados

### PROS:
- Supongo que Sherlock es una manera rápida de buscar a un usuario si este utiliza el mismo alias en varias redes sociales.
- Puede ser una buena herramienta para identificar y estudiar las actividades en la red, siempre que nos aseguremos de que se trata del 
usuario exacto.

### CONTRAS:
- También supongo es una herramienta inútil contra los que no usan el mismo nick en todo el sitio en el que se registran.
- Es una intrusión en la vida de las personas. Si bien toda esta información viene de fuentes abiertas, pues todo el mundo
puede acceder a ellas, no todo el mundo es una persona pública. Normalmente el que recaba datos sobre un tercero 
sin que este último sea consciente de ello no suele ser para algo bueno.
- Los falsos positivos, tienes que contar con ellos. No parece que esta herramienta elabore una conexión entre un perfil 
de una red social y otras redes, sino más bien interroga distintas urls identificando en cada una si existe una url
o elemento de html, etc que identifica al usuario.

Mi veredicto final es que tiene potencial, pero necesita mejorar. 

La imagen de este post pertenece al proyecto de Sherlock. 



