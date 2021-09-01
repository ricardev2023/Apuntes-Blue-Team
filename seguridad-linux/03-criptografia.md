---
description: Introducción a la criptografía.
---

# 03- CRIPTOGRAFÍA

## CONCEPTOS BASICOS DE CRIPTOGRAFIA

### TIPOS DE CIFRADO

* Simetrico
* Asimetrico
* SSH

### FICHEROS IMPORTANTES

**/home/strelock/.gnupg** --&gt; Directorio oculto donde se guardan las claves publicas y privadas. Cada usuario tiene su carpeta y root igual.

/home/strelock/.ssh --&gt; Directorio oculto donde se guardan las claves publicas de los hosts que se pueden conectar por SSH a cada usuario. Cada usuario tiene su carpeta y root igual.

## TIPOS DE CIFRADOS

### GPG

Vamos a utilizar la aplicacion **gpg** para generar claves criptograficas.  
Esta aplicacion esta basada en **pgp** que es **de pago**.

Aplicacion por defecto en Linux para manipular y generar claves criptograficas.  
**Por defecto genera una rsa 3072** \(la mas segura del mercado actualmente\)  
Tiene una serie de opciones.

 `--gen-key` --&gt; Genera una clave.  
 Esta generacion necesita que el sistema este trabajando a altas prestaciones para conseguir que la  
 generacion aleatoria de caracteres funcione correctamente.

 `--list-key / -k` --&gt; me permite listar las claves generadas.

 `--list-secret-key` --&gt; lista las privadas

 `--list-public-key` --&gt; lista las publicas

 `--encrypt [archivo]` --&gt; encripta un archivo utilizando la clave publica de un/varios usuarios.

 `--decrypt [archivo]` --&gt; desencripta un archivo si encuentra la clave privada que necesita.

 `--clearsign [archivo]` --&gt; firma el archivo con mis datos.

 `--verify [archivo]` --&gt; para comprobar la firma de un archivo.

 `--armor [archivo]` --&gt; permite sacar en ASCII la exportacion. \(-a\)

## COMANDOS

**SCP** --&gt; manda por SSH un archivo de un equipo a otro:  
 Es parecido a un servicio FTP pero la transmisión es cifrado.  
`-r` --&gt; si quiero descargar todo el contenido de una carpeta

 P.E:   
`scp [ruta origen] [usuario_destino@ip_destino:ruta destino]` --&gt; subir un archivo`scp [usuario_origen@ip_origen:ruta_origen] [ruta destino]` --&gt; desargar un archivo

