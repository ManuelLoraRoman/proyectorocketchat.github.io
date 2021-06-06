---
title: Proxy inverso y Rocket.Chat
layout: post
post-image: https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/proxy.png
description: En este post veremos las formas que hay para configurar un proxy inverso para 
 desplegar el servidor Rocket.Chat.
tags:
- Proxy
- Connections
- Rocket.Chat
- App
- Certificate
---

Existen varias formas de implementar un proxy inverso para Rocket.Chat. Veremos algunas, de las 
más interesantes, y después veremos como implementarlo en nuestro proyecto.

* La primera forma que nos encontramos es configurar un proxy inverso con NGinx.

En primer lugar, debemos tener instalado el siguiente paquete:


	sudo apt-get install nginx

<br>

Después, añadiremos nuestra clave privada dentro del directorio */etc/nginx/certificate.key*.
Configuramos también los permisos de dicho fichero:

	chmod 400 /etc/nginx/certificate.key

<br>

Después añadimos a la misma ubicación, nuestro certificado. Editaremos el fichero
*/etc/nginx/sites-enabled/default* o si estamos usando nginx desde Docker, */etc/nginx/conf.d/default.conf*
y nos aseguramos de usar nuestro hostname en lugar de hostname de ejemplo que viene a continuación:

```
# Upstreams
upstream backend {
    server 127.0.0.1:3000;
}

# HTTPS Server
server {
    listen 443;
    server_name your_hostname.com;

    # You can increase the limit if your need to.
    client_max_body_size 200M;

    error_log /var/log/nginx/rocketchat.access.log;

    ssl on;
    ssl_certificate /etc/nginx/certificate.crt;
    ssl_certificate_key /etc/nginx/certificate.key;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # don’t use SSLv3 ref: POODLE

    location / {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;

        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header X-Nginx-Proxy true;

        proxy_redirect off;
    }
}
```
<br>

Por último, solo nos quedaría el reiniciar el servicio de NGinx.

* Otro sistema para realizar Proxy inverso es Plesk Onyx con un Nginx.

Plesk Onyx dispone actualmente de una instalación con Docker y un generador de reglas Docker para proxy
con Nginx dentro del mismo. Debemos deshabilitar dicha opción puesto que no permite añadir directorios
personalizados.

Debemos a su vez, añadir manualmente las reglas en la zona de directorios adicionales de NGinx, quedando
algo así:

```
#manual extension docker with socket upgrade begin
location ~ ^/.* {
    proxy_pass http://0.0.0.0:3000;
    proxy_set_header Host             $host;
    proxy_set_header X-Real-IP        $remote_addr;
    proxy_set_header X-Forwarded-For  $proxy_add_x_forwarded_for;

    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header X-Forwarded-Proto http;
    proxy_set_header X-Nginx-Proxy true;
    proxy_http_version 1.1;

    proxy_redirect off;
}

#extension docker end
```
<br>

* Por último, disponemos del Proxy inverso realizado con Apache

En primer lugar, debemos ejecutar los siguientes comandos como root:

```
apt-get update
apt-get install apache2
a2enmod proxy_http
a2enmod proxy
a2enmod ssl
a2enmod proxy_wstunnel
a2enmod rewrite
```
<br>

Esto instalará apache2 y activará el módulo del proxy entre otras cosas.
Después de realizar estos comandos, vamos a realizar el mismo procedimiento seguido en el Proxy inverso
con NGinx:

 * Añadimos la clave privada a la ubicación */etc/ssl/private/chat.domain.com.key*

 * 

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/user3.png" width="800" /></div>

<div>

 <span style="margin-right:980px;text-align:left;color:blue" onclick="document.location.href = 'dockercompose-post'; return false">< Instalación mediante Docker-compose</span>

 <span style="margin-left:0px;float:right;color:blue" onclick="document.location.href = 'admin-post'; return false">Opciones de administrador ></span>

</div>

