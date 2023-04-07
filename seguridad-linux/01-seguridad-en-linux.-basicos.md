---
description: Introducción a la seguridad en sistemas Linux
---

# 01- SEGURIDAD EN LINUX

## CONCEPTOS BASICOS DE SEGURIDAD

Hay dos tipos de seguridad a la hora de montar un servidor:

* **Seguridad Fisica:** Todos los medios que impiden el acceso fisico al servidor.
* **Seguridad Lógica**: Todas las herramientas que permiten proteger la informacion del servidor.

## SEGURIDAD FÍSICA

{% hint style="info" %}
En ésta página no se van a tratar los aspectos de la seguridad física por encontrarse fuera del alcance de los temas a tratar.\
Sin embargo estas medidas de seguridad van desde racks cerrados con llave para los servidores hasta la implementación de videovigilancia, controles biométricos, personal de seguridad privada, etcétera.
{% endhint %}

## SEGURIDAD LÓGICA

Hay muchos métodos fundamentales para asegurar nuestra información. Antes de entrar en procedimientos más concretos, vamos a ver los métodos básicos muy por encima:

1. Control de Permisos
2. Control de Sudoers
3. Monitorizacion de puertos (netstat)
4. Monitorizacion de procesos (lsof y fuser)
5. Monitorizacion de usuarios (who / w / last)
6. Superservidores (inetd esta obsoleto / xinetd esta algo mas actualizado)
7. Gestion de contraseñas
8. Desactivacion del acceso root (/etc/securetty)
9. Anti Rootkit (chkrootkit / rkhunter)
10. Control de cambios (tripwire)
11. Copias de seguridad

### 1- CONTROL DE PERMISOS

Es importante ser restrictivo a la hora de dar los permisos a los ficheros del sistema.

**P.E**: no es seguro que cualquier usuario tenga acceso de lectura a `/etc/passwd` ya que en ese\
archivo se almacenan todos los usuarios de la red.

La seguridad recomendada para ficheros clave es:

* **Propietario y Grupo** --> root
* `/etc/passwd` --> _644_
* `/etc/shadow` --> _000_ (hay hashes de contraseñas)
* `/media` --> _700_ (es donde se montan las memorias extraibles USB)
* `/run/media` --> _700_ (es donde se montan las memorias extraibles USB en distribuciones SYSTEMD)
* El resto de **archivos** deben tener _640_ y los **directorios** _750_

{% embed url="https://ajcruz15.gitbook.io/apuntes-linux/apuntes-terminal/03-gestion-de-permisos-en-archivos-y-directorios" %}
Si quiere saber más sobre los permisos pulse éste enlace
{% endembed %}

### 2- CONTROL DE SUDOERS

**BISUDO** --> Con este comando (o accediendo a `/etc/sudoers`) se puede gestionar quien tiene permisos\
SUDO. La estructura del fichero `/etc/sudoers` es la siguiente:

* **Definicion de Alias**

Representa a un usuario, comando o equipo y engloba bajo un mismo nombre a una serie de elementos para utilizarlos en la definición de reglas. Los **Alias** pueden ser:

1. **Cmnd\_Alias** --> Define alias de comandos.
2. **User\_Alias** --> Define alias de usuarios normales.
3. **Runas\_Alias** --> Define alias de usuarios administradores.
4. **Host\_Alias** --> Define alias de equipos.

La estructura para **crear un alias** es:\
`tipo_alias NOMBRE_DEL_ALIAS = elemento1, elemento2, elemento3, ... elementoN`

