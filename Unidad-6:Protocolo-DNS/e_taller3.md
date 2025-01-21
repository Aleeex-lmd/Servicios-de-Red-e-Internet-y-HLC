# Taller 3: Delegación de subdominios con bind9

## ¿Qué vas a aprender en este taller?
- Entender el concepto de delegación de dominio.
- Configurar un servidor DNS para delegar un subdominio en otro servidor DNS.
- Realizar consultas a los nombres del dominio delegado.

## ¿Qué tienes que hacer?
1. **Crear una nueva máquina** e instalar un servidor DNS `bind9`, que será el servidor DNS con autoridad para la zona delegada `informatica.tunombre.org`. Nombra esta máquina como `dns.informatica.tunombre.org`, y supón que la dirección de esta máquina es `172.22.200.120`.

2. **Servidor DNS principal**: Tendremos el servidor `dns1.tunombre.org` (y el esclavo `dns2.tunombre.org`) con autoridad para la zona `tunombre.org`. En esta zona, por ejemplo, puede existir el nombre `www.tunombre.org`.

3. **Servidor DNS delegado**: Tendremos el servidor `dns.informatica.tunombre.org` con autoridad sobre el subdominio delegado, es decir, tendrá autoridad para la zona `informatica.tunombre.org`. En esta zona, por ejemplo, puede existir el nombre `www.informatica.tunombre.org`.

### Delegación de autoridad en el servidor DNS principal
Modifica el fichero `/var/cache/bind/db.tunombre.org` para añadir las siguientes líneas y realizar la delegación:

```plaintext
$ORIGIN informatica.tunombre.org.
@	IN	NS		dns
dns	IN	A	172.22.200.120
```

El parámetro `$ORIGIN` permite usar nombres no cualificados completamente. Esto indica que el servidor con autoridad para la zona `informatica.tunombre.org` es `dns.informatica.tunombre.org`, y se indica la dirección IP de este servidor.

### Configuración del servidor DNS delegado
En el servidor `dns.informatica.tunombre.org`, crea una nueva zona en el fichero `/etc/bind/named.conf.local`:

```plaintext
zone "informatica.tunombre.org" {
    type master;
    file "db.informatica.tunombre.org";
};
```

Y crea la zona en el fichero `db.informatica.tunombre.org`:

```plaintext
$TTL    86400
@       IN      SOA     dns.informatica.tunombre.org. root.informatica.tunombre.org. (
                               1         ; Serial
                          604800         ; Refresh
                           86400         ; Retry
                         2419200         ; Expire
                           86400 )       ; Negative Cache TTL
 ;
@	IN	NS		dns.informatica.tunombre.org.
@	IN	MX	10	mail.informatica.tunombre.org.

$ORIGIN informatica.tunombre.org.

dns			IN	A		172.22.200.120
mail			IN	A		172.22.200.121
web			IN	A		172.22.200.122
www			IN	CNAME		web
```

### Verificación de consultas
No modifiques el fichero `/etc/resolv.conf` del cliente. Las consultas se hacen al servidor DNS principal, y cuando preguntemos por un nombre en la zona delegada, el servidor DNS principal preguntará al servidor DNS delegado y guardará la respuesta en su caché.

1. Realiza una consulta para la dirección IP del nombre `www.informatica.tunombre.org`. ¿Quién ha respondido?
   
2. Realiza una consulta para averiguar el servidor DNS con autoridad para la zona del dominio `informatica.tunombre.org`. ¿Es el mismo que el servidor DNS con autoridad para la zona `tunombre.org`?

3. Realiza una consulta para averiguar el servidor de correo configurado para `informatica.tunombre.org`.

## ¿Qué tienes que entregar?
1. Una captura de pantalla donde se vea la consulta para averiguar la dirección IP de `www.informatica.tunombre.org`.
2. Una captura de pantalla donde se vea la consulta para averiguar el servidor DNS con autoridad para la zona del dominio `informatica.tunombre.org`. ¿Es el mismo que el servidor DNS con autoridad para la zona `tunombre.org`?
3. Una captura de pantalla donde se vea la consulta para averiguar el servidor de correo configurado para `informatica.tunombre.org`.
4. Realiza el ejercicio que te va a proponer el profesor.
