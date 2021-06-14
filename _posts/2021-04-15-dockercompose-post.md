---
title: Instalación mediante Docker-Compose
layout: post
post-image: "https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/dockercompose-image.jpg"
description: Este post tratará la instalación de Rocket.Chat sobre Docker
  con la utilidad de dockercompose.
tags:
- Docker
- dockercompose
- Rocket.Chat
- Installation
---

Es posible, como hemos visto en clase, que se puede automatizar el proceso de creación de 
contenedores mediante un fichero _.yml_. Por lo tanto, vamos a crear ese fichero con el siguiente
contenido:

```
version: '2'

services:
  rocketchat:
    image: registry.rocket.chat/rocketchat/rocket.chat:latest
    command: >
      bash -c
        "for i in `seq 1 30`; do
          node main.js &&
          s=$$? && break || s=$$?;
          echo \"Tried $$i times. Waiting 5 secs...\";
          sleep 5;
        done; (exit $$s)"
    restart: unless-stopped
    volumes:
      - ./uploads:/app/uploads
    environment:
      - PORT=3000
      - ROOT_URL=http://rocketchat:3000
      - MONGO_URL=mongodb://mongo:27017/rocketchat
      - MONGO_OPLOG_URL=mongodb://mongo:27017/local
      - MAIL_URL=smtp://smtp.email
    depends_on:
      - mongo
    ports:
      - 3000:3000

  mongo:
    image: mongo:4.0
    restart: unless-stopped
    volumes:
     - ./data/db:/data/db
     #- ./data/dump:/dump
    command: mongod --smallfiles --oplogSize 128 --replSet rs0 

  mongo-init-replica:
    image: mongo:4.0
    command: >
      bash -c
        "for i in `seq 1 30`; do
          mongo mongo/rocketchat --eval \"
            rs.initiate({
              _id: 'rs0',
              members: [ { _id: 0, host: 'mongo:27017' } ]})\" &&
          s=$$? && break || s=$$?;
          echo \"Tried $$i times. Waiting 5 secs...\";
          sleep 5;
        done; (exit $$s)"
    depends_on:
      - mongo

#  mongo-init-replica:
#    image: mongo:4.0
#    command: 'mongo mongo/rocketchat --eval "rs.initiate({ _id: ''rs0'', members: [ { _id: 0, host: ''localhost:27017'' } ]})"'
#    depends_on:
#      - mongo


# hubot, the popular chatbot (add the bot user first and change the password before starting this image)
  hubot:
    image: rocketchat/hubot-rocketchat:v0.1.4
    environment:
      - ROCKETCHAT_URL=rocketchat:3000
      - ROCKETCHAT_ROOM=
      - ROCKETCHAT_USER=rocket.cat
      - ROCKETCHAT_PASSWORD=password
      - LISTEN_ON_ALL_PUBLIC=true
      - BOT_NAME=bot
      - RESPOND_TO_EDITED=true
      - RESPOND_TO_DM=true
# you can add more scripts as you'd like here, they need to be installable by npm
      - EXTERNAL_SCRIPTS=hubot-help,hubot-seen,hubot-links,hubot-diagnostics
# this is used to expose the hubot port for notifications on the host on port 3001, e.g. for hubot-jenkins-notifier
    ports:
      - 3001:8080

  smtp:
    image: namshi/smtp
    environment:
      - RELAY_DOMAINS=:example.com:example.de

  nginx:
    image: nginx:latest
    volumes:
      - ./certs/host.example.com.crt:/etc/nginx/cert.crt
      - ./certs/host.example.com.key:/etc/nginx/cert.key
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    ports:
      - 8066:443

```
<br>
Este fichero dockercompose realizará lo siguiente:

* De primeras, inicia el servicio de _mongo_.

* Después inicia otro servicio llamado _mongo-init-replica_, que esperará a que el servicio de mongo
esté listo, para conectarse a él e inicializarlo.

* Por último, inicia el servicio de Rocket.Chat, que al igual que el anterior servicio, esperará
a que _mongo_ esté listo.

* También iniciará el servicio de smtp y el servicio de nginx que funcionará como proxy inverso.

Para iniciar el escenario que hemos montado, vamos a ejecutar el siguiente comando:

```
docker-compose up -d
```
<br>
<div align="center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/despliegue-compose.gif"/></div>
<br>
Podemos ver las características del despliegue:
<br>

<div align="center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/docker-ps.gif"/></div>

<br>

Y ya tendríamos nuestra aplicación de Rocket.Chat funcionando.
<br>

En caso de querer manejar logs y demás, podemos descomentar las líneas del montaje de volúmenes.

También es posible instalar Rocket.Chat en alta disponibilidad con un replicaset de *Mongodb* 
como backend, pero esta es una opción a tener en cuenta más adelante.

Una vez ya desplegado, veremos la IP de acceso de la máquina deploy_rocketchat_1 (en mi caso, 
172.18.0.3) y accederemos mediante el navegador:

<br>

<div align="center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/acceso-docker.gif"/></div>

<br>

Y una vez en la página, podemos iniciar sesión de manera normal:

<br>

<div align="center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/inicio-sesion.gif"/></div>

<br>

Todo este funcionamiento es si no hemos añadido el proxy inverso a nuestro docker-compose, pero en caso 
de haberlo añadido, accederemos a la página mediante el server_name de nginx y el puerto correspondiente.

A continuación, vamos a pasar al apartado de conexión de usuario.

<div>

 <span style="margin-right:980px;text-align:left;color:blue" onclick="document.location.href = 'systemd-post'; return false">< Instalación mediante systemd</span>

 <span style="margin-left:0px;float:right;color:blue" onclick="document.location.href = 'user-post'; return false">Conexiones y usuarios ></span>

</div>

