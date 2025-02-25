# Ejercicio 1: Resolución de nombres de dominios en sistemas Linux

## ¿Qué vas a aprender en este ejercicio?

- Los distintos mecanismos que podemos usar en una distribución Linux para resolver nombres.
- Las distintas utilidades para realizar resoluciones de nombres.

## ¿Qué tienes que hacer?

Crea una máquina virtual con Vagrant usando el box `debian/bookworm64`. Realiza las siguientes tareas:

1. **Instalación de herramientas básicas:**
   - Instala la herramienta `dig` (paquete `dnsutils`).

2. **Verificación de configuración DNS:**
   - Comprueba el servidor DNS que está utilizando el sistema. ¿Qué fichero tienes que comprobar?
   - Identifica los mecanismos de resolución configurados y el orden en el que se van a consultar.  
     - ¿Qué fichero tienes que comprobar?
     - ¿Qué base de datos nos interesa dentro de ese fichero?

3. **Resolución estática:**
   - Añade la siguiente resolución estática: `www.example.org` es la dirección IP `192.168.1.100`.  
     - ¿En qué fichero has configurado esta resolución?
   - Utiliza la herramienta `dig` para resolver `www.example.org` ejecutando:
     ```bash
     dig www.example.org
     ```
   - Analiza la respuesta en la sección `ANSWER SECTION`:
     - ¿Por qué no aparece la dirección que has configurado como resolución estática?
     - ¿Qué servidor DNS ha respondido? (Revisa la penúltima línea que comienza con `;; SERVER`).

4. **Uso de NSS:**
   - Realiza consultas utilizando el sistema NSS.  
     - ¿Qué dirección nos ofrece? ¿Por qué?
   - Quita la resolución estática y vuelve a ejecutar el comando anterior.  
     - ¿Qué dirección nos ofrece ahora? ¿Por qué?

5. **Configuración de resolvconf:**
   - Observa el fichero `/etc/resolv.conf` que se genera en la máquina Vagrant.  
     - Instala el paquete `resolvconf`.
     - Comprueba el fichero `/etc/resolv.conf` nuevamente.  
       - Nota: Si no hay un DNS configurado, reinicia la red para que `resolvconf` haga su trabajo.

6. **Multicast DNS:**
   - Instala `avahi` (paquetes `avahi-daemon` y `avahi-utils`) en tu máquina virtual y en tu anfitriona.
   - Revisa el orden de los mecanismos de resolución en `/etc/nsswitch.conf`.
   - Realiza las siguientes pruebas:
     - Usa la utilidad `getent ahosts` para resolver el nombre de tu anfitriona desde la máquina virtual.  
       - El nombre será `nombre_host.local`.
     - Comprueba la conectividad desde la anfitriona a la máquina virtual utilizando su nombre multicast DNS con `ping`.

7. **Configuración de systemd-resolved:**
   - Instala `systemd-resolved` y comprueba su estado con `systemctl status`.
   - Examina los cambios en los siguientes ficheros:
     - `/etc/nsswitch.conf`: Identifica los nuevos mecanismos de resolución.
     - `/etc/resolv.conf`: ¿Qué configuración DNS se ha configurado?  
       - ¿Es un archivo “stub”?  
       - ¿Es un DNS del sistema?  
       - ¿Se ha mantenido el `resolv.conf` anterior?  
       - Comprueba si el nuevo `/etc/resolv.conf` es un enlace simbólico.
       - ¿Qué es el servidor DNS `127.0.0.53`?
   - Usa `resolvectl` para mostrar la configuración actual de resolución.
   - Añade nuevamente la resolución estática del ejercicio anterior y realiza una consulta a `www.example.org` usando `resolvectl`.

8. **Resolución del nombre local (mecanismo myhostname):**
   - Utiliza `resolvectl` para consultar el nombre del equipo y el nombre `nombre_del_equipo.local`.
     - Nota: Este nombre no necesita estar en `/etc/hosts`.
   - Usa la opción de `resolvectl` para mostrar el servidor DNS en uso.
   - Elimina la resolución estática y realiza nuevamente una consulta a `www.example.org` usando `resolvectl`.

9. **Pruebas finales:**
   - Realiza una consulta con `getent`.  
     - ¿Sigue funcionando este comando? ¿Por qué? Razona tu respuesta.
   - Realiza una consulta con `dig`.  
     - ¿Sigue funcionando este comando? ¿Por qué? Razona tu respuesta.
