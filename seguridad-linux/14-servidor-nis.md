---
description: Introducción a la autenticación en linea
---

# 14- SERVIDOR NIS

## INTRODUCCIÓN

Un **servidor NIS** \(Network Information Service\) hace referencia a un protocolo de **servicios de directorio** cliente-servidor para permitir el intercambio de datos como pueden ser los nombres de usuarios y los nombres de equipos de una red.

El servicio NIS en un principio se llamaba **Yellow Pages** \(YP\), y como veras más adelante los ficheros de configuración, carpetas y subcarpetas todavía conservan sus iniciales, así como también sus _demonios_ nis\(servidor\) o nis\(cliente\).

Lo que hace NIS es centralizar a un equipo como **servidor de nombres** y para la **autenticación de usuarios** proporcionando de esta forma a los clientes acceso a sus bases de datos como puede ser los ficheros `/etc/passwd`, `/etc/group`, `/etc/hosts`,…

Es como cualquier otra base de datos.

El servicio es **ypbind**

{% hint style="info" %}
SIRVE PARA GESTIONAR LOS DATOS DE AUTENTICACIÓN EN LINEA.
{% endhint %}

## ARCHIVOS CONFIGURACION PARA SERVIDOR

**/etc/default/nis** --&gt; Archivo de configuracion del servicio. Para generar un servidor, hay que cambiar:

```text
NISSERVER=master
 NISCLIENT=false
:
/etc/ypserv.conf --> Archivo con las restricciones del servidor
 hay que añadir la mascara de red y la IP del host.
```

**/var/yp/Makefile** --&gt; Es el archivo de configuracion del servidor. Hay que compilarlo una vez esté a nuestro gusto.  
`make -C /var/yp/`

## ARCHIVOS CONFIGURACION PARA CLIENTE

**/etc/default/nis** --&gt; Lo dejamos como está

**/etc/yp.conf** --&gt; descomentamos la ultima linea:  
`ypserver ip_del_servidor`

**/etc/ypserv.securenets** --&gt; Con este archivo restringimos la red que conecta con nosotros  
Hay una linea que permite el acceso de todo el mundo. Hay que comentarla.

**/etc/nsswitch.conf** --&gt; Se configuran las prioridades de los credenciales. Es decir, donde contrasto las  
credenciales y en que orden. Por defecto es:

```text
 passwd: compat systemd
 group: compat systemd
 shadow: compat
 gshadow: files
```

Si quiero utilizar un servidor NIS, tengo que añadir NIS al final de cada linea para que despues de  
comprobar los anteriores, si no lo encuentra, lo busque en el nis.

**/etc/passwd** --&gt; añadimos una linea al final con el siguiente contenido: `+::::::`  
Lo que indica que hay mas usuarios de los que aparecen en el archivo, lo que le permite buscar en el NIS.

**/etc/shadow** --&gt; de la misma manera, en el shadow añadimos `+::::::::` para que la maquina detecte que hay mas usuarios que los que tenemos en el archivo.

**/etc/group** --&gt; hacemos lo mismo. `+:::`

**/etc/gshadow** --&gt; hacemos lo mismo `+:::`

## HERRAMIENTAS NIS

**ypcat**: esta herramienta es utilizada para obtener datos del servidor NIS al extraerlos del mapa NIS.

**ypwhich**: obtiene el nombre del servidor Linux NIS que está respondiendo a tus peticiones.

**ypmatch**: en vez de obtener el mapa entero, puedes buscar por una clave para obtener una entrada en específico.

## REFERENCIAS

{% embed url="https://instructorbenyblanco.wordpress.com/servidor-nis-en-linux-configuracion-y-funcionamiento/" caption="Para información pormenorizada pinche en el enlace externo." %}

