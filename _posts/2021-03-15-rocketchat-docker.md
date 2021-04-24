---
layout: post
title: Rocket.Chat implementado con Docker
date: 2021-03-15 13:31 +0800
last_modified_at: 2021-03-15 13:32:25 +0800
tags: [RocketChat, Docker, Tutorial, LDAP, Ubuntu]
toc:  true
---
Rocket.Chat es una herramienta que permite de manera segura, mantener los datos y las comunicaciones centralizadas.
{: .message }

## Recomendaciones

Antes de empezar a hablar sobre Rocket.chat, voy a explicar un poco el porqué utilizar Rocket.Chat con contenedores.

En principio, los contenedores y las máquinas virtuales difieren en varios aspectos pero el principal
es que los contenedores ofrecen una forma de virtualizar un sistema operativo haciendo que múltiples cargas de
trabajo puedan correr en una sencilla instancia de dicho sistema operativo, mientras que las máquinas virtuales
todo el hardware es virtualizado para correr en diferentes sistemas operativos. 

Esto hace que los contenedores sean más ágiles, veloces y que tengan mayor portabilidad. Además de los siguientes beneficios:

* Requieren menos tiempo para iniciarse

* Mejor distribución de recursos

* Acceso directo al hardware

* Menos redundante

Aparte son más sencillos de orquestar, con diferentes sistemas como Kubernetes, Rancher, Openshift o Docker-compose.

Otra de las recomendaciones es usar un sistema de monitorización, si es posible el estándar Prometheus + Grafana.


## Requerimientos

Los requerimientos han sido ya tratados en dicha sección en el pre-proyecto. Puedes visualizarlo aquí:

https://github.com/ManuelLoraRoman/ApuntesASIR/blob/master/Proyecto.md

Lo único a comentar sería con que voy a trabajar que sería:

* Lenovo Legion Y720

* Debian 10

* 16 GB RAM

* Intel(R) Core(TM) i7-7700HQ CPU @ 2.80GHz

* 8 procesadores con 4 cores cada uno

* 1 TB disco duro

## Instalaciones

Existen varios tipos de instalaciones para Rocket.Chat. Veremos algunos de ellos, y elegiremos cual es el más adecuado.

1. Ubuntu + Snap

{% highlight js linenos %}
sudo apt-get install snapd

sudo snap install rocketchat-server   
{% endhighlight %}

Una vez hecho esto, tendríamos desplegado en nuestro _localhost_ Rocket.chat y podremos configurarlo.

Snap actualiza de manera automática cuando hay una nueva version de Rocket.Chat y suele ser una opción más segura ya que 
tanto Rocket.Chat y sus dependencias están separadas del sistema.

2. Despliegue en PaaS

   Las recomendadas serían _SandStorm_ y _Cloudron_ pero prácticamente se puede desplegar en cualquier entorno,
   desde _Amazon Web Services_ hasta en _Digital Ocean_ entre otros.

