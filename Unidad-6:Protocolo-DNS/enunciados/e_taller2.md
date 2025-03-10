# Taller 2: Instalación y configuración de un servidor DNS esclavo

## ¿Qué vas a aprender en este taller?
- Configurar un servidor DNS de respaldo.
- Entender el proceso de transferencia de zona por el que se sincronizan los dos servidores DNS.
- Usar distintos comandos para verificar si la configuración de los servidores DNS es correcta.

## ¿Qué tienes que hacer?

Un servidor DNS esclavo contiene una réplica de las zonas del servidor maestro. Se debe producir una transferencia de zona (el esclavo hace una solicitud de la zona completa al maestro) para que se sincronicen los servidores.

1. Crea otra máquina que tendrá el rol de servidor DNS esclavo. Instala bind9 y nómbralo de manera adecuada para que tenga el nombre `dns2.tunombre.org`. Supongamos que la dirección de esta nueva máquina es `172.22.200.110`. La transferencia de zona entre maestro y esclavo usa el puerto `53/tcp`, ábrelo en el grupo de seguridad.

2. Por seguridad, sólo debemos aceptar transferencias de zonas hacia los esclavos autorizados. Para ello, en el fichero `/etc/bind/named.conf.options`, deshabilitamos las transferencias:

```bash
options {
    ...
    allow-transfer { none; };
    ...
}
```

3. Modificamos la definición de las zonas en el servidor DNS maestro. Modificamos el fichero `/etc/bind/named.conf.local`:

```bash
include "/etc/bind/zones.rfc1918";
zone "tunombre.org" {
    type master;
    file "db.tunombre.org";
    allow-transfer { 172.22.200.110; };
    notify yes;
};
zone "22.172.in-addr.arpa" {
    type master;
    file "db.172.22.0.0";
    allow-transfer { 172.22.200.110; };
    notify yes;
};
```

- `allow-transfer`: Se permite las transferencias de zonas al servidor DNS esclavo (`172.22.200.110`).
- `notify yes`: Cuando se reinicie el servidor DNS maestro, se notificará al esclavo que ha habido cambios para que solicite una transferencia de zona.

4. Modificamos la definición de las zonas en el servidor DNS esclavo. Modificamos el fichero `/etc/bind/named.conf.local`:

```bash
include "/etc/bind/zones.rfc1918";
zone "tunombre.org" {
    type slave;
    file "db.tunombre.org";
    masters { 172.22.200.100; };
};
zone "22.172.in-addr.arpa" {
    type slave;
    file "db.172.22.0.0";
    masters { 172.22.200.100; };
};
```

- `type slave`: Se indica que este servidor será esclavo para estas zonas.
- `masters`: Se indica cuál es el maestro, para saber a qué servidor hay que solicitar la transferencia de zona.

5. En el servidor DNS maestro, añade la información del servidor DNS esclavo en las zonas. En la zona de resolución directa, en el fichero `/var/cache/bind/db.tunombre.org`, añade un nuevo registro `NS` y su correspondiente registro `A`:

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
@	IN	NS		dns2.tunombre.org.
@	IN	MX	10	correo.tunombre.org.

$ORIGIN tunombre.org.

dns1		IN	A	172.22.200.100
dns2		IN	A	172.22.200.110
...
```

La zona de resolución inversa quedaría de la siguiente forma, modificando el fichero `/var/cache/bind/db.172.22.0.0`:

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
@	IN	NS		dns2.tunombre.org.

$ORIGIN 22.172.in-addr.arpa.

 100.200		IN	PTR		dns1.tunombre.org.
 110.200		IN	PTR		dns2.tunombre.org.
...
```

Nota: La información de los registros de las zonas sólo se modifica en el servidor DNS maestro. Estas modificaciones se copian en el esclavo por medio de una transferencia de zona.

6. Reinicia el servidor DNS maestro y esclavo. Puedes ver en el esclavo que se han producido las transferencias de zonas:

