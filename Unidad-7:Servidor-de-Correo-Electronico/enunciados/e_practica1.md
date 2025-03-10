# Práctica (1 / 2): Instalación y configuración de un servidor de correos en el VPS
Instala y configura de manera adecuada el servidor de correos en tu VPS. El nombre del servidor de correo será `nompre_vps.tudominio.algo` (este es el nombre que deberá aparecer en el registro MX).

## Gestión de correos desde el servidor
El envío y recepción se hará desde el servidor usando la herramienta `mail`. Debes realizar las siguientes tareas:

- Configura el registro SPF en tu DNS para autorizar el envío de correos desde tu VPS. Muestra la configuración del registro SPF en tu DNS.

- Configura de manera adecuada DKIM es tu sistema de correos. Comprueba el registro DKIM en la página DKIM Record Lookup.

- Configura de manera adecuada el registro DMARC. Muestra la configuración del registro que has realizado. Utiliza la página DMARC Check Tool para verificar que el registro es correcto.
- Realiza un envío de correo desde tu servidor al exterior.

- Envía un correo desde el exterior (gmail, hotmail,…) a tu servidor local.

- Vamos a configurar distintas mediadas para luchar contra el SPAM: Configura de manera adecuada Postfix para que tenga en cuenta el registro SPF de los correos que recibes (no hace falta configurar la comprobación de DKIM porque se hace automáticamente).

## Entrega

1. Realiza una consulta con `dig` para mostrar la configuración de tu registro SPF.

2. Envía un correo desde tu servidor a la dirección de correo que te indica la página Learn and Test DMARC y envía una captura de pantalla de la comprobación de que tu configuración SPF, DKIM y DMARC funcionan (**PASS**).

3. Muestra el log donde se demuestre el envío de correo. Una captura de pantalla donde se vea que el correo ha llegado. Y una comprobación en las cabeceras del correo que muestre que el receptor ha comprobado tus registros SPF, DKIM y DMARC.

4. Muestra el log donde se vea la recpción del correo desde el exterior. Muestra cómo has leído el correo. Muestra el registro MX de tu dominio con una consulta con `dig`.

5. Muestra el log del correo para comprobar que se está haciendo el testeo del registro SPF del correo que recibes.