1. **tipo\_alias** --> los hemos visto arriba (la primera en mayuscula)
2. **NOMBRE\_DEL\_ALIAS** --> identificador del alias (la primera en mayuscula y se acostumbra a ponerlo todo)
3.  **elemento1** --> Los elementos dependen del tipo de alias, vamos a ver ejemplos:\
    `Cmnd_Alias WEB = /usr/sbin/apachectl, /usr/sbin/httpd, sudoedit /etc/httpd/ #solo esos comandos`\
    `Cmnd_Alias APAGAR = /usr/bin/shutdown -h 23\:00 #solo shutdown con esos parametros`

    `Cmnd_Alias NET_ADMIN = /sbin/ifconfig, /sbin/iptables, WEB #incluye el alias WEB anterior`\
    `Cmnd_Alias TODO_BIN = /usr/bin/, !/usr/bin/rpm #todo menos rpm (es un programa inseguro)`

`User_Alias MYSQL_USERS = andy, marce, juan, %mysql #esos usuarios y el grupo mysql`\
`User_Alias TODOS = ALL, !samuel, !david (todos los usuarios menos samuel y david)`\
`User_Alias OPERADORES = MYSQL_USERS, alejandra #añade el alias + alejandra`

`Runas_Alias OPERADORES = #501, fabian #funciona igual pero se puede utilizar el UID con "#"`

`Host_Alias LANS = 192.168.0.0/24, 192.168.0.1/255.255.255.0 #define esos dos equipos como LANS`

*   **Definicion de Opciones (Defaults)**

    Permiten definir ciertas características de comportamiento para los alias previamente creados, para usuarios, usuarios privilegiados, para equipos o de manera global para todos. Hay 4 niveles:
* **Global** --> Defaults opcion1, opcion2 ...
* **Usuario** --> Defaults:usuario opcion1, opcion2 ...
* **Usuario Privilegiado** --> Defaults>usuario opcion1, opcion2 ...
* **Equipo** --> Defaults@equipo opcion1, opcion2 ...

Hay una lista muy extensa de defaults en `man sudoers`. Pero se pueden dividir en **4 tipos**:

1. **flags o booleanos** --> Se indica la opcion y se activa () o desactiva (!)  `Defaults !authenticate, log_host`
2. **Enteros** --> Manejan valores de números enteros en sus opciones  `Defaults:fernanda, regina passwd_tries = 1, passwd_timeout = 1`
3. **Cadenas** --> Manejan valores con mensajes, rutas de archivos...  Defaults badpass\_message = "Intenta de nuevo: "
4. **Listas** --> Permite establecer/eliminar variables de entorno. Solo hay tres y son muy confusas  `Defaults env_delete -= HOSTNAME #Elimina la variable HOSTNAME pero no el resto`  `Defaults env_reset`  `Defaults env_check += DISPLAY, PS1`

* **Definicion de Reglas**

Las reglas de acceso definen que usuarios ejecutan que comandos bajo que usuario y en que equipos.\
La estructura es:

`usuario1 maquinas=(usuarios) comandos`

Ejemplos\
_\*\*_`daniela ALL = /sbin/iptables #daniela en cualquier host o equipo puede utiliar iptables`

`ADMIN ALL = ALL #Los usuarios definifos en el alias 'ADMIN' desde cualquier host pueden ejecutar cualquier comando`

`WEB LANS = (OPERADORES) TODO_BIN #para esto sirven los alias`

* **TAGS (etiquetas de comandos)**

Cuando se definen reglas, en la lista de comandos, estos pueden tener cero (como en los ejemplos anteriores) o más tags. Existen 6 de estas etiquetas o tags:

1. **NOPASSWD Y PASSWD** --> indica en la definicion de reglas si es necesaria o no la autenticacion
2. **NOEXEC Y EXEC** --> Evita que los programas con escape a shell puedan ejecutar comandos.
3. **SETENV Y NOSETENV** --> permitirá al usuario indicado cambiar el entorno de variables del usuario

### 3- MONITORIZACION DE PUERTOS

