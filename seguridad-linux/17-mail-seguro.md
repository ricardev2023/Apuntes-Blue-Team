---
description: Securización de clientes de e-mail en nuestro servidor.
---

# 17- MAIL SEGURO

## PROTOCOLOS MAIL

Los protocolos que más se usan en internet – POP3, IMAP y SMTP. Cada uno de ellos tiene su función o forma de trabajar específica. 

### POP3

Post Office Protocol version 3 \(POP3\) es un protocolo standard de correo que se usa para la recepción   
de correo desde un servidor remoto a un cliente de correo local. POP3 te permite descargar los mensajes de correo en tu ordenador local y leerlos cuando estés offline. Ten en cuenta que cuando usas POP3 para conectarte a tu cuenta de correo, los mensajes se descargan localmente y se eliminan del servidor de correo. Esto significa que si accede a tu cuenta desde varias localizaciones o dispositivos, puede que esta no sea la mejor opción. Por otra parte, si usas POP3, tus mensajes se guardan en tu dispositivo local, lo que reduce el espacio que tus correos ocupan en tu servidor web.

Por defecto, el protocolo POP3 trabaja con dos puertos:

    Puerto 110 --&gt; Este es el predeterminado POP3 no cifrado.  
    Puerto 995 --&gt; Este es el puerto que debes usar si quieres conectarte usando POP3 de forma segura \(cifrada\).

### IMAP

Internet Message Access Protocol \(IMAP\) es un protocolo de correo usado para acceder al correo de un   
servidor web remoto desde un cliente local. IMAP y POP3 son los dos protocolos de correo de internet más utilizados para la Recuperación de Correos. Ambos protocolos son compatibles con todos los clientes de correo modernos y servidores web.

Mientras que el protocolo POP3 asume que solo se accede a tu correo desde una única aplicación, IMAP permite el acceso desde varios clientes de correo. Por eso IMAP es el más adecuado si vas a acceder desde diferentes localizaciones o si sus mensajes son gestionados por varios usuario.

Por defecto, el protocolo IMAP trabaja con dos puertos:

    Puerto 143 --&gt; Este es el predeterminado para IMAP no cifrado.  
    Puerto 993 --&gt; Este es el puerto que debes usar si quieres conectarte usando IMAP de forma segura \(cifrada\).

### SMTP

Simple Mail Transfer Protocol \(SMTP\) es un protocolo estándar para el envío de correo de Internet.

Por defecto, el protocolo SMTP trabaja con 3 puertos:

    Port 25   --&gt; Este es el predeterminado para SMTP no cifrado.  
    Port 2525 --&gt; Este puerto está abierto en todos los servidores de SiteGround por si el puerto 25 está   
 bloqueado por parte de tu proveedor de Internet y quieres enviar correo no cifrado por SMTP.  
    Port 465  --&gt; Este es el puerto que debes usar si quieres conectarte usando SMTP de forma segura \(cifrada\).

---------------------------------------------------------------------------------------------------------------

---PROGRAMAS PARA GESTION DE SERVIDORES MAIL---

--POSTFIX--

## REFERENCIAS

[https://soporte.duplika.com/hc/es/articles/360038343012-Protocolos-de-mail-POP3-SMTP-e-IMAP](https://soporte.duplika.com/hc/es/articles/360038343012-Protocolos-de-mail-POP3-SMTP-e-IMAP)  
[https://es.ccm.net/contents/279-protocolos-de-mensajeria-smtp-pop3-e-imap4](https://es.ccm.net/contents/279-protocolos-de-mensajeria-smtp-pop3-e-imap4)

