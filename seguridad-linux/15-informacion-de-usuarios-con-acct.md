---
description: Monitorear la información de usuarios con el comando ACCT
---

# 15- MONITOREAR USUARIOS CON ACCT

## ACCT

Es una pequeña aplicacion que nos permite obtener informacion de los usuarios.

El paquete se obtiene con apt install acct.

Tiene muchas opciones para obtener informacion muy variada.

## COMANDOS

**AC** --> me permite saber las horas totales que el usuario ha estado logado.\
`-d` --> me lo divide por días.

**SA** --> permite obtener informacion de los comandos utilizados por un usuario.

* col 1: num de veces que se utiliza el comando
* col 2: tiempo en minutos de sistema que ha permanecido activo
* col 3: tiempo en minutos de procesador que ha permanecido activo
* col 4: 0avio. Nunca cambia (buscar significado)
* col 5: uso de CPU
*   col 6: comando.

    `-u` --> lo divide por usuarios.\
    `-c` --> muestra los datos en porcentajes.

**LASTCOMM** --> permite ver los ultimos procesos de un usuario.

**ACCTON** --> permite activar procesos de un usuario

**ACCTOFF** --> permite desactivar procesos de un usuario

**LASTB** --> muestra un listado de los ultimos usuarios que se han logado.

## REFERENCIAS

{% embed url="https://blog.desdelinux.net/monitorear-actividad-de-usuarios-con-comando-acct/" %}
Para información pormenorizada pinche en el enlace externo.
{% endembed %}
