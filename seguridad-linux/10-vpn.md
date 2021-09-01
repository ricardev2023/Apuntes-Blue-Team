---
description: Introducción al servicio VPN y configuración de dicho servicio con OpenVPN.
---

# 10- VPN

## SERVICIO VPN

**Red Privada Virtual**, en español, son un tipo de red en el que se crea una extensión de una red privada   
para su acceso desde Internet.

Gracias a una conexión VPN, podemos establecer contacto con máquinas que estén alojadas en nuestra red local \(u otras redes locales\) de forma totalmente segura, ya que la conexión que se establece entre ambas máquinas viaja totalmente cifrada, es como si desde nuestro equipo conectado a Internet estableciésemos un túnel privado y seguro hasta nuestro hogar o hasta nuestra oficina, con el que podremos comunicarnos sin temer que nuestros datos sean vulnerables.

  
Podemos resumir los casos prácticos en los siguientes:

- Acceder a una red de trabajo o de casa mientras se está de viaje.  
- Esconder los datos de navegación.  
- Entrar en sitios con bloqueo geográfico.  
- Evitar la censura en Internet.

Podemos instalar la aplicación para crear un servidor VPN:  
`apt install openvpn`  
`apt install openssl #lo necesitamos para cifrar las comunicaciones.`

{% hint style="info" %}
En Linux se utiliza el **OpenVPN** que utiliza el puerto 1194 por defecto. Es importante cambiarlo por seguridad para evitar que los hacker identifiquen que estamos utilizando OpenVPN.
{% endhint %}

## ARCHIVOS RELEVANTES

**/etc/openvpn/client/** --&gt; Es el directorio donde se configura un cliente de la red VPN.

**/etc/openvpn/server/** --&gt; Es el directorio donde se configura el servidor VPN.

**`mkdir /etc/openvpn/ssl/`** --&gt; es el directorio en la que vamos a almacenar los certificados.

**/usr/share/doc/openvpn/examples/sample-config-files/** --&gt; Directorio con ejemplos de Archivos de configuracion.

**/usr/share/doc/openvpn/examples/sample-keys/** --&gt; Directorio de ejemplos de Certificados de OpenVPN.

## CONFIGURAR SERVIDOR OPENVPN

* `mkdir /etc/openvpn/ssl/` 
* `cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz   /etc/openvpn/`

* Extraemos el server.conf con gunzip `gunzip server.conf.gz`  
* `cp /usr/share/doc/openvpn/examples/sample-keys/ca.crt  /etc/openvpn/ssl` `cp /usr/share/doc/openvpn/examples/sample-keys/server.key  /etc/openvpn/ssl` `cp /usr/share/doc/openvpn/examples/sample-keys/server.crt.gz  /etc/openvpn/ssl` `cp /usr/share/doc/openvpn/examples/sample-keys/dh2048.pem /etc/openvpn/ssl` 
* HASTA AQUI SOLO HEMOS PUESTO LOS ARCHIVOS EN UNA RUTA MAS COMODA. 
* Configuramos server.conf 
  * Descomentamos **;local a.b.c.d** y ponemos la IP del servidor`local 192.168.43.124 (Deveria ser fija)`  
  * Cambiamos el puerto TCP/UDP por uno superior a 10000. \(Por seguridad\) 
  * La VPN puede utilizar protocolo TCP o UDP \(por defecto\). Está bien que utilice el segundo  para que no ralentice la comunicacion por TCP. --&gt; `proto UDP`  
  * modificamos la ruta de los certificados poniendo la correcta: `ca /etc/openvpn/ssl/ca.crt` `cert /etc/openvpn/ssl/server.crt` `key /etc/openvpn/ssl/server.key`  
  * Tambien hay que cambiar la ruta del dh2048.pem para que no de fallo. Para trabajar con Apache. `dh /etc/openvpn/ssl/dh2048.pem`  
  * `server 10.8.0.0 250.250.250.0` es la IP de la red VPN \(se puede acceder al servidor en 10.8.0.1\) Se puede modificar y poner la que la empresa quiera. 
  * Descomentamos una linea de push de tal manera que me pueda conectar al servidor VPN con el rango de IP de mi red privada, no la red VPN. Por ejemplo:  `push "route 192.168.43.0 255.255.255.0"`  
  * Buscamos tls \(con ctrl+w\) y comentamos la linea para que no de error ya que no tenemos ta.key. 
  * `openvpn --config /etc/openvpn/server.conf` --&gt; asi se arranca el servicio y se aplica la configuracion del Servidor

## CONFIGURAR CLIENTE OPENVPN

* `mkdir /etc/openvpn/ssl/`  
* `cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf  /etc/openvpn/`  
* `cp /usr/share/doc/openvpn/examples/sample-keys/ca.crt /etc/openvpn/ssl` `cp /usr/share/doc/openvpn/examples/sample-keys/client.key /etc/openvpn/ssl` `cp /usr/share/doc/openvpn/examples/sample-keys/client.crt.gz /etc/openvpn/ssl`  
* HASTA AQUI SOLO HEMOS PUESTO LOS ARCHIVOS EN UNA RUTA MAS COMODA. 
* Configuramos client.conf 
  * En `remote my-server-1 1194` cambiamos my server 1 por la IP y el puerto por el que hayamos puesto. 
  * Modificamos la ruta de los certificados poniendo la correcta: `ca /etc/openvpn/ssl/ca.crt`  `cert /etc/openvpn/ssl/server.crt` `key /etc/openvpn/ssl/server.key`  
  * Buscamos tls \(con ctrl+w\) y comentamos la linea para que no de error ya que no tenemos ta.key. 
  * `openvpn --config /etc/openvpn/client.conf` --&gt; asi se arranca el servicio en primer plano. 

