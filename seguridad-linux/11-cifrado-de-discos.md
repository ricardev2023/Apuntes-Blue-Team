---
description: Introducción al cifrado de discos en Linux
---

# 11- CIFRADO DE DISCOS

## INTRODUCCIÓN

**Cryptsetup** es la herramienta de línea de comandos para interactuar con **dm-crypt**, para crear, acceder y gestionar dispositivos cifrados. La herramienta se expandió posteriormente para admitir diferentes tipos de cifrado que dependen de los módulos del kernel de Linux device-mapper y cryptographic. La expansión más notable fue para la extensión de configuración de clave unificada de Linux («Linux Unified Key Setup» siglas en inglés LUKS), que almacena toda la información de configuración necesaria para **dm-crypt** en el propio disco y abstrae la partición y la gestión de claves en un intento por mejorar la facilidad de uso. Los dispositivos a los que se accede a través de **device-mapper** se denominan dispositivos de bloque.

`apt install cryptsetup` --> programa mas utilizado en linux para cifrar discos. se encarga de encriptar/desencriptar los datos escritos en un dispositivo de almacenamiento.

El cifrado a nivel de disco duro garantiza que los archivos siempre se almacenen en el disco de forma\
cifrada. Los archivos solo están disponibles para poder ser leídos mientras el sistema está en ejecución\
y desbloqueado por uno de los usuarios con acceso a la clave de descifrado. Una persona no autorizada que intente acceder al contenido del disco, solo encontrará datos confusos, en lugar de los archivos reales.

Como siempre que trabajamos en un disco duro empezamos particionando (fdisk /dev/\[disco])

{% embed url="https://ajcruz15.gitbook.io/apuntes-linux/apuntes-terminal/07-montaje-de-discos-y-dispositivos" %}
Para más información de los comandos a utilizar, pinche en éste enlace.
{% endembed %}

## PARÁMETROS DE CRYPTSETUP

`-c` --> permite elegir el tipo de cifrado:\
**aes-cbc-essiv:sha256** --> por defecto\
**aes-xts-plain64** --> para trabajar con lucks

`-s` --> tamaño de clave. (por defecto 512 bits)

`-h` --> algoritmo de hash utilizado (sha256 por defecto)

`-y` --> permite verificar la contraseña pidiendola dos veces.

## CIFRADO DE UN DISCO CON CRYPTSETUP

* `cryptsetup -c aes-cbc-essiv:sha256 -y luksFormat /dev/sdb` --> configuramos la particion con cifrada con LUKS. Tambien se especifica el tipo de cifrado y la verificacion de contraseña.&#x20;
* `cryptsetup luksDump /dev/sdb` --> permite ver el resultado del cifrado.&#x20;
* `cryptsetup luksOpen /dev/sdb cifrado` --> ahora desbloqueamos el dispositivo cifrado y le asignamos un nombre al dispositivo mapeado (en este caso, cifrado). Una vez hecho esto, la direccion de cifrado es **/dev/mapper/cifrado.**&#x20;
* `mkfs.ext4 -L cifrado /dev/mapper/cifrado` --> ahora generamos un sistema de archivos en la particion, dandole al volumen un nuevo nombre.&#x20;
* `cryptsetup remove cifrado` --> por ultimo, eliminamos el contenedor cifrado, dejando el volumen cifrado completamente.
