---
title: Instalación de Rocket.Chat via systemd
layout: post
post-image: "/assets/images/documetation-image.jpg"
description: En este post se tratará de ver una de las maneras de instalación de
  nuestro proyecto con Rocket.Chat. 
tags:
- Documentation
- post
- general
---

Rocket.Chat dispone de tres imágenes oficiales, siendo la versión _stable_ la que en principio 
vamos a utilizar. 

Para bajarse alguna de las imagénes, vamos a ejecutar lo siguiente:

```
docker pull rocket.chat
```
<br>
Demo de la descarga:

<img src="/assets/images/descarga-imagen.gif" width="480" height="259" frameBorder="0"/>

Una vez bajada dicha imagen, en caso de querer ejecutar Rocket.Chat sobre _systemd_ vamos a seguir los
siguientes pasos:

* Primero creamos una red en Docker:

```
docker network create rocketchat_default
```

* Después, creamos dos servicios: el primero será el servicio para Mongodb y el otro, el servicio de
Rocket.Chat.

	* mongo.service

	```
	[Unit]
	Description=mongo
	Requires=docker.service
	After=docker.service
	
	[Service]
	EnvironmentFile=/etc/mongo				<-- ficheros del servicio
	User=manuel
	Restart=always
	TimeoutStartSec=0
	ExecStartPre=-/usr/bin/docker kill mongo
	ExecStartPre=-/usr/bin/docker rm mongo
	ExecStartPre=-/usr/bin/docker pull mongo:3.2
	
	
	ExecStart=/usr/bin/docker run \
	    --name mongo \
	    -v /media/manuel/Datos1/PROYECTO/mongo:/data/db \		<-- ruta del volumen para Mongodb
	    --net=rocketchat_default \
	    mongo:3.2 \
	    mongod --smallfiles --oplogSize 128 --replSet rs0 --storageEngine=mmapv1
	
	ExecStop=-/usr/bin/docker kill mongo
	ExecStop=-/usr/bin/docker rm mongo
	```
<br>

	* rocketchat.service

	```
	[Unit]
	Description=rocketchat
	Requires=docker.service
	Requires=mongo.service
	After=docker.service
	After=mongo.service
	
	[Service]
	EnvironmentFile=/etc/rocketchat				<-- ficheros del servicio
	User=manuel						
	Restart=always
	TimeoutStartSec=0
	ExecStartPre=-/usr/bin/docker kill rocketchat
	ExecStartPre=-/usr/bin/docker rm rocketchat
	ExecStartPre=-/usr/bin/docker pull rocketchat/rocket.chat:latest
	
	ExecStart=/usr/bin/docker run \
	    --name rocketchat \
	    -v /media/manuel/Datos1/PROYECTO/rocketchat:/app/uploads \	<-- donde subir ficheros
	    -e MONGO_OPLOG_URL=mongodb://mongo:27017/local \
	    -e MONGO_URL=mongodb://mongo:27017/rocketchat \
	    -e ROOT_URL=https://sub.domain.xx \
	    --link mongo:mongo \
	    --net=rocketchat_default \
	    --expose 3000 \
	    rocketchat/rocket.chat:latest
	
	ExecStop=-/usr/bin/docker kill rocketchat
	ExecStop=-/usr/bin/docker rm rocketchat
	```
<br>
* Creados ambos servicios, vamos a iniciar el de Mongodb y creamos el siguiente contenedor Docker:

```
docker run \
      --name mongo-init-replica \
      --link mongo:mongo \
      --rm \
      --net=rocketchat_default \
      mongo:3.2 \
      mongo mongo/rocketchat --eval "rs.initiate({ _id: 'rs0', members: [ { _id: 0, host: 'localhost:27017' } ]})"
```
<br>
<img src="/assets/images/contenedor-mongo.gif" width="480" height="259" frameBorder="0"/>
<br>

* Por último, vamos a iniciar el servicio de Rocket.Chat creado anteriormente:

```
sudo systemctl start rocketchat.service
```
<br>

Con esto, ya tendriamos nuestro servicio Rocket.Chat funcionando en una máquina Docker, pero funcionando
desde systemd.

Como última aclaración, en caso de tener un proxy inverso, nos debemos asegurar que está añadido
en la red creada anteriormente. Para ello, debemos añadir la siguiente línea en el archivo
_rocketchat.service_:


```
ExecStartPre=-/usr/bin/docker network connect rocketchat_default Nginx
```

Ahora pasaremos a otro tipo de instalación.

<div>

 <span style="margin-right:980px;text-align:left;color:blue" onclick="document.location.href = 'introduction-post'; return false">< Introducción</span>

 <span style="margin-left:0px;float:right;color:blue" onclick="document.location.href = 'dockercompose-post'; return false">Instalación Docker-compose ></span>

</div>
