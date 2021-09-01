---
description: Configuraciń de Firewall con IPTABLES.
---

# 05- FIREWALL

## FIREWALLS DE LINUX

Un firewall es un dispositivo que filtra el tráfico entre redes, como mínimo dos. El firewall puede ser un  
dispositivo físico o un software sobre un sistema operativo. En general debemos verlo como una caja con DOS o mas interfaces de red en la que se establecen una reglas de filtrado con las que se decide si una conexión determinada puede establecerse ono. Incluso puede ir más allá y realizar modificaciones sobre las comunicaciones, como el NAT.

## IPTABLES

Es un firewall que se puede manejar por comandos o generando un script que lo haga automaticamente,  
ya que por comandos, al volver a arrancar el equipo, no se guardan los cambios.

El orden en el que se ponen las reglas de firewall es determinante. Normalmente cuando hay que decidir  
que se hace con un paquete se va comparando con cada regla del firewall hasta que se encuentra una que le afecta \(match\), y se hace lo que dicte esta regla \(aceptar o denegar\); después de eso **NO SE MIRARÁN MÁS REGLAS** para ese paquete.

Por lo tanto, se van poniendo reglas \(limitaciones y permisos\) de abajo arriba, es decir limitamos abajo todo y arriba vamos permitiendo lo que consideramos necesario.

Existen varios tipos de reglas:

* **INPUT** es el trafico entrante \(tablas filter\)
* **OUTPUT** es el trafico saliente \(tablas filter\)
* **FORWARD** es redirigir input a output \(tablas filter\)
* **PREROUTING** desde fuera \(firewall\) hacia dentro \(red\)\(tablas NAT\)
* **POSTROUTING** desde dentro \(red\) hacia fuera \(firewall\) \(tablas NAT\)

### COMANDOS

Las reglas se implementan con los siguientes comandos:

`iptables -t [-A/-C/-D/...] [especificaciones]`  
`-t` --&gt; tabla a la que afecta \(filter por defecto, NAT o mangle\)  
**Argumentos**:  
`-A` --&gt; añade una regla \(INPUT / OUTPUT / FORWARD / PREROUTING / POSTROUTING\)  
`-C` --&gt; comprueba que existe una regla  
`-D` --&gt; elimina una regla \(para trabajar con comandos\)  
`-I` --&gt; inserta una regla en una linea \(para trabajar con comandos\)  
`-R` --&gt; reemplaza una regla por otra \(para trabajar por comandos\)  
`-E` --&gt; renombra una cadena de comandos \(para trabajar por comandos\)  
`-N` --&gt; nueva cadena \(para trabajar por comandos\)  
`-L` --&gt; lista las reglas existentes de filter \(se utiliza con -n siempre\)  
`-S` --&gt; lista las reglas existentes.  
`-F` --&gt; limpia las tablas de reglas.  
`-X` --&gt; elimina las cadenas de comandos  
`-Z` --&gt; limpia las estadisticas  
`-P` --&gt; marca la politica por defecto de una pila \(ACCEPT / DROP\)  
Si es **ACCEPT**, se deben declarar al inicio  
Si es **DROP**, se deben declarar al final.

**Parametros**:  
`-s` --&gt; source \(fuente\) IP o red \(IP + Subred\)  
`-d` --&gt; destiny \(destino\) IP o red \(IP + Subred\)  
`-p` --&gt; protocolo \(TCP / UDP / ICMP\)  
`--dport` --&gt; puerto de destino  
`--sport` --&gt; puerto de origen  
`-j` --&gt; accion  
`ACCEPT` --&gt; acepta la conexion.  
`LOG` --&gt; acepta la conexion pero la loguea en /var/log/syslog.  
`REJECT` --&gt; la deniega y lo loguea en syslog.  
`DROP` --&gt; la deniega.  
`REDIRECT` --&gt; redirecciona trafico entrante de un puerto a otro. PREROUTING \(`--to-port [puerto]`\)  
`MASQUERADE` --&gt; SOLO NAT. las ips salen a internet con la IP publica del router. POSTROUTING.  
`DNAT` --&gt; SOLO NAT. Permite trafico exterior a una IP de la red.`--to [IP:PUERTO] --dport [PUERTO destino]` Comprueba la tabla NAT y si no lo encuentra, lo manda al puerto redirigido.  
`-i` --&gt; interfaz de entrada \(INPUT / FORWARD / PREROUTING\)  
`-o` --&gt; interfaz de salida \(OUTPUT / POSTROUTING\)

