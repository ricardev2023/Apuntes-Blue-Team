---
description: Introducción a los sistemas RAID
---

# 06- SISTEMAS RAID

Conjunto redundante de discos independientes. Es decir, un conjunto de discos que se tratan como un solo disco.\
La finalidad de un sistema RAID de discos es la de proteger los datos en caso de que un disco duro falle, o en algunos casos tiene como función principal mejorar la velocidad de lectura de varios discos que conforman un único volumen.

## TIPOS DE RAID

* **RAID-0** --> Este tipo de RAID supone el concepto principal que proporciona mayor velocidad al sistema.  La información se va escribiendo en dos discos de manera alterna, es decir, un bit en uno, y otro bit en otro, de manera que el ancho de banda es literalmente el doble y por eso se mejora notablemente el rendimiento en este modo. Además, se duplica la capacidad del volumen, es decir, si usamos dos discos duros de 1 TB cada uno, tendríamos un volumen de 2 TB.  La contrapartida de este tipo de RAID es que si fallara alguno de los dos discos duros, la información de los dos se echaría a perder puesto que se encontraría repartida entre los dos.&#x20;
* **RAID-1** --> Este es el otro tipo básico de RAID, y supone el concepto principal de redundancia.   En este modo, los datos se escriben en los dos discos de manera simultánea, siendo el uno una   copia exacta del otro, motivo por el que se conoce a este modo como «mirroring». En este caso,   si se estropeara uno de los dos discos no pasaría nada porque los datos estarían todavía en el   otro, y bastaría con reemplazar el disco estropeado por uno nuevo para volver a restablecer el   RAID 1.   La parte mala de este modo de RAID es que no se gana ningún rendimiento, más bien al contrario   porque todos los datos deben escribirse dos veces. Además, el tamaño del volumen será el del   disco de menor capacidad. Es decir, si usáramos un disco de 1 TB y otro de 500 GB, tendríamos   un volumen de 500 GB en RAID 1.&#x20;
* **RAID-5** --> Este es el modo más utilizado en la actualidad, puesto que permite tener casi cualquier   número de discos duros en el RAID (con un mínimo de tres) y solo uno de los discos será utilizado   como «backup», es decir, que solo se desperdiciará la capacidad de uno de ellos.   En RAID 5 se incrementa el rendimiento de lectura del volumen, multiplicando éste por tantos   discos como conformen el RAID menos uno. Es decir, si tuviéramos 5 discos duros en RAID 5, la   velocidad se multiplicaría por 4. Además, tendríamos tolerancia a fallos de un disco: si falla   un disco, no se pierde nada, se cambia el disco y listo.   La parte mala de este sistema RAID de discos duros es que si fallaran dos discos, sí que   tendríamos pérdida de datos. Además, lógicamente como el mínimo son 3 discos, necesitaremos una mayor inversión inicial para hacerlo.&#x20;
* **RAID-6** --> Es una variante del RAID 5 pero que emplea dos discos como backup en lugar de uno, y por lo tanto la velocidad es de n-2, siendo n el total de discos del conjunto. Es un RAID 5 pero un poco más seguro, con mayor gasto en inversión inicial.&#x20;
* **RAID-1+0** -->consiste en hacer primero dos RAID 1 y luego un RAID 0 entre ellos, teniendo así en total 4 discos duros con 2 discos de tolerancia a fallos (uno por cada RAID 1), y en RAID 0 para una mayor velocidad.

## MONTAR UN RAID-5 EN LINUX

{% embed url="https://ajcruz15.gitbook.io/apuntes-linux/apuntes-terminal/07-montaje-de-discos-y-dispositivos" %}
Para más información de los comandos a utilizar, pinche en éste enlace.
{% endembed %}

* `fdisk -l` para ver los discos disponibles.&#x20;
* Creamos las tablas de particiones de los tres discos

```
fdisk /dev/sdb
 n --> nueva tabla de particiones
 generamos 1 particion primaria en cada disco con toda su capacidad
 w --> escribe los cambios.
fdisk /dev/sdc #repetimos el proceso
fdisk /dev/sdd #repetimos el proceso
```

* Instalamos el gestor de sistemas RAID si no lo tenemos: `apt install mdadm`   Opciones del comando: `-C` --> Crea un nuevo array (por defecto se llama /dev/md0) `-l` --> Nivel del Raid (0, 1, 5, 6, 10...) `-n` --> Numero de discos en el Raid `-v` --> verbose `-D` --> muestra los datos del Raid (/dev/md0) `--readwrite` --> permite cambiar el estado a lectura y escritura. `--manage` --> permite configurar el volumen, añadir, retirar discos...  `--add` --> añadimos un disco en espera.  `--remove` &#x20;
* Utilizamos **mdadm** para gestionar el sistema RAID `mdadm -Cv /dev/md0 -l 5 -n 4 /dev/sd{b,c,d,e}1` &#x20;
* Formateamos el volumen creado `mke2fs -t ext4 /dev/md0`&#x20;
* Por ultimo hay que montarlo, por lo que: `mkdir /mnt/raid5 #le doy un lugar caracteristico donde montarlo` `mount /dev/md0 /mnt/raid5` &#x20;
* Añadimos el volumen Raid5 al archivo /etc/fstab para que arranque al inicio. Para ello, utilizamos el UUID para evitar que si el sistema cambie el nombre del disco no inicie el equipo. `blkid #Obtenemos el UUID del disco` `nano /etc/fstab` &#x20;
* Para comprobar el estado del Raid-5 hacemos:  `cat /proc/mdstat.`  En caso de que aparezca como (auto-read-only) tras reiniciar, hay que hacer lo siguiente `--readwrite /dev/md0` &#x20;
* Podemos agregar un disco:\
  `mdadm --manage /dev/md0 --add /dev/sdf1 #este disco queda de repuesto por si alguno se estropea.`
* Podemos simular que un disco falle:\
  `mdadm --manage /dev/md0 --fail /dev/sdb1 #si falla un disco entra automaticamente el disco spare si lo hay.`
* Si tengo un disco fallido, tengo que sacarlo `mdadm --manage /dev/md0 --remove /dev/sdb1`
