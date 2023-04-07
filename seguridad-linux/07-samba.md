---
description: >-
  Conceptos básicos de Protocolo SMB en Linux y configuración del servicio
  SAMBA.
---

# 07- SAMBA

## PROTOCOLO SMB

{% embed url="https://ajcruz15.gitbook.io/red-team/services-hacking/445-smb" %}
Para saber lo básico del protocolo SMB clica en el enlace.
{% endembed %}

## SERVICIO SAMBA

Servicio que utiliza Linux como cliente y servidor del protocolo smb de windows. Es un servidor de archivos que permite compartir archivos e impresoras.\
Los permisos de samba son diferentes a los de linux. Por eso:

* lo primero es crear usuarios y grupos para compartir:  `usuario samba`  `grupo smb --> con samba y strelock` &#x20;
* Creamos passwords y permisos para el servicio samba. `smbpasswd -a strelock #añade contraseña de samba al usuario strelock` `smbpasswd -a samba #lo mismo con el usuario samba.` &#x20;
* Creamos las carpetas a compartir y le damos permisos, hay que tener en cuenta que se tendran en cuenta los permisos mas restrictivos (los de linux o los de samba) por ello: `mkdir /home/carpeta_samba` `mkdir /home/carpeta_samba/compartida` `mkdir /home/carpeta_samba/compartida2` &#x20;
* Configuramos el recurso compartido con /etc/samba/smb.conf (en la parte final del archivo .conf)

```
[compartida]
 comment = Carpeta compartida en Linux
 path = /home/carpeta_samba/compartida
 browsable = yes
 read only = no
 write list = @smb 
 valid users = samba,@smb
```

* Reiniciamos servicio `/etc/init.d/samba restart`&#x20;
* Ya podemos entrar a nuestros archivos compartidos.

{% hint style="info" %}
Desde windows, al ser SMB un protocolo y un servicio internos, no hace falta instalar ningún programa y se puede acceder directamente utilizando la ruta UNC: `\\192.168.0.18`
{% endhint %}

*   Hacemos un test para comprobar que los recursos compartidos funcionan correctamente:

    `testparm`
