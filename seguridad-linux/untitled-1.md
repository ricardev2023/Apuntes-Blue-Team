---
description: Introducción al protocolo DHCP y a su configuración en sistemas Linux.
---

# 08- DHCP

## PROTOCOLO DHCP

El protocolo de configuración dinámica de host es un protocolo de red de tipo cliente/servidor mediante  
el cual un servidor DHCP asigna dinámicamente una dirección IP y otros parámetros de configuración de red a cada dispositivo en una red para que puedan comunicarse con otras redes IP.

Este servidor posee una lista de direcciones IP dinámicas y las va asignando a los clientes conforme estas vanquedando libres, sabiendo en todo momento quién ha estado en posesión de esa IP, cuánto tiempo la ha tenido y a quién se la ha asignado después.

Así los clientes de una red IP pueden conseguir sus parámetros de configuración automáticamente.  
En linux existen estos dos paquetes:

`apt-get install dhcp3-server #antiguo`  
`apt-get install isc-dhcp-server #debian 10`

{% hint style="info" %}
Cuidado con los servidores DHCP ya que pueden entrar en conflicto facilmente, por que los router tienen función DHCP.
{% endhint %}

## ARCHIVOS RELEVANTES

**/etc/dhcp/dhcpd.conf** --&gt; Se configura la subred de direcciones que nos va a suministrar el DHCP.

```text
(subred)

subnet [IP de red] netmask [Mascara de red]

{
 range [ip inicio] [ip fin] #rango de IP,s en el que trabajamos.
 option routers [puerta de enlace] #Define la puerta de enlace. 
 option domain-name [ip servidor DNS] #Servidores DNS tanto internos como externos.
 host [nombre equipo] #permite configurar un servidor con IP fija.
 {
 hardware ethernet [MAC del servidor];
 fixed-adress [ip del Servidor]
 option host-name "nombre_servidor"
 }  
}
```

## REFERENCIAS

[https://www.ionos.es/digitalguide/servidores/configuracion/que-es-el-dhcp-y-como-funciona/](https://www.ionos.es/digitalguide/servidores/configuracion/que-es-el-dhcp-y-como-funciona/)