**NETSTAT** --> Network Statistics. Lista las conexiones activas tanto entrantes como salientes\
`-r` --> nos permite ver las rutas de los equipos\
`-s` --> muestra las estadísticas de los protocolos\
`-i` --> muestra la transmisión por interfaces de red\
`-a` --> muestra todo\
`-panut` --> muestra la informacion mas relevante de la red en el siguiente formato:\
`Protocolo / Trafico / IP + puerto (privada) / IP + puerto (publico) / Estado / PID + Servicio`

### 4- MONITORIZACION DE PROCESOS

**LSOF** --> muestra los ficheros y librerias que se utilizan en los procesos activos.\
Si no se utiliza ningún argumento, muestra los ficheros y librerias en uso por todos los procesos de sistema en ese instante.\
Si solo quiero uno, tengo que poner como argumento el proceso concreto.\
`-i` --> muestra los puertos que nos muestran al exterior.\
`-p` --> muestra todos los procesos relacionados con un PID.\
`-p` \`pidof \[proceso]\` --> lo mismo pero sin poner PID.

**FUSER** --> muestra todos los procesos que utilizan socket en un directorio dado.\
`-v` --> verbose\
`-k` --> mata el proceso\
`-i` --> modo interactivo (permite interactuar con la salida de fuser)

### 5- MONITORIZACION DE USUARIOS

**WHO** --> Muestra por pantalla usuarios logueados y desde cuando.

**W** --> Muestra por pantalla usuarios logueados con informacion sobre que hacen

* **USER** --> que usuario es.
* **TTY** --> es la estacion&#x20;
* **FROM** -->&#x20;
* **LOGIN** --> a que hora se ha logueado.
* **IDLE** --> tiempo que lleva disponible.
* **JCPU** --> Tiempo de uso de CPU.
* **PCPU** --> Tiempo de proceso actual.
*   **WHAT** --> que esta haciendo (coincide con los procesos activos).

    `-s` --> muestra solo USER TTY FROM WHAT\
    `-i` --> muestra la direccion IP en vez de FROM\
    `-v` --> muestra la informacion de la version\
    `user` --> muestra informacion sobre un usuario concreto

**LAST** --> Muestra un listado de los usuarios que se han logueado ultimamente

### 6- GESTION DE CONTRASEÑAS

Todos recomiendan al menos 8 caracteres incluyendo minusculas, mayusculas, numeros y simbolos.\
Es recomendable que tengan 14 caracteres por que el hash que genera se multiplica por dos.\
Siempre que deje es preferible poner espacios en la contraseña, ya que los diccionarios no lo incluyen.

Es importante que cada usuario fisico tenga su propio usuario en el sistema para evitar problemas legales\
al compartir usuarios y no saber quien ha cometido un delito desde ese usuario. (teoria no repudio)

Se recomienda cambiar la contraseña minimo cada 3 meses. Si no se manejan datos sensibles, puede llegar al\
año pero no es lo recomendado

CHAGE --> permite cambiar las politicas de los usuarios (la info del /etc/passwd)\
\-d --> Establece el dia del ultimo dia que se cambio la contraseña desde 1ENE1970\
\-E --> Establece la fecha de caducidad del usuario con la misma premisa que -d\
\-I --> Establece los dias que debe estar una cuenta inactiva para que se deshabilite\
\-l --> Muestra la informacion de la edad de la cuenta\
\-m --> Establece el numero minimo de dias antes de volver a cambiar la contraseña.\
\-M --> Establece el numero maximo de dias\
\-W --> Establece los dias que el sistema avisara al usuario antes de la expiracion de contraseña.

PWGEN --> genera contraseñas aleatorias\
14 --> numero de caracteres de la contraseña.

### 7- DESACTIVACION DEL ACCESO ROOT

Existen varias formas para asegurar que el acceso como root esta limitado. Las mas conocidas son:

/etc/securetty --> En este archivo se puede decidir que dispositivos conectados a la computadora tiene\
acceso root a la consola.

/etc/passwd --> Modificar la shell de root de /bin/bash a /bin/nologin. Impide el acceso a root desde shell

/etc/ssh/sshd\_config --> PermitRootLogin = no Previene el acceso a root desde OpenSSH.

/etc/pam.d/ --> Hay que asegurarse de que se requiera pam\_listfile.so para la autenticacion. Asi se limita\
el acceso a root a todos los servicios de red PAM (cliente FTP, cliente correo...)

### 8- ANTI-ROOTKIT

Un rootkit es un conjunto de software malintencionado que busca dar acceso root a un hacker y esta\
diseñado para permanecer oculto en el sistema.

CHKROOTKIT --> analiza el sistema en busca de rootkit.

RKHUNTER --> es mas nuevo y potente que el anterior y ademas incluye firmas de linux.

CLAMAV --> antivirus gratuito realizado por la comunidad linux. No es de calidad, pero es gratis y sirve\
para ver las firmas del malware y entender el funcionamiento de un AV.\
freshclam --> actualiza la base de datos.\
checkclam --> realiza un analisis del equipo.

### 9- CONTROL DE CAMBIOS

TRIPWIRE --> herramienta que controla los cambios realizados en el sistema. Muy bueno para securizar\
un servidor.

/etc/tripwire/twpol.txt --> archivo de configuracion\
comentar la linea /etc/rc.boot (ya no existe ese directorio)\
todas las /root/.loquesea se comentan para no dar error\
comentar /proc tambien.

despues ejecutar sudo twadmin -m P /etc/tripwire/twpol.txt para inicializar las politicas

\--init --> inicializa la base de datos.

Por ultimo, para generar un informe tenemos dos opciones:\
1\. tripwire --check > \~/Escritorio/report.txt (así evitas tener que descifrar el resultado)

1.  tripwire --check --> guarda un reporte en /var/lib/tripwire/report/ &#x20;

    twprint -m r --twrfile \[ruta del reporte] --> permite ver el reporte descifrado

VER MAN POR QUE TIENE MUCHAS OPCIONES

### 10- COPIAS DE SEGURIDAD

TAR --> Permite trabajar con archivos comprimidos .tar

\-c --> crear un fichero\
\-f --> especificar la ruta\
\-v --> modo verbose\
\-x --> extraer fichero\
\-z --> comprime o descomprime en gzip (.gz)\
\-j --> comprime o descomprime en bzip2 (.bz2)\
\-J --> comprime o descomprime en xz (.xz)\
\-t --> lista los archivos que se encuentran dentro del comprimido.\
P.E:\
tar -cvf \[ruta del contenedor creado] \[ruta de archivos que van dentro del contenedor]\
crea un contenedor .tar con los archivos de la ruta seleccionada\
tar -zcvf \[ruta del contenedor creado] \[ruta de archivos que van dentro del contenedor]\
crea in contenedor .tar.gz comprimido con los archivos seleccionados.

## REFERENCIAS

{% embed url="https://www.infoteknico.com/guia-de-seguridad-servidores-linux/" %}
Para una guía en profundidad sobre éstos temas, pinche en éste enlace
{% endembed %}

[https://www.solvetic.com/tutoriales/article/1458-entender-los-permisos-linux-chmod/](https://www.solvetic.com/tutoriales/article/1458-entender-los-permisos-linux-chmod/)\
[https://ayuda.svigo.es/index.php/servidores/119-gestion-de-permisos-en-servidores-linux](https://ayuda.svigo.es/index.php/servidores/119-gestion-de-permisos-en-servidores-linux)\
[https://www.thegeekdiary.com/understanding-etc-security-limits-conf-file-to-set-ulimit/](https://www.thegeekdiary.com/understanding-etc-security-limits-conf-file-to-set-ulimit/)\
[https://www.ochobitshacenunbyte.com/2017/01/18/servicios-de-internet-en-linux-con-xinetd/](https://www.ochobitshacenunbyte.com/2017/01/18/servicios-de-internet-en-linux-con-xinetd/)