### EJEMPLO DE DESPLIEGUE

1- **Generar archivo iptables.sh**

```text
#Esta primera linea permite que el trafico que llega a la tarjeta de red se 
#pueda reenviar
#Activamos el bit de forwarding
echo "1" > /proc/sys/net/ipv4/ip_forward

#limpiamos las tablas filter 
iptables -F
#limpiamos las cadenas realizadas por comandos
iptables -X
#limpiamos las estadisticas
iptables -Z
#limpiamos tabla NAT
iptables -t nat -F

#Comenzamos con las reglas
#Bloqueo internet a toda la red (todo http pero no https).
iptables -A OUTPUT -s 192.168.43.0/24 -p tcp --dport 80 -j DROP

#Abrimos el trafico de entrada https y lo redirigimos a un servidor interno.
#(P.E: servidor web)
iptables -t nat -A PREROUTING -i ens33 -p tcp --dport 443 -j DNAT 
--to 192.168.43.25:443

#bloqueamos el ping de entrada para que no se me descubran desde fuera de mi red
iptables -A INPUT -i ens33 -p icmp -j DROP

#Regla masquerade para que nadie salga a ningun sitio con IP privada:
iptables -t nat -A POSTROUTING -s 192.168.43.0/24 -d 0.0.0.0/0 -j MASQUERADE

#Declaramos las politicas por defecto restrictivas:
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP

#Esta ultima linea guarda los cambios realizados en iptables 
#y lo copia en el archivo para que no se borre cada vez que 
#apaguemos el equipo:
iptables-save > /etc/iptables.up.rules
```

1. **Cambiamos los permisos de iptables.sh**

`chmod 770 iptables.sh`

1. **Para ejecutar el firewall ejecutamos como cualquier script.**

`./iptables.sh`

## UFW FIREWALL

Es un firewall desarrollado para ubuntu, lo que significa que es mas sencillo que el iptables.  
Es muy basico y tiene menos opciones que el iptables. Permite usarlo con servicios en vez de con puertos.

### COMANDOS GENERALES

`ufw enable` --&gt; activa el firewall

`ufw disable` --&gt; desactiva el firewall

`ufw status` --&gt; indica el estado del firewall \(activo/inactivo\)

`ufw status verbose` --&gt; estado pero mas desarrollado.

`ufw status numbered` --&gt; muestra las normas activas numeradas.

`ufw app list` --&gt; muestra las aplicaciones que utilizan puertos.

`ufw app info [nombre de app]` --&gt; muestra un resumen de informacion de la aplicacion con su puerto

### COMANDOS PARA HACER REGLAS:

`ufw default deny incoming` --&gt; deniega trafico de entrada.

`ufw default allow outgoing` --&gt; acepta trafico de salida.

`ufw allow 22` --&gt; permite el trafico del puerto 22.

`ufw allow 25/tcp` --&gt; permite el trafico TCP en el puerto 25.

`ufw logging o`n --&gt; comienza a loguear la actividad de la red.

`ufw allow sshd` --&gt; permite el trafico generado por el protocolo ssh.

`ufw deny pop3` --&gt; deniega el trafico generado por el protocolo pop3.

`ufw delete` --&gt; borra una regla utilizando el numero de norma anterior.

`ufw deny proto tcp from 192.168.1.0/24 to any port 22` --&gt; Denegar el trafico TCP desde toda la red a cualquier IP por el puerto 22.

`ufw allow proto tcp from any to any port 22,80,443` --&gt; Permite el trafico TCP desde cualquier IP a cualquier IP para los puertos 22, 80 y 443.

`ufw reject out to any port 80` --&gt; Reenvio desde el equipo propio a cualquier IP por el puerto 80.

