---
description: Introducción y configuración del protocolo SSH
---

# 04- SSH

## PROTOCOLO

Protocolo Secure Shell, interprete de comandos seguro. Lo que hace es comunicar una maquina con otra de manera cifrada.  
El puerto por defecto es el **22**

`apt-get install ssh-client`

Tambien existe el **RSH** que funciona exactamente igual que el **SSH** pero con texto plano. Nos permite realizar una conexion y analizar el trafico.  
`rsh usuario@ip_privada`  
`rlogin usuario@ip`

## ARCHIVOS DE INTERES

**/etc/ssh/ssh\_config** --&gt; configuracion del cliente

**/etc/ssh/sshd\_config** --&gt; configuracion del servidor.

### CONSEJOS DE SEGURIDAD EN LA CONFIGURACIÓN

* **Puerto** --&gt; viene comentado y 22 por defecto, seria conveniente utilizar un puerto por encima del 10000. Cualquier ataque va a buscar el 22.
* **LoginGraceTime** --&gt; Tiempo de gracia. Desde que hago la solicitud SSH hasta que  introduzco la contraseña. Debe ser suficiente para introducirla pero no tan largo que suponga un problema de seguridad.
* **PermitRootLogin no** --&gt; Debe estar asi ya que si acceden a SSH con root, tienen acceso  total.
* **MaxAuthTries** --&gt; Cuantas veces se puede introducir mal la contraseña
* **MaxSessions** --&gt; Cuantas sesiones pueden estar activos en SSH al mismo tiempo.
* **PermitEmptyPasswords no** --&gt; Debe estar asi para asegurar contraseñas seguras.
* **UsePAM yes** --&gt; Gestion de accesos privilegiados.  AllowUsers  DenyUsers  AllowGroups  DenyGroups

## COMANDOS

`ssh [usuario]@[ip privada destino]` --&gt; Permite acceso con el usuario en la maquina destino  
`-p` --&gt; indica el puerto de entrada

**SSH-KEYGEN** --&gt; genera una contraseña para el acceso. Va de la mano con:  
`-t` --&gt; tipo de cifrado \(rsa por ejemplo\)  
`-b` --&gt; cantidad de bites de la clave \(normalmente de 2048 o 3072\)

**SSH-COPY-ID** --&gt; permite copiar la clave publica de un usuario en otra maquina.  
`-i [clave.pub]` --&gt; archivo de id  
`ssh-copy-id -i clave.pub ricardo@192.168.0.19 -p 22 #es un buen ejemplo`

## ACCEDER REMOTAMENTE CON CERTIFICADO SSH

El acceso a un equipo remoto con certificado SSH es igual de sencillo que sin él. Lo único que se debe tener es la clave pública del host al que queremos acceder en el fichero: `/home/usuario/.ssh/known_hosts`

Si todo está correctamente configurado, obtendremos acceso sin necesidad de introducir credenciales.

## REFERENCIAS

[https://www.dbigcloud.com/virtualizacion/192-como-crear-una-relacion-de-confianza-ssh-entre-hosts-vmware-esxi-y-linux.html](https://www.dbigcloud.com/virtualizacion/192-como-crear-una-relacion-de-confianza-ssh-entre-hosts-vmware-esxi-y-linux.html)

