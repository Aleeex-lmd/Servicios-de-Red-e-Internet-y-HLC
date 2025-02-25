# Servidor de correos

## Instalación Postfix

```sh
apt install postfix
```

**Tipo de servidor:** Internet Site (vamos a recibir y enviar correo directamente)
**Mailname:** DOMINIO
**Fichero de configuración:** `/etc/postfix/main.cf`

## Caso 1: Envío local, entre usuarios del mismo servidor

Instalamos el cliente de correos `mail`:

```sh
sudo apt install bsd-mailx
```

**Envío de correo:**
```sh
usuario1@maquina:~$ mail usuario2@localhost
```

**Leer correo:**
```sh
usuario2@maquina:~$ mail
```

El buzón del usuario está en `/var/mail/usuario2`. Los correos leídos se guardan en `~/mbox`.
Podemos comprobar el log para verificar el envío del mensaje:

```sh
sudo journalctl -fu postfix@-
```

## Caso 2: Envío de correo desde usuarios del servidor a correos de internet

### Desde el aula

En el fichero de configuración:
```ini
relayhost = mail.gonzalonazareno.org
```

### Desde tu servidor VPS

Con la configuración básica del servidor, podemos enviar correos sin un relay.

Se recomienda configurar el registro MX en nuestro DNS apuntando a nuestra máquina.
Para evitar que los correos sean rechazados, implementamos:
- **SPF**
- **DKIM**
- **DMARC**

Si la IP es dinámica, los servicios como Gmail, Hotmail o Yahoo pueden rechazar el correo.

## Caso 3: Recibir correos desde internet a usuarios del servidor

### Desde el aula

Ejemplo de correo: `usuario@dominio_de_cada_alumno`, e.g. `jose@josedom.gonzalonazareno.org`.

Si queremos recibir correos desde internet, nuestros dominios deben apuntar a nuestra IP pública:

```dns
* IN CNAME macaco.gonzalonazareno.org.
```

El administrador de `macaco` añade nuestro correo en `relay_domains` y configura el DNS correctamente.

Configuramos un registro MX en nuestro DNS:
```dns
luffy.loquesea.gonzalonazareno.org.
```

El correo llega a `luffy` y se reenvía a `samji` mediante un DNAT del puerto `25/TCP`.

### Desde tu VPS

Asegurar que en `/etc/mailname` esté el dominio correcto.
Configurar DNS con el registro MX apuntando a nuestra IP pública.
Verificar autenticidad de correos entrantes mediante SPF, DKIM y DMARC.

## Caso 4: Recepción de correo electrónico usando nuestro servidor de correos

### Protocolos para recibir correos:
- **POP3**: Descarga todos los correos.
- **IMAP**: Sincroniza el estado de los correos.

Usaremos **IMAP**.

### Tipos de buzones:
- **mbox**: Todos los mensajes en un único archivo.
- **maildir**: Mensajes guardados en una carpeta (requerido para IMAP).

#### Configuración de Maildir:
```ini
home_mailbox = Maildir/
mailbox_command =
```

Instalamos el servidor IMAP `dovecot`:
```sh
apt-get install dovecot-imapd
```

Se recomienda cifrar la comunicación con un certificado de Let's Encrypt.

## Caso 5: Envío de correo electrónico usando nuestro servidor de correos

Modificar en el fichero de configuración:
```ini
mynetworks = 0.0.0.0/0
```

Si los clientes están en internet, la conexión debe ser autenticada y cifrada.

### Métodos de cifrado:

#### **ESMTP + STARTTLS**
- Puerto `587/TCP` (Submission)
- Autenticación con **SASL** (Dovecot)
- Cifrado con **STARTTLS**

#### **SMTPS**
- Puerto `465/TCP`
- Cifrado directo (similar a HTTPS)

### Clientes web recomendados:
- Roundcube
- Horde
- Rainloop
- SquirrelMail

---
### **Enlaces de referencia**
- [ISP Mail Tutorial for Debian 10 (Buster)](https://workaround.org/ispmail/buster/)
- [How to secure Postfix using Let’s Encrypt](https://www.linode.com/docs/guides/postfix-smtp-debian10/)