```bash
root@dns2:~# systemctl restart bind9
root@dns2:~# journalctl -u named
... dns2 named[5739]: zone 22.172.in-addr.arpa/IN: transferred serial 1
... dns2 named[5739]: transfer of '22.172.in-addr.arpa/IN' from 172.22.200.100#53: Transfer status: success
... dns2 named[5739]: transfer of '22.172.in-addr.arpa/IN' from 172.22.200.100#53: Transfer completed: ...
... dns2 named[5739]: managed-keys-zone: Key 20326 for zone . acceptance timer complete: key now trusted
... dns2 named[5739]: resolver priming query complete
... dns2 named[5739]: zone tunombre.org/IN: Transfer started.
... dns2 named[5739]: transfer of 'tunombre.org/IN' from 172.22.200.100#53: connected using 172.22.200.110#58461
... dns2 named[5739]: zone tunombre.org/IN: transferred serial 1
... dns2 named[5739]: transfer of 'tunombre.org/IN' from 172.22.200.100#53: Transfer status: success
... dns2 named[5739]: transfer of 'tunombre.org/IN' from 172.22.200.100#53: Transfer completed: ...
```

7. Si al reiniciar los servidores DNS tienes algún error, puedes detectar errores de sintaxis usando los siguientes comandos:

```bash
named-checkzone tunombre.org /var/cache/bind/db.tunombre.org
named-checkzone 22.172.in-addr.arpa /var/cache/bind/db.172.22.0.0
```

Para detectar errores de configuración en `named.conf`, podemos usar:

```bash
named-checkconf
```

8. Configura el cliente con los dos servidores DNS. Para ello, en su fichero `/etc/resolv.conf` utiliza dos directivas `nameserver`. Si hacemos una consulta desde un cliente y el DNS maestro no responde, responderá el esclavo. Prueba a realizar una consulta. ¿Quién ha respondido? Apaga el servidor maestro y vuelve a hacer la misma consulta. ¿Ha respondido el servidor DNS esclavo?

9. Vamos a modificar la información de la zona. Para ello, en el servidor DNS maestro, en su fichero `/var/cache/bind/db.tunombre.org`, vamos a añadir un nuevo registro:

```bash
... 
prueba		IN	A	172.22.200.120
```

Recuerda incrementar el número de serie para que, al reiniciar el servidor DNS maestro, se produzca la transferencia de zona.

10. Desde el cliente, realiza una consulta para preguntar por la dirección IP de `prueba.tunombre.org`. ¿Quién ha respondido? Apaga el servidor maestro y vuelve a hacer la misma consulta. ¿Ha respondido el servidor DNS esclavo? Comprueba en el esclavo que se ha producido la transferencia.

11. Algunos trucos adicionales:

- Puedes manejar el servidor DNS con el comando `rndc`. Por ejemplo:
  - `rndc reload`: reinicia el servicio;
  - `rndc reload tunombre.org`: reinicia sólo esa zona;
  - `rndc flush`: borra la caché de resoluciones guardadas en el servidor.

- Realiza una consulta al servidor maestro y al esclavo para comprobar que las respuestas son autorizadas (bit AA). Además, asegúrate que coinciden los números de serie:

```bash
dig +norec @x.x.x.x tunombre.org. soa
```

- Solicita una copia completa de la zona y comprueba que sólo se puede hacer desde el esclavo:

```bash
dig @x.x.x.x tunombre.org. axfr
```

- Cada vez que realices una modificación en el servidor DNS maestro, recuerda incrementar el número de serie.

## ¿Qué tienes que entregar?
1. Realización del apartado 8.
2. Transferencia de zonas: indica para qué sirve el número de serie. Explica con tus palabras qué indican los tiempos que se configuran en el registro SOA.
3. Realización del apartado 10.
4. Realiza el ejercicio que te va a proponer el profesor.
