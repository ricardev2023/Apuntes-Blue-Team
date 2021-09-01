---
description: Instalación y configuración segura básica de Apache Server
---

# 02- SECURIZAR APACHE SERVER

## APACHE SERVER

El servidor web es una parte fundamental de las aplicaciones basadas en web. Apache Web Server a menudo se coloca en el borde de la red, por lo que se convierte en uno de los servicios más vulnerables a los ataques.

Tener una configuración predeterminada proporciona mucha información confidencial que puede ayudar a los piratas informáticos a prepararse para un ataque a las aplicaciones. La mayoría de los ataques a aplicaciones web se producen a través de XSS, pérdida de información, administración de sesiones y ataques de inyección SQL que se deben a un código de programación débil y a la falta de limpieza de la infraestructura de la aplicación web.

### CREAR UN SERVIDOR WEB SIN SSL

Vamos a configurar nuestro servidor con Apache Server.

1. Instalamos apache2 `sudo apt install apache2`  
2. Tras instalar Apache2, se abre el puerto 80 \(http\) por defecto y se crea la pagina de apache por defecto en localhost o 127.0.0.1

   Tenemos 2 directorios:  
   **/var/www/** --&gt; es el directorio donde se almacenan las webs del servidor  
   **/etc/apache2** --&gt; directorio de configuracion de Apache \(salvo los log /var/log\)

   1. **Configuramos /etc/apache2/apache2.conf**  

   **Timeout** --&gt; tiempo en segundos que el servidor espera respuesta  
   **KeepAlive** --&gt; Permite mantener la conexion activa si esta ON  
   **MaxKeepAliveRequests** --&gt; Cuantas conexiones se pueden tener activas al mismo tiempo.  
   **KeepAliveTimeout** --&gt; Tiempo que tarda en desactivar una conexion por espera.

   1. **Configuramos /etc/apache2/ports.conf**  

      En este fichero permite marcar que puertos escucha mi servidor  

      Listen 80  

      Listen 443  

      Listen 8080  

      Ademas permite marcar por qué puerto quiero que saque un host.  

      `NameVirtualHost www.ejemplo.com:80`

3. En el directorio **/etc/apache2** hay un fichero de modulos activos y otro de modulos disponibles: **/etc/apache2/ mods-enabled** **/etc/apache2/mods-available**
4. Para activar o desactivar un modulo en apache2 lo hacemos de la siguiente manera en la terminal de Linux:  `a2enmod [modulo]` --&gt; activar `a2dismod [modulo]` --&gt; desactivar 
5. Cada vez que hacemos cambios, es necesario reiniciar el proceso  `/etc/init.d/apache2 restart`  
6. Por ultimo hay dos directorios importantes que tienen las paginas activas y disponibles  **/etc/apache2/sites-available** --&gt; disponibles **/etc/apache2/sites-enabled** --&gt; activas \(son enlaces simbolicos a las disponibles\) 
7. Aqui encontramos el archivo **000-default.conf** que es donde configuro mi sitio web:  _Es recomendable copiarlo antes de modificarlo para no modificar el default_.  **ServerName** \(nombre pag web\) **ServerAdmin** \(correo del admin\) **DocumentRoot** \(directorio donde esta la web\) **DirectoryIndex** \(cual es la pagina index\) **ServerAlias** \(alias para la web\) 
8. Hacemos un `ln -s` en enabled al archivo de configuracion de disponibles. 
9. utilizamos el comando `a2ensite [pagina.conf]`  
10. Genero un fichero en **www** que albergue la pagina web. Dentro genero el **index.html**

### CREAR UN SERVIDOR WEB CON SSL

En caso de querer hacer una pagina con certificado SSL \(puerto 443\), se hace lo mismo que anteriormente pero en el paso 7 se copia y modifica el **default-ssl.conf**:

* El **módulo SSl** debe estar **activo**  Hay que añdir el **puerto 445** a **ports.conf** con **listen**.

```text
ServerAdmin
 DocumentRoot
 DirectoryIndex
 SSLEngine On
 SSLCertificateKeyFile /etc/ssl/certificados/certificado.key
 SSLCertificateFile /etc/ssl/certificados/certificado.pem
```

* Vamos a generar un **certificado SSL**:
* Genero un directorio certificados en `/etc/ssl`
* dentro de ese directorio ejecuto:  `openssl req -x509 -newkey rsa:2048 -keyout certificado.key -out certificado.pem -days 15695`

De esta manera se generan dos claves, una publica y una privada.

Para ver más información acerca de los certificados SSL pinche en el siguiente link:

{% page-ref page="03-criptografia.md" %}

## REFERENCIAS

[https://geekflare.com/es/apache-web-server-hardening-security/](https://geekflare.com/es/apache-web-server-hardening-security/)  
[https://geekflare.com/es/apache-2-4-6-installation-on-unix/](https://geekflare.com/es/apache-2-4-6-installation-on-unix/)

