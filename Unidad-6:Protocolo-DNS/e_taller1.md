# Taller 1: Instalación y configuración del servidor bind9 en nuestra red local

## ¿Qué vas a aprender en este taller?

- Instalar un servidor DNS bind9.
- Crear una zona de resolución directa en nuestro servidor DNS.
- Crear una zona de resolución inversa en nuestro servidor DNS.
- Realizar consultas sobre el servidor DNS.

## ¿Qué tienes que hacer?

1. **Crear la máquina:**
   Crea una máquina y configúrala para que se llame `dns1.tunombre.org`. Ejecuta el comando `hostname -f` para comprobar que el nombre FQDN se ha configurado correctamente.

2. **Instalar bind9:**
   En esta máquina vamos a instalar un servidor DNS `bind9`. De principio no es necesario realizar ninguna configuración para que el servidor funcione como servidor recursivo/caché. Sin embargo, haremos una pequeña configuración:

   - Para evitar que se intente resolver usando la dirección IPv6 de los servidores DNS, modifica la opción `OPTION` en el archivo de configuración `/etc/default/named` con las siguientes opciones:

     ```bash
     OPTIONS="-4 -f -u bind"
     ```

   - El servidor `bind9` sólo permitirá consultas desde la misma red local. Si, por ejemplo, estamos en casa y con la VPN queremos hacer una consulta, la consulta se realizaría desde otra red. En este caso, en el archivo `/etc/bind/named.conf.options`, configura el parámetro `allow-query`:

     ```bash
     allow-query {172.201.0.0/16; 172.22.0.0/16;};
     ```

   - Desactiva, en el mismo archivo, la validación DNSSEC para proteger el servidor. Cambia la siguiente directiva:

     ```bash
     dnssec-validation no;
     ```

   - Reinicia el servidor DNS.

3. **Realiza la consulta:**
   Ejecuta la siguiente consulta desde tu ordenador:

   ```bash
   dig @<IP de tu servidor DNS> www.josedomingo.org
   ```

   - ¿Cuánto ha tardado en realizar la consulta? 
   - ¿Qué consultas se han realizado para averiguar la dirección IP?
   - Realiza de nuevo la consulta. ¿Cuánto ha tardado ahora? ¿Por qué ha tardado menos? ¿Qué consultas se han realizado para averiguar la dirección IP?

4. **Crear una zona directa:**
   Vamos a crear una zona directa para el dominio `tunombre.org`. Para ello, añade en el archivo `/etc/bind/named.conf.local` la definición de la zona:

   ```bash
   zone "tunombre.org" {
       type master;
       file "db.tunombre.org";
   };
   ```

   - Es una zona de tipo maestra.
   - La información de la zona se guardará en el archivo `db.tunombre.org` que estará en `/var/cache/bind9`.
   - Crea el archivo de zona en `/var/cache/bind/db.tunombre.org`. El contenido de este archivo debería ser algo similar a:

     ```bash
     $TTL    86400
     @       IN      SOA     dns1.tunombre.org. root.tunombre.org. (
                                 1         ; Serial
                            604800         ; Refresh
                             86400         ; Retry
                           2419200         ; Expire
                             86400 )       ; Negative Cache TTL
     ;
     @	IN	NS		dns1.tunombre.org.
     @	IN	MX	10	correo.tunombre.org.

     $ORIGIN tunombre.org.

     dns1			IN	A	172.22.XX.XX
     correo			IN	A	172.22.200.101
     asterix		IN	A	172.22.200.102
     obelix			IN	A	172.22.200.103
     www			IN	CNAME	asterix
     informatica		IN	CNAME	asterix
     ftp			IN	CNAME	obelix
     ```

   - Modifica la zona para que el servidor DNS tenga la IP real de tu máquina.

5. **Crear una zona inversa:**
   Vamos a crear una zona inversa para la red `172.22.0.0/16`. El nombre de la zona será `22.172.in-addr.arpa`. Añade la definición de la zona en el archivo `/etc/bind/named.conf.local`:

   ```bash
   zone "22.172.in-addr.arpa" {
       type master;
       file "db.172.22.0.0";
   };
   ```

   - Descomenta la línea `include "/etc/bind/zones.rfc1918";` para incluir todas las zonas correspondientes a redes privadas.
   - Modifica el archivo `/var/cache/bind/db.172.22.0.0` con el siguiente contenido:

     ```bash
     $TTL    86400
     @       IN      SOA     dns1.tunombre.org. root.tunombre.org. (
                                 1         ; Serial
                            604800         ; Refresh
                             86400         ; Retry
                           2419200         ; Expire
                             86400 )       ; Negative Cache TTL
     ;
     @	IN	NS		dns1.tunombre.org.

     $ORIGIN 22.172.in-addr.arpa.

     XX.XX			IN	PTR		dns1.tunombre.org.
     101.200		IN	PTR		correo.tunombre.org.
     102.200		IN 	PTR		asterix.tunombre.org.
     103.200		IN 	PTR		obelix.tunombre.org.
     ```

6. **Reiniciar el servicio:**
   Reinicia el servicio de `bind9` para que los cambios tengan efecto.

7. **Configurar el DNS en otra máquina:**
   Configura el nuevo servidor DNS en otra máquina y realiza las consultas con `dig`.

### ¿Qué tienes que entregar?

1. Realiza una consulta con `dig` a tu servidor para averiguar la IP de `www.marca.es`. Contesta las siguientes preguntas:
   - ¿Cuánto ha tardado en realizar la consulta?
   - ¿Qué consultas se han realizado para averiguar la dirección IP?

2. Realiza de nuevo la consulta. ¿Cuánto ha tardado ahora? ¿Por qué ha tardado menos? ¿Qué consultas se han realizado para averiguar la dirección IP?

3. El resultado de las siguientes consultas a tu servidor DNS desde otra máquina:
   - Dirección IP de una máquina o servicio de tu dominio.
   - Servidor DNS con autoridad del dominio.
   - Servidor de correo del dominio.
   - Una resolución inversa.

Finalmente, avisa al profesor y enséñale el servidor DNS funcionando.
