---
description: Introducción al sistema DNS y a su configuración en sistemas Linux.
---

# 09- DNS

## SISTEMA DNS

DNS son las iniciales de Domain Name System \(sistema de nombres de dominio\) y es una tecnología basada en una base de datos que sirve para resolver nombres en las redes, es decir, para conocer la dirección IP de la máquina donde está alojado el dominio al que queremos acceder.

Por lo tanto, el DNS es un sistema que sirve para traducir los nombres en la red, y está compuesto por   
tres partes con funciones bien diferenciadas:

* **Cliente DNS**: está instalado en el cliente \(es decir, nosotros\) y realiza peticiones de resolución de  nombres a los servidores DNS. 
* **Servidor DNS**: son los que contestan las peticiones y resuelven los nombres mediante un sistema estructurado en árbol. Las direcciones DNS que ponemos en la configuración de la conexión, son las direcciones de los Servidores DNS. 
* **Zonas de autoridad**: son servidores o grupos de ellos que tienen asignados resolver un conjunto de dominios determinado \(como los .es o los .org\).

La dirección completa de dominio se llama **FQDN** \(www.dominio.com\) --&gt; \(HOST.DOMINIO.TLD\)

En un servidor DNS hay dos zonas:

* **Zona directa:** Permite encontrar IP dando dominio.
* **Zona inversa:** Permite encontrar dominio dando IP.

Se puede poner un DNS **master** y otro **slave** \(esclavo\) en el que se replica el master por si falla.

Se puede montar un **DNS interno** descargando el siguiente paquete:

 `apt-get install bind9 #es el mas conocido`

## ARCHIVOS RELEVANTES

**/etc/bind/named.conf.options** --&gt; Archivo donde se configuran las opciones del DNS.

`listen-on-port 53 {127.0.0.1, 192.168.0.18;}; #Escucha en puerto 53 con la IP de host y del servidor.`

 `allow-query {192.168.0.1/24} #Que maquinas pueden utilizar el servicio DNS`

 `allow-transfer {192.168.1.25;}; #Permite la replicacion en el servidor esclavo (192.168.1.25)`

 `forwarders {IP} #IP de un DNS externo que si no se encuentra en el sistema, mande la solicitud al exterior.` 

```text
#Estructura de un archivo de configuración DNS.

//Zona Directa --> Zona en la que se resuelven dominios a IP.
 Zone "empresa.local" {
 type master;
 file "/etc/bind/db.empresa.local.host";
 notify yes;
 };
 
 //Zona Inversa --> Zona en la que se resuelven IP a dominio.
 Zone "1.168.192.in-addr.arpa"{
 type master;
 file "/etc/bind/db.192.168.1.rev";
 notify yes;
 };
```

## TIPOS DE REGISTROS

Los registros DNS son diferentes cadenas de letras que se utilizan para indicar ciertas acciones al servidor DNS. Estas letras también son conocidas como sintaxis de DNS. A continuación, veremos una lista de las diferentes sintaxis de DNS que existen:

* **A**: Hace referencia a la dirección IPv4 de un servidor web.
* **AAAA**: Hace referencia a la dirección IPv6 de un host. 
* **CNAME**: Hace referencia a un alias de otro dominio. Es decir, su función es hacer que un dominio sea un alias de otro dominio. Normalmente este tipo de registros se utilizan para asociar nuevos subdominios con dominios ya existentes del registro A.
* **MX**: Hace referencia a una lista de servidor de intercambio de correo que se debe utilizar para el dominio.
* **PTR**: Hace referencia a un punto de terminación de red. Es decir, que la sintaxis de DNS es la responsable del mapeo de una dirección IPv4 para el CNAME en el alojamiento.
* **NS**: Hace referencia a que servidor de nombres es el autorizado para el dominio.
* **SOA**: Hace referencia al comienzo de autoridad. Este registro es uno de los registros DNS más importantes porque guarda información esencial como la fecha de la última actualización del dominio, otros cambios y actividades.
* **SRV**: Hace referencia a un servicio. Es decir, se utiliza para la definición de un servicio TCP en el que opere en el dominio.
* **TXT**: Hace referencia a un texto. Es decir, permite que los administradores inserten texto en el registro DNS. Esto se utiliza para dejar notas sobre información del dominio.
* **SPF**: Hace referencia a qué servidores están autorizados para enviar correos electrónicos con nuestro dominio.
* **LOC**: Hace referencia a la ubicación física del servidor. Es decir, este tipo de registros se utilizan para indicar la latitud, longitud y altura sobre el nivel de mar de la ubicación física del servidor.
* **MB**: Hace referencia al nombre del dominio de correo electrónico. Actualmente este registro es experimental \(Diciembre 2019\).
* **MG**: Hace referencia a los miembros de un grupo de correo electrónico. Actualmente este registro es experimental \(Diciembre 2019\).
* **MR**: Hace referencia al renombre de un dominio de correo electrónico. Actualmente este registro es experimental \(Diciembre 2019\).
* **NULL**: Hace referencia a recurso nulo. Actualmente este registro es experimental \(Diciembre 2019\).
* **HINFO**: Hace referencia a los detalles sobre el hardware y el software del host.
* **MINFO**: Hace referencia a la información sobre un buzón de correo electrónico. Actualmente este registro es experimental. \(Diciembre 2019\).
* **RP**: Hace referencia a información sobre los encargados del dominio.
* **ANY**: Hace referencia a toda la información de todos los tipos que exista.
* **AFSDB**: Está pensado especialmente para clientes AFS.
* **NAPTR**: Hace referencia a una ampliación del registro A que permite usar patrones de búsqueda.
* **KX**: Hace referencia a Key Exchanger y permite gestionar claves criptográficas.
* **CERT**: Este registro guarda certificados.
* **DNAME**: Este indica alias para dominios enteros.
* **OPT**: Hace referencia a un pseudo registro del ámbito de los mecanismos de extensión de DNS más conocido como EDNS.
* **APL**: Hace referencia a “Address Prefix List” y sirve para enumerar rangos de direcciones en formato CIDR.
* **DS**: Hace referencia a “Delegation Signer” y sirve para identificar zonas con firma DNSSEC.
* **SSHFP**: Hace referencia a “SSH Public Key Fingerprint” y muestra la huella digital para las claves SSH.
* **IPSECKEY**: Este contiene una clave para IPsec.
* **RRSIG**: Este alberga una firma digital para DNSSEC.
* **NSEC**: Este interconecta zonas firmadas en DNSSEC.
* **DNSKEY** **DNS**: Este contiene una clave pública para DNSSEC.
* **DHCID**: Este enlaza nombres de dominio con clientes DHCP.
* **TLSA**: Este registro establece un enlace conocido como TLSA con un nombre de dominio.
* **SMIMEA**: Este registro establece un enlace conocido como S/MIME con un nombre de dominio.
* **CDS**: Este es una copia de un registro DS.
* **CDNSKEY**:Este es una copia de un registro DNSKEY.
* **OPENPGPKEY**: Este muestra claves públicas.
* **TKEY**: Este permite el intercambio de claves secretas.
* **TSIG**: Este sirve para la autenticación.
* **URI**: Este muestra la asignación de nombres de host a las URL.
* **CAA**: Este especifica las posibles autoridades de certificación \(CA\) para un dominio.

## REFERENCIAS

[https://www.ionos.es/digitalguide/servidores/know-how/que-es-el-servidor-dns-y-como-funciona/](https://www.ionos.es/digitalguide/servidores/know-how/que-es-el-servidor-dns-y-como-funciona/)  
[https://www.redeszone.net/tutoriales/internet/que-es-registros-dns/](https://www.redeszone.net/tutoriales/internet/que-es-registros-dns/)

