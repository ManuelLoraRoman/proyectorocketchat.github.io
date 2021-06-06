---
title: Introducción a Rocket.Chat
layout: post
post-image: "https://github.com/ManuelLoraRoman/proyectorocketchat.github.io/tree/main/assets/images/1075x720-Introduction-Placeholder.png"
description: Rocket.Chat es una herramienta que permite de manera segura, 
 mantener los datos y las comunicaciones centralizadas.
tags:
- Introduction
- Rocket.Chat
- Communication
---

Antes de empezar a hablar sobre Rocket.chat, voy a explicar un poco el porqué utilizar Rocket.Chat 
con contenedores.

En principio, los contenedores y las máquinas virtuales difieren en varios aspectos pero el principal
es que los contenedores ofrecen una forma de virtualizar un sistema operativo haciendo que múltiples 
cargas de trabajo puedan correr en una sencilla instancia de dicho sistema operativo, mientras que 
las máquinas virtuales todo el hardware es virtualizado para correr en diferentes sistemas operativos.

Esto hace que los contenedores sean más ágiles, veloces y que tengan mayor portabilidad. Además de los 
siguientes beneficios:

* Requieren menos tiempo para iniciarse

* Mejor distribución de recursos

* Acceso directo al hardware

* Menos redundante

Aparte son más sencillos de orquestar, con diferentes sistemas como Kubernetes, Rancher, 
Openshift o Docker-compose.

Otra de las recomendaciones es usar un sistema de monitorización, si es posible el estándar 
Prometheus + Grafana.

## Requerimientos

Los requerimientos han sido ya tratados en dicha sección en el pre-proyecto. Puedes visualizarlo 
[aquí](https://github.com/ManuelLoraRoman/ApuntesASIR/blob/master/Proyecto.md).

Lo único a comentar sería con que voy a trabajar que sería:

* Lenovo Legion Y720

* Debian 10

* 16 GB RAM

* Intel(R) Core(TM) i7-7700HQ CPU @ 2.80GHz

* 8 procesadores con 4 cores cada uno

* 1 TB disco duro

## Instalaciones

Aunque hablaremos más adelante de los tipos de instalaciones que hay, vamos a
exponer de manera breve los diferentes tipos que hay:

* **Ubuntu + Snap**

```
sudo apt-get install snapd

sudo snap install rocketchat-server
```
<br>
Una vez hecho esto, tendríamos desplegado en nuestro _localhost_ Rocket.chat y podremos configurarlo.

Snap actualiza de manera automática cuando hay una nueva version de Rocket.Chat y suele ser una opción más segura ya que 
tanto Rocket.Chat y sus dependencias están separadas del sistema.

* **Despliegue en PaaS**

Las recomendadas serían _SandStorm_ y _Cloudron_ pero prácticamente se puede desplegar en cualquier entorno,
desde _Amazon Web Services_ hasta en _Digital Ocean_ entre otros.

* **Herramientas de automatización**

    * [Ansible](https://docs.rocket.chat/installation/automation-tools/ansible)

    * [OpenShift](https://docs.rocket.chat/installation/automation-tools/openshift)

    * [Kubernetes + Helm](https://docs.rocket.chat/installation/automation-tools/helm-chart)

    * [Vagrant](https://docs.rocket.chat/installation/automation-tools/vagrant)

<br>
* **Instalación manual**

    * CentOS

    * Debian

    * RedHat

    * Ubuntu

Prácticamente, es posible instalar el servidor de Rocket.Chat en diferentes entornos.

* **Contenedores Docker**

Hemos hablado anteriormente de esta opción y además, vamos a explicar detalladamente esta manera
de instalación más adelante.

* **Métodos no oficiales**

    * FreeBSD

    * Windows 10 / Windows Server

    * Kali

    * OpenSUSE

Los métodos anteriormente comentados en los otros puntos, están respaldados por Rocket.Chat. 

Pero es posible instalar el servidor en alguno de estos sistemas, pero no se puede asegurar que funcionen o que se actualicen las funcionalidades.

<br>


Comentados todos los métodos de instalación, vamos a desarrollar nuestro ejercicio con contenedores Docker. Se ajustan
bien a nuestro escenario y disponen de la portabilidad que buscamos.

Ahora pasaremos al siguiente punto, que es la documentación.

<div align="right">

 <span style="color:blue" onclick="document.location.href = 'systemd-post'; return false">Instalación via systemd ></span>

</div>

