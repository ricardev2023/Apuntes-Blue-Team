# 16- SFTP

## PROTOCOLO SFTP

SFTP transmite información a través de Secure Shell \(SSH\) para establecer una conexión segura y proporcionarle a las organizaciones un nivel más alto de protección en las transferencias de archivos. Esto se debe a que SFTP usa encriptación generada por algoritmos para mover la información de forma segura a su servidor, y mantener los archivos ilegibles durante el proceso, y además la autenticación evita el acceso no autorizado a los archivos durante la operación.

Si bien SFTP no requiere autenticación de doble factor, el usuario tiene la opción de solicitar tanto una ID   
de usuario como una contraseña, así como claves SSH, para una conexión más segura. Generar claves SSH ayuda a evitar que impostores o hackers se conecten al servidor. Los pares de claves SSH se deben generar con anticipación.

SFTP da la opción de realizar una gran variedad de tareas para gestionar archivos sensibles, desde eliminar archivos hasta reanudar transferencias pausadas. A diferencia de FTP sobre SSL/TLS \(FTPS\), SFTP solo necesita un único número de puerto \(puerto 22\) para establecer una conexión con un servidor.

## PAQUETE DE INSTALACION

`sudo apt install vsftpd`

## CONFIGURACION DEL SERVIDOR

**/etc/vsftpd.conf** --&gt;  descomentar `write enable= YES`  
 descomentar `local_umask= 022`  
 descomentar `chroot_local_user=YES`  
 descomentar `chroot_list_file=/etc/vsftpd.chroot_list`

**/etc/vsftpd.chroot\_list** --&gt; Este archivo no existe, hay que crearlo.  
 Es una lista de los usuarios con acceso al sftp.

VAMOS A GENERAR UNA ESTRUCTURA DE DATOS PARA UTILIZAR POR LOS USUARIOS DEL FTP.

`mkdir /home/ftp` --&gt; Hay que crear una carpeta cualquiera en una ruta donde todos tengan permisos para compartirla.

`mkdir /home/ftp/ftpdir` --&gt; Por seguridad se les da permisos en otra carpeta dentro.

`mkdir /home/ftp/ftpdir/usuarioftp` --&gt; Esta es la carpeta personal compartida del usuarioftp.

`useradd -g ftp -d /home/ftp/ftpdir/ -c "usuarioftp" usuarioftp`

`passwd usuarioftp`

`chmod 775 /home/ftp/`

`chgrp ftp /home/ftp/ftpdir`

`chmod 750 /home/ftp/ftpdir`

`chown usuarioftp /home/ftp/ftpdir/usuarioftp (no cambio el grupo propietario a ftp por que no es publica)`

`chmod -R 750 /home/ftp/ftpdir/usuarioftp #-R para que los hijos hereden los permisos de la carpeta`

VAMOS A GENERAR UNA SHELL PARA LOS USUARIOS FTP 

`sudo echo /bin/ftp >> /etc/shells #añado una linea con /bin/ftp`

`sudo nano /etc/passwd #modificamos la shell de los usuarios que vayan a utilizar la ftp por la /bin/ftp.`

Hasta este momento lo que hemos configurado es un servidor ftp normal al que se puede acceder libremente desde cualquier punto de la red local con un cliente ftp como filezilla.

Ahora vamos a darle forma para tener una autenticacion con SSL:

`mkdir /etc/vsftpd` --&gt; Para generar dentro el certificado autofirmado

`openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout vsftpd.pem -out vsftpd.pem`

`/etc/vsftp.conf -->  ssl_enable= YES`  
`rsa_cert_file=directorio_donde_esta_el_certificado`  
`rsa_private_key_file=lo_mismo_que_arriba`

CON ESTO YA TENDRIA MI SFTP TRABAJANDO CON CERTIFICADO AUTOFIRMADO.

## REFERENCIAS

{% embed url="https://geekland.eu/crear-servidor-sftp-enjaulado/" caption="Para información pormenorizada pinche en el enlace externo." %}

