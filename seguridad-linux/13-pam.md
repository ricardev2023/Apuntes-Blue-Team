---
description: Introducción al sistema centralizado de autenticación PAM.
---

# 13- PAM

## PAM (Pluggable Authentication Modules)

Modulos de autenticacion conectables. Potente framework utilizado en Linux para la autenticacion.\
La mayoria de las aplicaciones de Linux utilizan PAM para autenticar al usuario.

Esta autenticacion se basa en hacer coincidir la contraseña encriptada en **/etc/shadow** con la ingresada por el usuario.

En resumen, se puede entender como un sistema centralizado de autenticación.

## ARCHIVOS DE CONFIGURACION

**/etc/pam.d** --> en este directorio se almacenan todos los archivos de configuracion de autenticacion\
de las aplicaciones que utilizan PAM.

hay 4 tipos de módulos PAM :

* **auth** --> Sirve para autenticar al usuario. Tiene una serie de restricciones:
  * **REQUISITE** -->  si el requisito no se encuentra no se carga y deja de cargar otros modulos (error)
  * **REQUIRED** -->  si el requisito no se encuentra no se carga pero sigue cargando otros.
  * **SUFFICIENT** -->  si el modulo retorna exito, no hace falta procesar otros modulos.
  * **OPTIONAL** -->  en caso de fallo, se continua procesando modulos.
* **account** --> Este valor ofrece los servicios para la verificación de la cuenta con aspectos como caducidad de la contraseña del usuario o si el usuario tiene permitido el acceso al servicio solicitado.
* **password** --> Permiten actualizar las contraseñas de los usuarios y se integran junto a los módulos de autenticación de PAM
*   **session** --> Esta se encarga de administrar las acciones ejecutadas al principio y al final de una sesión.

    **P.E:**\
    `auth required pam_nologin.so --> debe comprobar que no es nologin`\
    `auth required pam_securetty.so --> debe comprobar que no se root`\
    `auth required pam_env.so --> debe comprobar que puede cargar las variables de entorno.`

## REFERENCIAS

{% embed url="https://www.solvetic.com/tutoriales/article/6543-como-funciona-y-configurar-pam-en-linux/" %}
Para información pormenorizada pinche en el enlace externo.
{% endembed %}