3. Herramientas de automatización

   Es posible instalar el servidor con las siguientes herramientas:

	* [Ansible](https://docs.rocket.chat/installation/automation-tools/ansible)

	* [OpenShift](https://docs.rocket.chat/installation/automation-tools/openshift)

	* [Kubernetes + Helm](https://docs.rocket.chat/installation/automation-tools/helm-chart)

	* [Vagrant](https://docs.rocket.chat/installation/automation-tools/vagrant)

4. Instalación manual

   Prácticamente, es posible instalar el servidor de Rocket.Chat en diferentes entornos. Algunos de ellos son:

	* CentOS

	* Debian

	* RedHat

	* Ubuntu

5. Contenedores Docker

   Hemos hablado anteriormente de esta opción.

6. Métodos No-oficiales

   Los métodos anteriormente comentados, están respaldados por la Rocket.Chat. Es posible instalar el servidor
   en alguno de estos sistemas, pero no se puede asegurar que funcionen o que se actualicen las funcionalidades.
   Por comentar algunas, tenemos:

	* FreeBSD

	* Windows 10 / Windows Server

	* Kali

	* OpenSUSE


Comentados todos los métodos de instalación, vamos a desarrollar nuestro ejercicio con contenedores Docker. Se ajustan
bien a nuestro escenario y disponen de la portabilidad que buscamos.


## Documentación sobre contenedores Docker y Rocket.Chat

Rocket.Chat dispone de tres imágenes oficiales, siendo la versión _stable_ la que en principio vamos a utilizar. 

Simplemente, para bajarse la imagen, debemos ejecutar lo siguiente:

{% highlight js linenos %}

docker pull rocket.chat

{% endhighlight %}

En caso de querer ejecutar Rocket.Chat sobre _systemd_ vamos a seguir los siguientes pasos:

{% highlight js linenos %}

docker network create rocketchat_default

{% endhighlight %}

Crearemos a continuación dos servicios: el servicio de mongo y el servicio de Rocket.Chat.

* mongo.service

{% highlight js linenos %}

[Unit]
Description=mongo
Requires=docker.service
After=docker.service

[Service]
EnvironmentFile=/etc/environment
User=dockeruser
Restart=always
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill mongo
ExecStartPre=-/usr/bin/docker rm mongo
ExecStartPre=-/usr/bin/docker pull mongo:3.2


ExecStart=/usr/bin/docker run \
    --name mongo \
    -v .../path/to/data/db:/data/db \
    -v .../path/to/data/dump:/data/dump \ <--optional
    --net=rocketchat_default \
    mongo:3.2 \
    mongod --smallfiles --oplogSize 128 --replSet rs0 --storageEngine=mmapv1

ExecStop=-/usr/bin/docker kill mongo
ExecStop=-/usr/bin/docker rm mongo

{% endhighlight %}

* rocketchat.service

{% highlight js linenos %}

[Unit]
Description=rocketchat
Requires=docker.service
Requires=mongo.service
After=docker.service
After=mongo.service

[Service]
EnvironmentFile=/etc/environment
User=dockeruser
Restart=always
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill rocketchat
ExecStartPre=-/usr/bin/docker rm rocketchat
ExecStartPre=-/usr/bin/docker pull rocketchat/rocket.chat:latest

ExecStart=/usr/bin/docker run \
    --name rocketchat \
    -v .../path/to/uploads:/app/uploads \
    -e MONGO_OPLOG_URL=mongodb://mongo:27017/local \
    -e MONGO_URL=mongodb://mongo:27017/rocketchat \
    -e ROOT_URL=https://sub.domain.xx \
    --link mongo:mongo \
    --net=rocketchat_default \
    --expose 3000 \
    rocketchat/rocket.chat:latest

ExecStop=-/usr/bin/docker kill rocketchat
ExecStop=-/usr/bin/docker rm rocketchat

{% endhighlight %}

Una vez creado dichos servicios, iniciamos el servicio de mongo y creamos este contenedor Docker:

{% highlight js linenos %}

docker run \
      --name mongo-init-replica \
      --link mongo:mongo \
      --rm \
      --net=rocketchat_default \
      mongo:3.2 \
      mongo mongo/rocketchat --eval "rs.initiate({ _id: 'rs0', members: [ { _id: 0, host: 'localhost:27017' } ]})"

{% endhighlight %}

E iniciamos el servicio de Rocket.Chat. En caso de tener un _proxy inverso_, nos debemos asegurar que está añadido
en la red creada anteriormente. Para ello, debemos añadir la siguiente línea en el archivo
_rocketchat.service_:

{% highlight js linenos %}

ExecStartPre=-/usr/bin/docker network connect rocketchat_default Nginx

{% endhighlight %}

## Documentación sobre Docker-compose y Rocket.chat

Es posible, como hemos visto en clase, que se puede automatizar el proceso de creación de contenedores
mediante un fichero .yml. Podremos descargar el fichero docker-compose.yml que nos proporciona la 
plataforma de rocket.chat:

{% highlight js linenos %}

curl -L https://raw.githubusercontent.com/RocketChat/Rocket.Chat/develop/docker-compose.yml -o docker-compose.yml

{% endhighlight %}

Dicho archivo _docker-compose.yml_ tiene el siguiente contenido:

{% highlight js linenos %}

version: '2'

services:
  rocketchat:
    image: rocketchat/rocket.chat:latest
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
      - ROOT_URL=http://localhost:3000
      - MONGO_URL=mongodb://mongo:27017/rocketchat
      - MONGO_OPLOG_URL=mongodb://mongo:27017/local
      - MAIL_URL=smtp://smtp.email
#       - HTTP_PROXY=http://proxy.domain.com
#       - HTTPS_PROXY=http://proxy.domain.com
    depends_on:
      - mongo
    ports:
      - 3000:3000
    labels:
      - "traefik.backend=rocketchat"
      - "traefik.frontend.rule=Host: your.domain.tld"

  mongo:
    image: mongo:4.0
    restart: unless-stopped
    volumes:
     - ./data/db:/data/db
     #- ./data/dump:/dump
    command: mongod --smallfiles --oplogSize 128 --replSet rs0 --storageEngine=mmapv1
    labels:
      - "traefik.enable=false"

  # this container's job is just run the command to initialize the replica set.
  # it will run the command and remove himself (it will not stay running)
  mongo-init-replica:
    image: mongo:4.0
    command: >
      bash -c
        "for i in `seq 1 30`; do
          mongo mongo/rocketchat --eval \"
            rs.initiate({
              _id: 'rs0',
              members: [ { _id: 0, host: 'localhost:27017' } ]})\" &&
          s=$$? && break || s=$$?;
          echo \"Tried $$i times. Waiting 5 secs...\";
          sleep 5;
        done; (exit $$s)"
    depends_on:
      - mongo

  # hubot, the popular chatbot (add the bot user first and change the password before starting this image)
  hubot:
    image: rocketchat/hubot-rocketchat:latest
    restart: unless-stopped
    environment:
      - ROCKETCHAT_URL=rocketchat:3000
      - ROCKETCHAT_ROOM=GENERAL
      - ROCKETCHAT_USER=bot
      - ROCKETCHAT_PASSWORD=botpassword
      - BOT_NAME=bot
  # you can add more scripts as you'd like here, they need to be installable by npm
      - EXTERNAL_SCRIPTS=hubot-help,hubot-seen,hubot-links,hubot-diagnostics
    depends_on:
      - rocketchat
    labels:
      - "traefik.enable=false"
    volumes:
      - ./scripts:/home/hubot/scripts
  # this is used to expose the hubot port for notifications on the host on port 3001, e.g. for hubot-jenkins-notifier
    ports:
      - 3001:8080

  #traefik:
  #  image: traefik:latest
  #  restart: unless-stopped
  #  command: >
  #    traefik
  #     --docker
  #     --acme=true
  #     --acme.domains='your.domain.tld'
  #     --acme.email='your@email.tld'
  #     --acme.entrypoint=https
  #     --acme.storagefile=acme.json
  #     --defaultentrypoints=http
  #     --defaultentrypoints=https
  #     --entryPoints='Name:http Address::80 Redirect.EntryPoint:https'
  #     --entryPoints='Name:https Address::443 TLS.Certificates:'
  #  ports:
  #    - 80:80
  #    - 443:443
  #  volumes:
  #    - /var/run/docker.sock:/var/run/docker.sock


{% endhighlight %}

Este fichero realizará lo siguiente:

* Inicia el servicio llamado _mongo_.

* Inicia el servicio llamado _mongo-init-replica_ que esperará a que el servicio de mongo esté listo,
para conectarse a él e inicializarlo.

* Por último, inicia el servicio de Rocket.Chat, que al igual que el anterior servicio, esperará a que
_mongo_ esté listo.

Para iniciar dicho docker-compose con la instrucción _docker-compose up -d_ y ya tendríamos corriendo
nuestra aplicación Rocket.chat.

En caso de querer manejar logs y demás, podemos descomentar las líneas del montaje de volúmenes.

Es posible intalar Rocket.Chat en alta disponibilidad con un replicaset de mongodb como backend, pero
esta es una opción a tener en cuenta más adelante.

## Conexión usuario

El usuario de la aplicación puede conectarse a través de 3 clientes diferentes:

1. Mediante navegador web

   Para conectarse a un servidor de Rocket.Chat, buscar la dirección donde se aloje dicha aplicación, 
   introducir sus credenciales y seguidamente, ya estaremos como miembros del servidor.

2. Mediante la aplicación de escritorio

   Mismo funcionamiento que el anterior, salvo que se realizará todo desde la aplicación descargada
   [aquí](https://rocket.chat/install).

   Mediante la aplicación de escritorio, uno se puede conectar a varios servidores. Simplemente, pulsar
   el icono _+_ para añadir un servidor nuevo.

3. Mediante la app móvil

## Conexión y opciones de administrador

Rocket.Chat permite administrar la aplicación, cambiado las diferentes opciones que esta dispone.
Ahora iré comentando una a una, todas las características que tiene y parándome a desarrollar las
más interesantes:

* Permitir el uso de certificados autofirmados e inválidos.

Solo para validar mediante link y para previsualizaciones.

* Permitir el uso de Bugsnag API Key

(Detección y corrección de Bugs)

* Prefijos CDN para JS/CSS u otros

Se puede configurar un prefijo CDN de donde cargará el contenido de JS/CSS

* Habilitar salas favoritas

Permitirá a los usuarios el que puedan colocar en favoritos las diferentes salas públicas o privadas.

* Canal por defecto

Una vez un usuario se conecte al servidor, este entrará en dicho canal de manera predeterminada. Si el
campo está vacío, o tiene un canal inválido, el usuario será dirigido a la página principal del servidor.

* Forzar deshabilitación de OpLog para Caché

No se usará OpLog para sincronizar la caché incluso si está habilitada dicha opción.

* Forzar SSL

Esta opción no la usaremos, porque para eso tenemos el proxy inverso que redireccionará a https.

* Uso de Google Tag Manager ID

* Lenguaje

* Reinicio

* Contenido del fichero Robots.txt

Permite cambiar el contenido de dicho fichero, para ayudar a indexar de manera correcta el servidor en 
diferentes motores de búsqueda.

* Mostrar Opciones Wizard

Se recomienda no tocar esta opción ya que puede hacer que se bloquee un servidor.

* Nombre del sitio

* URL del sitio

* Guardar último mensaje de cada sala

Esto permitirá ver dichos mensajes en la barra lateral.

* Contador de mensajes no leídos

* Contador de mensjaes no leídos para mensajes directos

* Habilitar notificaciones

Y otras tantas características que tratan de la API RESTful u integración de Iframe.


## HUB

## Bibliografía

* www.blackblaze.com/blog/vm-vs-containers/

------
