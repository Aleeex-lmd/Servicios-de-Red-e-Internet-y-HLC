# Ejercicio 2: Consultas DNS con dig

## ¿Qué vas a aprender en este ejercicio?

- Usar el comando `dig` para realizar consultas DNS.
- Aprender a hacer consultas DNS para obtener información sobre el servidor DNS.
- Consultar diferentes tipos de registros: NS, A, CNAME, MX, PTR.

## ¿Qué tienes que hacer?

El comando `dig` permite hacer consultas a un servidor DNS desde la línea de comandos. La sintaxis es:

```bash
dig [tipo de registro] [@servidor DNS] Consulta DNS
```

Por defecto, el tipo de registro es `A` y el servidor DNS por defecto es el definido en `/etc/resolv.conf`.

**Nota**: Si el comando `dig` no funciona, instala el paquete `dnsutils`, que lo incluye.

Realiza las siguientes consultas utilizando el comando `dig` (recuerda que la respuesta estará en la sección `ANSWER SECTION`):

### 1. Dirección IP de los siguientes dominios:

- `www.gonzalonazareno.org`
- `www.debian.org`

### 2. Servidores con autoridad de los dominios:

- Dominio raíz
- `es`
- `gonzalonazareno.org`

### 3. Servidores de correo de los dominios:

- `gonzalonazareno.org`
- `us.es`

### 4. Tipo de registro que resuelve los siguientes dominios:

- `www.josedomingo.org`
- `informatica.gonzalonazareno.org`

### 5. Pregunta sobre resolución inversa:

- En clase, ¿a qué nombre corresponde la dirección IP `172.22.0.1`?