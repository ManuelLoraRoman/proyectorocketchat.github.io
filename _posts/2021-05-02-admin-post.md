---
title: Opciones de Administrador
layout: post
post-image: https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin.jpg
description: En este post veremos las diferentes opciones de las que disponen los administradores
  para permitir o no, diferentes configuraciones.
tags:
- Administration
- Rocket.Chat
- Options
---

Rocket.Chat permite administrar la aplicación, cambiando las diferentes opciones de las que esta dispone.
Ahora iremos comentando una por una, las características que tiene y parándonos en las más interesantes:

Esta información se halla una vez desplegada la aplicación en el botón superior izquierdo de nuestro
perfil:

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/ldap1.png" width="600" /></div>

Y se nos presentará la siguiente pestaña:

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin1.png" width="300" /></div>

En ella nos encontramos las siguientes configuraciones:

* En la primera pestaña nos encontramos **Información**.

En esta pestaña podemos encontrar datos del despliegue de Rocket.Chat, así como de los usuarios existentes
u de las salas que están creadas en el servidor.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin2.png" width="1000" /></div>

* Después, disponemos de la pestaña **Importación** en la cual, podemos importar datos de otras 
aplicaciones como Slack, CSV o Hipchat.

* La siguiente pestaña es la de usuarios, en la cual tendremos la lista de usuarios incorporados en
Rocket.Chat. Esta lista se irá rellenando dependiendo de si los usuarios entran al servidor mediante
autenticación LDAP u por correo, etc.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin3.png" width="1000" /></div>

* A continuación, nos encontramos con la pestaña de **Salas**.

Desde aquí podemos administrar las diferentes salas y poder filtrarlas para modificar su nombre, la
descripción, el tema, etc.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin4.png" width="800" /></div>

* Otra pestaña es la de **Invites**.

Esta pestaña nos muestra todas los enlaces generados para añadir a un usuario a un espacio de trabajo
u sala. En resumidas cuentas, todas las invitaciones generadas.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin5.png" width="1000" /></div>

* La siguiente pestaña que nos encontraremos es la de **Servicios de conectividad**.

	Con Rocket.Chat nos podemos conectar a uno de sus servicios llamados Rocket.Chat Cloud, el cual nos
	permite acceder a estas características:

  * Entrada de notificaciones Push de Rocket.Chat

  * Marketplace de Rocket.Chat

  * Omnichannel Proxy

  * Sincronización automática de licencia

Para usar este y otros servicios de Rocket.Chat, el servidor debe estar conectado a *cloud.rocket.chat*.
Puedes seguir los siguientes pasos para conectarte [aquí](https://docs.rocket.chat/guides/administrator-guides/administration/connectivity-services).

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin6.png" width="500" /></div>

* La siguiente pestaña es **Ver Registros**

En esta sección se puede observar un pequeño log de lo que está pasando en el servidor

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin7.png" width="800" /></div>

* Otra de las secciones trata sobre **Sonidos personalizados**

Aquí, podemos añadir diferentes ficheros de audio en formato MP3 para su posterior uso como sonido de
notificaciones. Estas opciones se pueden cambiar en *Preferencias*.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin8.png" width="800" /></div>

* La siguiente sección se llama **Federation DashBoard** y esta se encuentra todavía bajo construcción.

* Nos remitimos ahora a la sección **Aplicaciones**

Esta sección también se encuentra en construcción, pero dispone de algo más de información.

Son scripts o paquetes dinámicos que permiten personalizar su instancia de Rocket.Chat 
sin tener que bifurcar el código base. Si desea obtener más información, puedes consultar
[aquí](https://go.rocket.chat/i/developing-an-app).

* Otra de las pestañas es **Marketplace** que solo funcionará si tenemos la opción anteriormente comentada.

* La sección **Bandejas de entrada de correo electrónico** nos permite introducir direcciones de correo
para poder visualizar los diferentes correos que llegan a dicha dirección.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin9.png" width="800" /></div>

Esta sección es una de las pocas secciones que está en versión *Alpha*.

* La sección **Emoji personalizado**, permite subir una imagen y añadirla al servidor como emoji para
uso público.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin10.png" width="1000" /></div>

* Rocket.Chat también dispone de una sección para **Integraciones**.

Esta sección nos permite integrar algunos servicios externos en forma de Script, para que sea el
servicio quien controle parte del servidor (Incoming) o que sea el servidor quien se encargue de
ejecutar dicho script cuando sea necesario (Outgoing).

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin11.png" width="800" /></div>

Alguna de las integraciones pueden *Jira*, *Gitlab*, *Github*, etc.

* La siguiente sección trata sobre el uso de **Aplicaciones OAuth**.

Permite añadir diferentes aplicaciones que redigiran a los usuarios para que se autentiquen.
De manera predeterminada, solo disponemos de una llamada *Zapier*, pero podemos añadir más aquí:

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin12.png" width="800" /></div>

* La sección de **Remitente** permite enviar correos electrónicos.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin13.png" width="800" /></div>

* Otra de las secciones es **Estado de usuario personalizado**.

En ella, podemos cambiar nuestro estado de perfil en vez de usar la predeterminada.

* Por último, disponemos de la sección llamada **Permisos**.

Desde aquí, podemos gestionar las diferentes acciones y configuraciones que pueden tomar los diferentes 
roles en nuestro servidor Rocket.Chat.

<div style="text-align: center"><img src="https://raw.githubusercontent.com/ManuelLoraRoman/proyectorocketchat.github.io/main/assets/images/admin14.png" width="800" /></div>

Además de estas secciones, dentro de la pestaña de *Configuración*, nos encontramos con la sección
**General** teniendo esta las siguientes secciones:

* Permitir el **uso de certificados autofirmados e inválidos**.
    
Esto permitirá a Rocket.Chat validar mediante link y solo para previsualizaciones.

* Permitir el uso de **Bugsnag API Key**.

Permite la detección y corrección de Bugs

* **Prefijos CDN** para JS/CSS u otros.

Se puede configurar un prefijo CDN de donde cargará el contenido de JS/CSS.

* Habilitar **salas favoritas**.

Permite a los usuarios el que puedan colocar en favoritos las diferentes salas públicas/ privadas.

* **Canal por defecto**.

Una vez el usuario se conecte al servidor, este entrará en dicho canal de manera predeterminada.
Si el campo está vacío, o tiene un canal inválido, el usuario será dirigido a la página
principal del servidor.

* Forzar la deshabilitación de **OpLog para Caché**.

No se usará OpLog para sincronizar la caché incluso si está habilitada dicha opción.

* **Forzar SSL**.

Esta opción no la usaremos, porque para eso tenemos el proxy inverso que redireccionará a https.

* Uso de **Google Tag Manager ID**.

* **Lenguaje**

* **Reinicio**

* Contenido del fichero **_Robots.txt_**

Permite cambiar el contenido de dicho fichero, para ayudar a indexar de manera correcta el 
servidor en diferentes motores de búsqueda.

* Mostrar **Opciones Wizard**.

Se recomienda no tocar esta opción ya que puede hacer que se bloquee el servidor.

* **Nombre del sitio**

* **URL del sitio**

* Guardar **último mensaje** de cada sala.

Esto permitirá ver dichos mensajes en la barra lateral.

* Contador de **mensajes no leídos**.

* Contador de **mensajes no leídos para mensajes directos**.

* Habilitar **notificaciones**.

Y otras tantas características que tratan de la API RESTful u integración de Iframe.


<div>

 <span style="margin-right:980px;text-align:left;color:blue" onclick="document.location.href = 'user-post'; return false">< Conexiones y usuarios</span>

 <span style="margin-left:0px;float:right;color:blue" onclick="document.location.href = 'ldap-post'; return false">Configuración de LDAP ></span>

</div>

